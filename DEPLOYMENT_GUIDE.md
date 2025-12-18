# Deployment Guide (AI-Oriented)

## 1. Introduction

This guide provides you, the AI agent, with the step-by-step instructions for deploying and configuring the Subscription Management Micro-SaaS platform.

## 2. Vercel Deployment

1.  **Project Setup:**
    *   Create a new project on Vercel and connect it to the GitHub repository.
    *   Set the build command to `npm run build` and the output directory to `build`.
2.  **Environment Variables:**
    *   You must configure the following environment variables in the Vercel project settings:
        *   `SUPABASE_URL`: The URL of your Supabase project.
        *   `SUPABASE_ANON_KEY`: The anonymous key for your Supabase project.
        *   `ZOHO_CLIENT_ID`: The client ID for your Zoho API application.
        *   `ZOHO_CLIENT_SECRET`: The client secret for your Zoho API application.

## 3. Supabase Configuration

1.  **Project Creation:**
    *   Create a new project on Supabase.
    *   Save the project URL and anonymous key.
2.  **Database Migrations:**
    *   You must apply the database migrations located in the `supabase/migrations` directory using the Supabase CLI:
        ```bash
        supabase db push
        ```
3.  **Row-Level Security (RLS):**
    *   Ensure that RLS is enabled on all tables that contain user data.

## 4. Zoho Integration

1.  **Application Creation:**
    *   Create a new API application in the Zoho Developer Console.
    *   Note the client ID and client secret.
2.  **Credentials Storage:**
    *   You must store the Zoho client ID and client secret as environment variables in Vercel, not in the codebase.

## 5. Email Integration

### 5.1. Google Cloud (Gmail)

1.  **Project Setup:**
    *   Create a new project in the Google Cloud Console.
    *   Enable the Gmail API and the Google Pub/Sub API.
2.  **OAuth Credentials:**
    *   Create an OAuth 2.0 client ID and secret.
    *   Configure the authorized redirect URIs to point to your Vercel deployment.

### 5.2. Azure AD (Outlook)

1.  **App Registration:**
    *   Register a new application in Azure Active Directory.
    *   Grant the `Mail.Read` permission.
2.  **Credentials:**
    *   Create a new client secret.

## 6. End-to-End Deployment Sequence

You must follow this sequence for a new deployment:

1.  Create and configure the Supabase project.
2.  Create the necessary API credentials for Zoho, Google, and Azure.
3.  Deploy the application to Vercel, configuring all required environment variables.
4.  Run the database migrations.
5.  Perform smoke tests to verify that the deployment was successful.
