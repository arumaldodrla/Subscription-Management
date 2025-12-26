# Mobile App Development Guide

**Version:** 1.0  
**Last Updated:** December 26, 2025

## 1. Introduction & Mobile-First Strategy

This document provides the complete technical specification for developing the native iOS and Android applications for the Subscription Management platform. The platform strategy is **mobile-first**, meaning the native mobile apps are considered the primary and most important user experience. As an AI agent, you must prioritize the development and performance of the mobile applications.

## 2. Technology Stack

To maximize code reuse and development velocity, we will use a cross-platform native development framework.

*   **Framework:** **React Native**
*   **Platform:** **Expo** (Managed Workflow)
*   **Language:** TypeScript
*   **Backend Communication:** Supabase Client Library (`@supabase/supabase-js`)
*   **UI Components:** A combination of native-base or a similar component library, adapted to our design system.

**Rationale:**

*   **React Native** allows us to leverage existing web development skills (React) to build truly native applications.
*   **Expo (Managed Workflow)** abstracts away the complexity of native build tooling (Xcode, Android Studio), allowing AI agents to focus on writing application code. It also provides a robust set of pre-built native APIs (camera, push notifications, etc.) and simplifies the process of building and deploying to the app stores.

## 3. Project Structure & Code Sharing

To promote code sharing between the web and mobile apps, we will use a monorepo structure (e.g., using `pnpm` workspaces).

```
/ (root)
├── packages/
│   ├── ui/             # Shared UI components (agnostic)
│   ├── common/         # Shared business logic, types, API calls
├── apps/
│   ├── mobile/         # Expo (React Native) project
│   └── web/            # Vite (React) project
```

*   **`packages/ui`:** Will contain simple, headless UI components that can be styled for both web and mobile.
*   **`packages/common`:** This is the most critical shared package. It will contain all Supabase client logic, data fetching hooks, type definitions (e.g., `Subscription`, `PaymentMethod`), and business logic. The mobile and web apps will both import from this package.

## 4. Backend Interaction

The mobile apps will communicate with the **exact same Supabase backend** as the web application. The `@supabase/supabase-js` library will be used for all interactions:

*   **Authentication:** The mobile app will use the same Supabase Auth endpoints. Secure storage on the device (e.g., Expo's `SecureStore`) will be used to persist the user's session (JWT).
*   **Database:** All data will be fetched and manipulated via the Supabase PostgREST API, respecting all Row-Level Security (RLS) policies.
*   **Real-time:** The mobile app will use Supabase Realtime to subscribe to database changes for a reactive user experience.

## 5. Native-Specific Features

The mobile apps must leverage native device capabilities to provide a superior experience.

*   **Push Notifications:** Use Expo's Push Notifications API to send reminders for trial expirations and upcoming renewals, even when the app is closed.
*   **Biometric Authentication:** Allow users to log in using Face ID or Touch ID for a seamless and secure experience.
*   **Offline Support (Future Iteration):** While not in v1, the architecture should be mindful of future requirements for basic offline support, such as caching the subscription list.

## 6. Build & Deployment

Expo Application Services (EAS) will be used to build and deploy the applications.

*   **EAS Build:** This service will build the native `.ipa` (iOS) and `.apk`/`.aab` (Android) files in the cloud, without requiring a local macOS machine for iOS builds.
*   **EAS Submit:** This service will manage the process of submitting the application binaries to the Apple App Store and Google Play Store.
*   **Over-the-Air (OTA) Updates:** We will heavily leverage EAS Update to push minor bug fixes and UI tweaks directly to users without requiring a full app store review. This is ideal for the autonomous self-maintenance cycle.

## 7. AI Agent Development Workflow

1.  **Task Assignment:** A task is assigned to an AI agent (e.g., "Add filtering by payment method to the mobile app").
2.  **Code Implementation:** The AI agent will primarily work within the `apps/mobile` and `packages/common` directories.
3.  **Testing:** The AI agent will use the Expo Go app on a simulator or physical device to test changes in real-time.
4.  **Pull Request:** The AI agent creates a pull request. The CI/CD pipeline will run tests and linting.
5.  **Review & Merge:** The PR is reviewed and merged.
6.  **Deployment:**
    *   For minor changes, an AI agent can trigger an **EAS Update** to push the change OTA.
    *   For major changes that require a new native build, a human will trigger an **EAS Build** and **EAS Submit** process.
