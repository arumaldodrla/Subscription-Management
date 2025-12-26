# Product Requirements Document (PRD)

**Version:** 1.3  
**Last Updated:** December 26, 2025

## 1. Introduction

### 1.1. Problem Statement

In the modern digital economy, consumers and small businesses are increasingly reliant on subscription-based services. This proliferation has led to "subscription fatigue," a state of cognitive overload where users lose track of their recurring payments. A major barrier to solving this is the high friction of onboarding: users are reluctant to link bank accounts due to privacy concerns, and manual entry is tedious.

### 1.2. Product Vision & Goal

Our vision is to create the indispensable **control cockpit for the subscription economy**. The primary goal is to empower users to move from a state of passive spending to one of active, informed control. We will achieve this by offering multiple, privacy-first onboarding paths that deliver instant value.

## 2. Guiding Principles

*(This section remains the same as v1.2)*

## 3. Target Audience and Personas

*(This section remains the same as v1.2)*

## 4. Core Features & Functionality

### 4.1. Frictionless Onboarding: Statement Upload & Wallet Integration

This is the primary method for new users to populate their subscription list instantly.

*   **Description:** Users can upload a credit card or bank statement in PDF or CSV format. An AI-powered parsing service will process the document, identify recurring payments, and suggest them as potential subscriptions. The system will also support connecting to services like PayPal via a secure OAuth 2.0 flow to import transaction history.
*   **Workflow:**
    1.  User uploads a file or authorizes a wallet connection.
    2.  The backend service parses the data and identifies potential subscriptions.
    3.  The user is presented with a list of suggested subscriptions (e.g., "Netflix - $15.99/mo").
    4.  The user can approve or dismiss each suggestion with a single click.
*   **User Benefit:** Achieve a comprehensive subscription inventory in under a minute, without manual data entry or sharing sensitive bank login credentials.

### 4.2. Subscription Registry

*(This section remains the same as v1.2)*

### 4.3. Email-Based Trial Detection

*(This section remains the same as v1.2)*

*(Other core features remain the same)*

## 5. Non-Functional Requirements

| Category | Requirement |
| :--- | :--- |
| **Performance** | API response times for typical user actions must be under 200ms. Statement parsing should complete within 15 seconds. |
| **Security** | Uploaded statements must be processed in a secure, isolated environment and deleted immediately after processing. Only extracted, non-sensitive metadata may be stored. |
| **Privacy** | The platform will never store raw statement files. The OAuth integration with wallets must only request read-only access to transaction history. |
| **Internationalization (i18n)** | The statement parser must be trained to handle date and currency formats from different regions (e.g., DD/MM/YYYY vs. MM/DD/YYYY). |

*(Other non-functional requirements remain the same)*

## 6. Success Metrics

*   **Import Adoption Rate:** At least 50% of new users attempt to use the statement upload feature during onboarding.
*   **Import Success Rate:** At least 80% of uploaded statements are successfully parsed with at least 3 suggested subscriptions.
*   **Activation Rate:** % of new users who add at least 3 subscriptions within the first week.
*   **Retention & Satisfaction:** High retention rates and a Net Promoter Score (NPS) of 40+.

## 7. Acceptance Criteria

*   **Statement Upload:** A user can successfully upload a common bank statement (PDF and CSV), and the system correctly identifies at least 80% of the recurring payments.
*   **Wallet Integration:** A user can securely connect their PayPal account via OAuth and import their transaction history.
*   **Simple UX:** A new user can get a populated dashboard via statement upload in under 60 seconds.
*   **Privacy:** No raw statement files are retained on the server after processing is complete.
