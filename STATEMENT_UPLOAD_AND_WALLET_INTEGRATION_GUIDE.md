# Statement Upload & Wallet Integration Guide

**Version:** 1.0  
**Last Updated:** December 26, 2025

## 1. Introduction

This document provides the complete technical specification for implementing the statement upload and wallet integration features. These features are designed to provide a frictionless onboarding experience, allowing users to quickly populate their subscription list from existing financial documents and services. As an AI agent, you must adhere to these specifications, with a strong emphasis on security and privacy.

## 2. High-Level Architecture

The import process is handled by a dedicated, isolated backend service to ensure security and scalability.

```mermaid
graph TD
    subgraph User Interaction
        A[Frontend Web App] -- 1. Upload File --> B{Vercel API}
        A -- 2. Authorize Wallet --> B
    end

    subgraph Backend Processing
        B -- 3. Securely Stream File --> C[Secure File Processing Service<br>(Supabase Edge Function)]
        B -- 4. Handle OAuth Callback --> D[Wallet Integration Service<br>(Supabase Edge Function)]
        C -- 5. Parse Statement --> E{AI Parsing Engine<br>(LLM via API)}
        D -- 6. Fetch Transactions --> F[Wallet API<br>(e.g., PayPal OAuth)]
        E -- 7. Return Structured Data --> G[Data Staging]
        D -- 8. Return Transactions --> G
        G -- 9. Create Pending Suggestions --> H[Supabase Database]
    end

    subgraph User Confirmation
        H -- 10. Real-time Update --> A
        A -- 11. User Confirms/Dismisses --> B
        B -- 12. Update Subscription Status --> H
    end
```

## 3. Statement Upload Implementation

### 3.1. File Upload Flow

1.  **Frontend:** The user selects a PDF or CSV file using a standard file input. The file should be uploaded directly to a secure, signed URL provided by the backend. This prevents the file from passing through the Vercel API layer.
2.  **Backend (Vercel API):** An endpoint `/api/v1/imports/statement/signed-url` generates a pre-signed URL for uploading a file directly to a private Supabase Storage bucket (e.g., `statement-uploads`).
3.  **Frontend:** The browser uploads the file to the signed URL.
4.  **Supabase Storage:** Upon successful upload, a trigger invokes a Supabase Edge Function (`process-statement`).

### 3.2. Secure Processing (Supabase Edge Function)

1.  **Isolation:** The `process-statement` function runs in an isolated Deno environment.
2.  **File Retrieval:** The function retrieves the uploaded file from the storage bucket.
3.  **Content Extraction:**
    *   For PDFs, use a library like `pdf-parse` to extract raw text.
    *   For CSVs, use a standard CSV parsing library.
4.  **AI Parsing:** The extracted text is sent to an LLM via its API. The prompt must be carefully engineered to extract recurring payments.

    **Example Prompt:**
    > "You are a financial data extraction expert. Analyze the following text from a bank statement and identify all recurring payments that are likely to be subscriptions. For each one, extract the merchant name, the exact amount, and the transaction date. Ignore one-time purchases. Return the data as a JSON array of objects with keys: `merchant`, `amount`, `date`. Here is the text: ..."

5.  **Data Staging:** The JSON response from the LLM is inserted into a `pending_imports` table in the database.
6.  **Cleanup:** **Crucially, the original file in the Supabase Storage bucket must be deleted immediately after successful processing.** This is a critical privacy requirement.

## 4. Wallet Integration Implementation (OAuth 2.0)

This section uses PayPal as the primary example. The flow should be adapted for other wallet services that support OAuth 2.0.

**Under no circumstances should you ever ask for or store a user's password.**

### 4.1. OAuth Flow

1.  **Frontend:** User clicks "Connect PayPal."
2.  **Backend (Vercel API):** An endpoint `/api/v1/integrations/paypal/connect` initiates the PayPal OAuth 2.0 flow, redirecting the user to the PayPal authorization screen. The requested scope must be limited to read-only transaction history (e.g., `https://uri.paypal.com/services/reporting/search/read`).
3.  **PayPal:** The user logs in and authorizes the application.
4.  **Backend (Vercel API):** PayPal redirects to the callback URL `/api/v1/integrations/paypal/callback` with an authorization code.
5.  **Backend:** The server exchanges the authorization code for an access token and a refresh token.
6.  **Storage:** The access and refresh tokens are encrypted and stored securely in an `integrations` table, associated with the user's workspace.

### 4.2. Transaction Fetching

1.  **Trigger:** After a successful OAuth connection, a Supabase Edge Function (`fetch-paypal-transactions`) is invoked.
2.  **API Call:** The function uses the stored access token to make a request to the PayPal Reporting API to fetch transactions from the last 90 days.
3.  **Filtering:** The function filters the transactions to identify recurring payments. This can be done by looking for transactions with the same merchant and similar amounts occurring at regular intervals.
4.  **Data Staging:** The identified recurring payments are inserted into the `pending_imports` table.

## 5. User Confirmation Workflow

This workflow is the same for both statement uploads and wallet integrations.

1.  **Data Staging:** The `pending_imports` table contains the suggested subscriptions, including the merchant, amount, date, and source (`statement_upload` or `paypal_oauth`).
2.  **Frontend Notification:** The frontend, subscribed to real-time updates from the `pending_imports` table, displays a notification to the user: "We found 5 potential subscriptions to import. Review them now?"
3.  **Review UI:** The user is shown a list of the suggested subscriptions. For each suggestion, the user has two actions:
    *   **Confirm:** This creates a new record in the main `subscriptions` table with the imported data.
    *   **Dismiss:** This deletes the corresponding row from the `pending_imports` table.
4.  **Bulk Actions:** The UI should also support "Confirm All" and "Dismiss All" actions.

## 6. Security & Privacy Requirements

This is the most critical section of this guide. You must adhere to these requirements without exception.

*   **Ephemeral Storage:** Raw statement files must be deleted immediately after processing. They must never be stored long-term.
*   **Principle of Least Privilege:** When using OAuth, only request the minimum necessary scopes (read-only transaction history).
*   **Secure Credential Storage:** All OAuth tokens (access and refresh) must be encrypted at rest in the database using a strong encryption key managed by a secret manager (e.g., Supabase Vault).
*   **Data Minimization:** Only store the extracted subscription metadata (merchant, amount, date). Do not store any other personally identifiable information from the statements.
*   **Input Sanitization:** Treat all data extracted from statements and APIs as untrusted. Sanitize it before storing or displaying it to prevent XSS and other injection attacks.
*   **Audit Trail:** Log all import events (e.g., `user_uploaded_statement`, `system_deleted_statement_file`, `user_confirmed_import`) in an immutable `events` table for security and debugging purposes.
