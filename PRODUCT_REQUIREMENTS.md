# Product Requirements Document (PRD)

**Version:** 1.4  
**Last Updated:** December 26, 2025

## 1. Introduction

### 1.1. Platform Strategy: Mobile-First

This platform will be delivered across three primary frontends: a responsive web application, a native iOS application, and a native Android application. The project scope includes the development of all three. **The strategic focus is mobile-first.** We anticipate the majority of user interactions will occur on mobile devices, and as such, the native mobile apps are considered the primary user experience.

### 1.2. Problem Statement

In the modern digital economy, consumers and small businesses are increasingly reliant on subscription-based services. This proliferation has led to "subscription fatigue," a state of cognitive overload where users lose track of their recurring payments. A major barrier to solving this is the high friction of onboarding: users are reluctant to link bank accounts due to privacy concerns, and manual entry is tedious.

### 1.3. Product Vision & Goal

Our vision is to create the indispensable **control cockpit for the subscription economy**, accessible seamlessly across web and mobile. The primary goal is to empower users to move from a state of passive spending to one of active, informed control. We will achieve this by offering multiple, privacy-first onboarding paths that deliver instant value.

## 2. Guiding Principles

*(This section remains the same as v1.3)*

## 3. Core Features & Functionality

### 3.1. Frictionless Onboarding: Statement Upload & Wallet Integration

*(This section remains the same as v1.3)*

### 3.2. Payment Method Intelligence

This feature explicitly links subscriptions to the payment methods that fund them, providing critical financial clarity.

*   **Description:** Users can add and manage their payment methods, including credit/debit cards and digital wallets (e.g., PayPal, connected via OAuth). Each payment method will have a user-defined nickname for easy identification (e.g., "Personal Visa," "Business Amex").
*   **Mandatory Linking:** Every subscription **must** be linked to a specific payment method. This is a core data requirement.
*   **Filtering & Insights:** The UI **must** allow users to filter their subscription list by payment method. This enables users to instantly see all subscriptions tied to a specific card or wallet, which is crucial for managing card expirations, cancellations, or understanding financial exposure.
*   **User Benefit:** Understand financial exposure at a card-level, easily manage subscriptions when a card is lost or expires, and avoid the surprise and inconvenience of failed payments.

### 3.3. Subscription Registry

*(This section remains the same as v1.3, with the implicit requirement that `payment_method_id` is a mandatory field.)*

*(Other core features remain the same)*

## 4. Non-Functional Requirements

| Category | Requirement |
| :--- | :--- |
| **Platforms** | The system will consist of a backend API, a responsive web app, a native iOS app, and a native Android app. All features must be available across all platforms. |
| **Performance** | API response times for typical user actions must be under 200ms. Statement parsing should complete within 15 seconds. Mobile app startup time should be under 2 seconds. |
| **Security** | Uploaded statements must be processed in a secure, isolated environment and deleted immediately after processing. All other security requirements from v1.3 apply. |

*(Other non-functional requirements remain the same)*

## 5. Success Metrics

*   **Mobile Adoption:** At least 70% of Monthly Active Users (MAU) should be on the native iOS or Android apps.
*   **Import Adoption Rate:** At least 50% of new users attempt to use the statement upload feature during onboarding.
*   **Activation Rate:** % of new users who add at least 3 subscriptions within the first week.
*   **Retention & Satisfaction:** High retention rates and a Net Promoter Score (NPS) of 40+ across all platforms.

## 6. Acceptance Criteria

*   **Payment Method Filtering:** A user can select a payment method (e.g., "Personal Visa") and see a filtered list containing only the subscriptions linked to that method.
*   **Mobile Experience:** The native iOS and Android apps provide a smooth, intuitive, and feature-complete experience.
*   **Statement Upload:** A user can successfully upload a common bank statement (PDF and CSV), and the system correctly identifies at least 80% of the recurring payments.
*   **Simple UX:** A new user can get a populated dashboard via statement upload in under 60 seconds on any platform.
