# Subscription Management Micro-SaaS - Technical Documentation

## Overview

This repository contains the complete technical documentation for the **Subscription Management Micro-SaaS** application. This documentation is specifically designed to be consumed and executed by **AI agents** working within Google Antigravity to design, build, deploy, operate, and continuously evolve the platform.

The Subscription Management Micro-SaaS is a focused platform that helps individuals, families, and small teams gain complete visibility and control over their recurring subscription payments. The platform features intelligent email-based trial detection, payment method intelligence, cost optimization recommendations, and AI-first operations.

## Product Vision

Become the **"control cockpit" for all your subscriptions** – a single app to track, manage, and optimize every recurring payment in your life. The platform eliminates surprises (no more forgotten renewals or expired cards) and saves users money by intelligently suggesting optimizations, all while maintaining a delightful, cross-platform user experience.

## Technology Stack

*   **Frontend:** Refine (React) + Tailwind CSS + Metronic Theme
*   **Backend:** Vercel Serverless Functions + Supabase (Postgres, Auth, Edge Functions)
*   **Integrations:** Zoho Billing, Zoho Books, Zoho CRM, Zoho Desk, Authorize.net
*   **Email Providers:** Gmail API, Microsoft Graph, iCloud IMAP
*   **Development:** Google Antigravity (AI-first IDE)
*   **Deployment:** Vercel (frontend/API) + Supabase (database/backend)

## Documentation Index

### Core Documentation

1.  **[Product Requirements Document (PRD)](PRODUCT_REQUIREMENTS.md)**
    *   Problem statement and goals
    *   Target users and personas
    *   Feature specifications
    *   Non-functional requirements
    *   Acceptance criteria

2.  **[System Architecture & Technical Design](SYSTEM_ARCHITECTURE.md)**
    *   High-level architecture diagram
    *   Component breakdown
    *   Entity-relationship diagram
    *   Technology stack details

3.  **[Data Model and Schema Specification](DATA_MODEL_SCHEMA.md)**
    *   Complete database schema
    *   Table definitions with columns and constraints
    *   Row-Level Security (RLS) policies
    *   Indexes and performance optimization
    *   Migration strategy

4.  **[API Specification](API_SPECIFICATION.md)**
    *   RESTful API endpoints
    *   Authentication and authorization
    *   Request/response formats
    *   Error handling
    *   Rate limiting

### Feature Implementation Guides

5.  **[Email Trial Detection Implementation Guide](EMAIL_TRIAL_DETECTION.md)**
    *   Gmail, Outlook, and iCloud integration
    *   OAuth flows and authentication
    *   Email parsing logic and trial detection
    *   User confirmation workflow
    *   Privacy and data retention

6.  **[Zoho Integration Guide](ZOHO_INTEGRATION_GUIDE.md)**
    *   Zoho Billing, Books, CRM, and Desk integration
    *   OAuth authentication and token management
    *   Customer and subscription sync
    *   Webhook handling
    *   Error handling and rate limiting

7.  **[Frontend Development Guide: Refine + Metronic](FRONTEND_DEVELOPMENT_GUIDE.md)**
    *   Refine framework setup
    *   Metronic theme integration
    *   Component architecture
    *   Page implementations
    *   State management and authentication

### Development and Operations

8.  **[Development SOP (Standard Operating Procedure) for AI Agents](DEVELOPMENT_SOP.md)**
    *   Repository structure
    *   Coding standards
    *   Feature development workflow
    *   Testing requirements
    *   Code review and PR strategy

9.  **[Development & Enhancement Guide (AI-First)](DEVELOPMENT_ENHANCEMENT_GUIDE.md)**
    *   PRD evolution and updates
    *   Database schema evolution
    *   Feature flags and safe rollouts
    *   Self-maintenance procedures
    *   Performance optimization

10. **[Testing Strategy](TESTING_STRATEGY.md)**
    *   Testing pyramid and coverage requirements
    *   Unit, integration, and E2E testing
    *   Test frameworks and tools
    *   CI/CD pipeline configuration
    *   Security and performance testing

