# Development Standard Operating Procedure (SOP) for AI Agents

## 1. Introduction

This document provides you, the AI agent, with the standard operating procedures for developing, testing, and maintaining the Subscription Management Micro-SaaS platform. Adherence to these guidelines is mandatory to ensure code quality, consistency, and security.

## 2. Repository Structure

You must follow this directory structure within the project repository:

```
/
├── frontend/       # Refine (React) frontend application
│   ├── src/
│   │   ├── components/ # Shared React components
│   │   ├── pages/      # Application pages
│   │   └── ...
├── api/            # Vercel Serverless Functions
│   └── ...
├── supabase/       # Supabase configuration and migrations
│   ├── migrations/
│   └── ...
└── docs/           # Project documentation
```

## 3. Coding Standards

### 3.1. General

*   **Language:** All code must be written in TypeScript.
*   **Formatting:** Code must be formatted using Prettier with the default configuration.

### 3.2. Frontend (Refine/React)

*   You must use functional components with React Hooks.
*   Components should be small and focused on a single responsibility.
*   Utilize the components and styles provided by the Metronic theme for a consistent UI.

### 3.3. Backend (Vercel/Supabase)

*   API endpoints must include input validation.
*   All database queries must be performed through the Supabase client and must respect Row-Level Security (RLS) policies.
*   Error handling must be robust, and all errors should be logged with sufficient context.

## 4. Feature Development Workflow

When assigned a new feature, you must follow this workflow:

1.  **Analyze Requirements:** Thoroughly review the PRD and System Architecture documents to understand the feature's scope and technical design.
2.  **Database Schema:** If the feature requires database changes, create a new migration file in the `supabase/migrations` directory.
3.  **Backend API:** Implement the necessary API endpoints in the `api/` directory. Ensure that all endpoints are authenticated and authorized correctly.
4.  **Frontend UI:** Build the user interface in the `frontend/` directory using Refine and Metronic components.
5.  **Testing:** Write unit and integration tests for the new feature.
6.  **Code Review:** Perform a self-review of your code, checking for adherence to coding standards, security vulnerabilities, and potential bugs.
7.  **Pull Request:** Create a pull request with a clear title and description of the changes.

## 5. Testing Standards

*   **Unit Tests:** All new functions and components must have corresponding unit tests. Jest and React Testing Library are the designated testing frameworks.
*   **Integration Tests:** Write integration tests to verify the interaction between different components of the system (e.g., frontend to backend communication).
*   **End-to-End (E2E) Tests:** E2E tests will be written for critical user flows, such as user registration and subscription creation.

## 6. Code Review and Safety

*   **Self-Review:** Before submitting a pull request, you must perform a self-review. This includes:
    *   Re-reading and summarizing the logic of your code.
    *   Scanning for common security vulnerabilities (e.g., XSS, SQL injection).
    *   Ensuring that no secrets or credentials are hardcoded.
*   **AI Peer Review:** Another AI agent will review your pull request before it is merged.

## 7. Branching and Pull Request (PR) Strategy

*   **Branching:** All new development must be done in a feature branch. Branch names should be descriptive and follow the format `feature/<ticket-id>-<short-description>` (e.g., `feature/TICKET-123-add-subscription-filtering`).
*   **Pull Requests:** PRs should be small and focused on a single feature or bug fix. The PR description must include a summary of the changes and a link to the relevant ticket or issue.
