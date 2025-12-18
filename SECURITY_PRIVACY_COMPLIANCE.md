# Security, Privacy & Compliance Blueprint

## 1. Introduction

This document outlines the security, privacy, and compliance controls for the Subscription Management Micro-SaaS platform. It is designed to be a practical guide for AI agents to ensure the system is built and operated in accordance with ISO 27001, SOC 2, and GDPR.

## 2. Security Controls (ISO 27001-Style)

### 2.1. Information Security Policies

*   **A.5.1:** A formal set of information security policies will be maintained and reviewed annually.

### 2.2. Access Control

*   **A.9.1:** Access to the production environment is restricted to authorized personnel and AI agents.
*   **A.9.2:** User access management will be implemented using Supabase Auth, with role-based access control (RBAC) for workspaces.
*   **A.9.4:** The principle of least privilege will be enforced for all user and system accounts.

### 2.3. Cryptography

*   **A.10.1:** All data will be encrypted in transit using TLS 1.2 or higher. Data at rest will be encrypted using Supabase's built-in encryption features.

### 2.4. Operations Security

*   **A.12.1:** Change management procedures will be followed for all changes to the production environment.
*   **A.12.4:** Logging and monitoring will be implemented to detect and respond to security incidents.

## 3. SOC 2 Principles

*   **Security:** The system will be protected against unauthorized access, use, or modification.
*   **Availability:** The system will be available for operation and use as committed or agreed.
*   **Processing Integrity:** System processing will be complete, valid, accurate, timely, and authorized.
*   **Confidentiality:** Information designated as confidential will be protected as committed or agreed.
*   **Privacy:** Personal information will be collected, used, retained, disclosed, and disposed of in conformity with the commitments in the entity's privacy notice.

## 4. GDPR Compliance

### 4.1. Lawful Basis for Processing

*   The lawful basis for processing user data is the performance of a contract (the terms of service) and consent for optional features like email scanning.

### 4.2. Data Minimization

*   The system will only collect and process personal data that is necessary for the provision of the service.
*   For email integration, only derived metadata will be stored, not the full email content.

### 4.3. Data Subject Rights

*   Users will have the right to access, rectify, and erase their personal data.
*   A data export feature will be provided to allow users to download their data in a machine-readable format.

## 5. Payments and Sensitive Data Handling

*   The platform is not in scope for PCI-DSS, as all payment processing is handled by Zoho Billing and Authorize.net.
*   The system will not store, process, or transmit any raw credit card data.

## 6. Email Integration-Specific Protections

*   **OAuth Scopes:** The system will request the minimum necessary OAuth scopes for email integration (e.g., `gmail.readonly`).
*   **Token Storage:** OAuth tokens and app-specific passwords will be encrypted and securely stored in the Supabase database.

## 7. Logging and Monitoring

*   Logs will not contain any sensitive personal data or secrets.
*   Logs will be retained for a maximum of 30 days.

## 8. Explicit Security Requirements for AI Agents

*   **You, the AI agent, must never log secrets or credentials.**
*   **You must never hardcode secrets or credentials in the source code.**
*   **You must always follow the principle of least privilege when defining IAM roles, API scopes, or RLS policies.**