11. **[Deployment Guide (AI-Oriented)](DEPLOYMENT_GUIDE.md)**
    *   Vercel deployment configuration
    *   Supabase setup and migrations
    *   Zoho and email provider setup
    *   Environment variables
    *   End-to-end deployment sequence

12. **[Monitoring and Observability Guide](MONITORING_OBSERVABILITY.md)**
    *   Key metrics and KPIs
    *   Logging strategy and best practices
    *   Error tracking with Sentry
    *   Uptime monitoring
    *   Alerting rules and dashboards

### Security and Compliance

13. **[Security, Privacy & Compliance Blueprint](SECURITY_PRIVACY_COMPLIANCE.md)**
    *   ISO 27001 security controls
    *   SOC 2 principles
    *   GDPR compliance requirements
    *   Payment data handling
    *   Email integration security
    *   AI agent security requirements

### Support and Operations

14. **[AI Support & Zoho Desk Integration Playbook](AI_SUPPORT_ZOHO_PLAYBOOK.md)**
    *   AI chatbot scope and capabilities
    *   Escalation rules and procedures
    *   Zoho Desk ticket creation
    *   UX guidelines
    *   Safety constraints

15. **[Incident Response Runbook](INCIDENT_RESPONSE_RUNBOOK.md)**
    *   Incident detection and analysis
    *   Response phases and procedures
    *   Incident classification
    *   Escalation procedures
    *   Post-incident activities

## Key Features

### Core Functionality

*   **Subscription Registry:** Central repository for all subscriptions with comprehensive metadata
*   **Payment Method Intelligence:** Explicit mapping of subscriptions to payment methods with expiry tracking
*   **Email-Based Trial Detection:** Automatic detection and import of subscription trials from Gmail, Outlook, and iCloud
*   **Reminders & Notifications:** Proactive alerts for renewals, trial expirations, and card expiries
*   **Analytics Dashboard:** Spending insights, optimization suggestions, and at-risk subscription identification
*   **Calendar Integration:** Visual calendar view with ICS export for external calendar apps
*   **Household/Team Sharing:** Multi-user workspaces with role-based access control

### AI-First Operations

*   **Autonomous Development:** All code generation, testing, and deployment handled by AI agents
*   **AI-Powered Support:** In-app chatbot for user assistance with escalation to Zoho Desk
*   **Self-Maintenance:** Automated weekly health checks, security scans, and optimization
*   **Intelligent Monitoring:** AI-driven log analysis and incident detection

## Regulatory and Security Requirements

The platform is architected to support:

*   **ISO 27001:** Information security management controls
*   **SOC 2:** Security, availability, confidentiality, processing integrity, and privacy
*   **GDPR:** Data protection, user rights, data minimization, and lawful processing
*   **PCI Compliance:** Payment processing via Zoho Billing and Authorize.net (no raw card data stored)

## Getting Started for AI Agents

If you are an AI agent tasked with working on this platform, follow this sequence:

1.  **Read the [Product Requirements Document](PRODUCT_REQUIREMENTS.md)** to understand the product vision and features
2.  **Review the [System Architecture](SYSTEM_ARCHITECTURE.md)** to understand the technical design
3.  **Study the [Development SOP](DEVELOPMENT_SOP.md)** to understand coding standards and workflows
4.  **Consult the [Data Model Schema](DATA_MODEL_SCHEMA.md)** when working with the database
5.  **Reference the [API Specification](API_SPECIFICATION.md)** when building or modifying endpoints
6.  **Follow the [Security Blueprint](SECURITY_PRIVACY_COMPLIANCE.md)** to ensure compliance
7.  **Use the [Testing Strategy](TESTING_STRATEGY.md)** to write comprehensive tests
8.  **Refer to the [Deployment Guide](DEPLOYMENT_GUIDE.md)** when deploying changes

## Contributing

All development is performed by AI agents within Google Antigravity. Human oversight is limited to:

*   Reviewing AI-generated artifacts (code, tests, documentation)
*   Approving major architectural decisions
*   Handling escalated support tickets from Zoho Desk

## License

Proprietary - All rights reserved

## Contact

For questions or support regarding this documentation, please create an issue in this repository.

---

**Last Updated:** December 2024  
**Documentation Version:** 1.0  
**Maintained by:** AI Agents via Google Antigravity
