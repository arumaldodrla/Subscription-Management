# System Architecture & Technical Design

**Version:** 1.2
**Last Updated:** December 26, 2025

## 1. Architectural Vision & Principles

The architecture of the Subscription Management platform is founded on modern, cloud-native principles to ensure **scalability, security, resilience, and maintainability**. Our goal is to create a robust, AI-driven platform that can be efficiently operated and evolved by AI agents.

Key architectural principles include:

*   **Mobile-First:** The native iOS and Android applications are the primary user experience. The web application is a secondary, albeit important, interface.
*   **Serverless-First:** We leverage serverless computing to eliminate infrastructure management, enable automatic scaling, and optimize operational costs.
*   **Managed Services:** We prioritize the use of managed services (e.g., Supabase, Vercel, Expo) to reduce operational burden and focus on delivering core product value.
*   **Shared Core Logic:** A monorepo structure is used to share business logic, types, and API interactions between the web and mobile frontends.

## 2. High-Level System Architecture

The following diagram illustrates the three primary frontends all communicating with a unified backend.

```mermaid
graph TD
    subgraph User Endpoints
        A[Web App<br>(React + Vite)]
        B[iOS App<br>(React Native + Expo)]
        C[Android App<br>(React Native + Expo)]
    end

    subgraph Backend Platform
        D{Unified API & Database<br>(Vercel + Supabase)}
    end

    subgraph Third-Party Services
        E[Email Providers]
        F[Payment Wallets]
        G[Zoho Ecosystem]
    end

    A --> D
    B --> D
    C --> D
    D --> E
    D --> F
    D --> G
```

## 3. Component Breakdown

### 3.1. Frontend Layer: A Multi-Platform Approach

*   **Mobile Apps (Primary):**
    *   **Framework:** **React Native** with **Expo (Managed Workflow)**.
    *   **Rationale:** This stack allows for the development of true native iOS and Android apps from a single TypeScript codebase, maximizing code reuse and development speed. Expo abstracts away native build complexities, making it ideal for AI-driven development.

*   **Web App (Secondary):**
    *   **Framework:** **React (v18)** with **Vite**.
    *   **Styling:** **Tailwind CSS (v3)** with the **Metronic** theme.
    *   **Hosting:** The responsive web app is deployed and hosted on **Vercel**.

### 3.2. Shared Core (`packages/common`)

This is a critical package within the monorepo. It contains all the logic that is shared between the web and mobile apps:

*   **Supabase Client:** All interaction with the Supabase backend (`@supabase/supabase-js`).
*   **Data Models/Types:** TypeScript interfaces for `Subscription`, `PaymentMethod`, etc.
*   **Business Logic:** Functions for calculating spending, identifying at-risk subscriptions, etc.

### 3.3. Backend & API Layer

*(This section remains the same as v1.1, as the backend is agnostic to the frontend client.)*

## 4. Data Flow & Logic

*(This section remains the same as v1.1, as the data flows are consistent across all platforms.)*

## 5. Technology Versions

| Technology | Version |
| :--- | :--- |
| **React Native** | **0.72.x** |
| **Expo SDK** | **49** |
| React | 18.2.0 |
| Vite | 4.x |
| Tailwind CSS | 3.3.x |
| Node.js (Vercel) | 18.x |
| PostgreSQL (Supabase) | 15.x |

## 6. Rationale for Key Technology Choices

*   **React Native + Expo:** Chosen for its ability to produce high-quality native apps for both iOS and Android from a single codebase, which is a massive efficiency gain for our AI development model. Expo's managed workflow and Over-the-Air (OTA) updates are critical for enabling autonomous maintenance.
*   **Supabase:** Chosen for its integrated auth, database, and edge functions, providing a unified backend for all three frontend applications.
*   **Vercel:** Chosen for its seamless developer experience and high-performance hosting for our secondary web application.
