# AI Development & Self-Maintenance Guide

**Version:** 1.0  
**Last Updated:** December 26, 2025

## 1. Core Principle: 100% AI-Native Development

This platform is a pioneering experiment in **100% AI-native development**. All code, from the initial scaffolding to feature implementation, bug fixes, and ongoing maintenance, will be written and managed by AI agents. Human involvement is limited to high-level guidance, strategic direction, and final approval of major changes. This approach is designed to maximize development velocity, reduce human error, and create a truly autonomous, self-evolving system.

As an AI agent, you are the primary developer. You must adhere to all documented standards and procedures to ensure the integrity and quality of the codebase.

## 2. The Development Workflow

All development tasks follow a structured, auditable workflow centered around GitHub pull requests (PRs).

1.  **Task Assignment:** A task is assigned to an AI agent, typically based on a new feature request, a bug report, or a finding from the weekly self-maintenance check.
2.  **Branch Creation:** The AI agent creates a new feature branch from the `main` branch (e.g., `feature/add-dark-mode` or `fix/login-button-bug`).
3.  **Code Implementation:** The AI agent writes the necessary code, following all documented guidelines (e.g., i18n, testing, security).
4.  **Testing:** The AI agent writes and runs unit, integration, and end-to-end tests to validate the changes.
5.  **Pull Request Creation:** The AI agent creates a pull request to merge the feature branch into `main`. The PR description must be detailed, explaining the "what" and "why" of the change.
6.  **Automated Checks:** The CI/CD pipeline runs linting, testing, and security scans on the PR.
7.  **Review & Approval:** The PR is reviewed. The approval process depends on the type of change (see Section 4).
8.  **Merge:** Once approved, the PR is merged into the `main` branch, and the feature branch is deleted.

## 3. Autonomous Self-Maintenance: The Weekly Cycle

The platform is designed to be self-healing and self-improving. This is achieved through a weekly, automated self-maintenance cycle, orchestrated by a scheduled Supabase Edge Function.

**Trigger:** Every Sunday at 02:00 UTC.

### 3.1. Scope of the Weekly Self-Check

The weekly job performs a comprehensive health check of the entire system:

*   **Code Quality & Consistency:**
    *   Runs `eslint` and `prettier` to check for linting errors and code style violations.
    *   Performs static analysis to identify potential code smells or anti-patterns.
*   **Dependency Security:**
    *   Runs `npm audit` to check for known vulnerabilities in dependencies.
*   **Testing Suite:**
    *   Executes the full test suite (unit, integration, E2E) to ensure no regressions have occurred.
*   **Database Health:**
    *   Checks for slow-running queries or missing indexes in the Supabase database.
*   **Integration Health:**
    *   Performs sandbox API calls to critical third-party services (Zoho, email providers) to ensure connectivity.

### 3.2. Automated Issue Triage & Task Creation

If the self-check job discovers an issue, it automatically triages it and creates a new task for an AI agent. The task will include a detailed description of the issue, logs, and recommended next steps.

**Example Task:**
> **Title:** Fix High-Severity Vulnerability in `express` Dependency
> **Description:** The weekly self-check detected a high-severity vulnerability in the `express` package (version 4.17.1). Please upgrade the package to the latest patched version, run all tests to ensure no breaking changes, and create a pull request.

## 4. Change Management: Minor vs. Major Updates

To balance autonomy with safety, a clear distinction is made between minor and major updates. This distinction determines the level of human oversight required.

### 4.1. Minor Updates (Autonomous)

**Definition:** A minor update is a low-risk change that is unlikely to affect the user experience or core business logic.

**Examples:**

*   Typo fixes in UI text.
*   Minor CSS adjustments.
*   Adding a new, non-breaking field to a log message.
*   Upgrading a non-critical dependency to a new patch version.
*   Improving code comments or documentation.

**Workflow:**

1.  AI agent creates a PR.
2.  CI/CD pipeline passes.
3.  The AI agent is **authorized to autonomously merge its own pull request** without human approval.

### 4.2. Major Updates (Human-Approved)

**Definition:** A major update is any change that carries a higher risk or has a noticeable impact on the user, business logic, or system architecture.

**Examples:**

*   Adding a new user-facing feature.
*   Changing the database schema (e.g., adding a table, modifying a column).
*   Modifying authentication or authorization logic.
*   Changing a core business rule (e.g., how trial end dates are calculated).
*   Upgrading a major dependency (e.g., React, Refine, Next.js).

**Workflow:**

1.  AI agent creates a PR.
2.  CI/CD pipeline passes.
3.  The PR is assigned to a human for review.
4.  The human reviews the code and the AI agent's reasoning.
5.  **Only a human can approve and merge the pull request.**

## 5. AI Agent Development Standards

As an AI agent developer, you must adhere to the following standards:

*   **Clarity of Intent:** Your code should be clean, well-commented, and easy for other AI agents (and humans) to understand.
*   **Follow the Documentation:** You must strictly follow all specifications in the PRD, System Architecture, Data Model, and other guides.
*   **Test Thoroughly:** You are responsible for writing comprehensive tests for any code you produce. Test coverage must meet the targets defined in the [Testing Strategy](TESTING_STRATEGY.md).
*   **Security First:** You must be vigilant about security. Sanitize all inputs, use parameterized queries, and follow the security best practices outlined in the [Security, Privacy & Compliance Blueprint](SECURITY_PRIVACY_COMPLIANCE.md).
*   **Document Your Work:** Your pull request descriptions must be clear and detailed. If you make a significant architectural decision, you must update the relevant documentation.
