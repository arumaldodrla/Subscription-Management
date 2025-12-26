# Subscription Management Micro-SaaS

**Technical Documentation Repository**

---

## 1. What is This?

This repository is the single source of truth for the **Subscription Management Micro-SaaS** platform. It contains all the technical documentation required for AI agents to design, build, deploy, operate, and evolve the application. It is also structured to be understandable by human stakeholders who oversee the project and by marketing teams who need to understand the product's value proposition.

## 2. The Product in a Nutshell

**Subscription Management** is an intelligent platform that helps individuals, families, and small teams take complete control of their recurring subscription payments. In an era of "subscription fatigue," our solution provides a centralized, privacy-first command center to manage the entire subscription lifecycle.

**The Problem:** Consumers are overwhelmed by the number of subscriptions they manage. They lose track of payments, forget to cancel free trials, and waste money on services they no longer use.

**Our Solution:** A dedicated "control cockpit" that provides complete visibility, proactive intelligence (like automated trial detection from email), and effortless control over all recurring expenses.

**Primary Tagline:** *Your Subscriptions, Under Control.*

## 3. Documentation Index

This documentation is organized into several categories to serve different needs.

### For Marketing & Business Stakeholders

This document captures the essence of the product, its value proposition, target audience, and key messaging. It is the primary resource for creating marketing materials.

| Document | Description |
| :--- | :--- |
| [Product Vision & Marketing Blueprint](PRODUCT_VISION_AND_MARKETING.md) | The "why" of the product. Problem, solution, personas, differentiators, and key messaging. |

### For AI Agents & Developers

These documents provide the technical specifications required to build and maintain the platform.

| Document | Description |
| :--- | :--- |
| [Product Requirements (PRD)](PRODUCT_REQUIREMENTS.md) | Detailed functional and non-functional requirements, features, and acceptance criteria. |
| [System Architecture](SYSTEM_ARCHITECTURE.md) | High-level architecture, component breakdown, technology stack, and design rationale. |
| [Data Model & Schema](DATA_MODEL_SCHEMA.md) | Complete database schema, table definitions, RLS policies, and migration strategy. |
| [API Specification](API_SPECIFICATION.md) | RESTful API endpoints, request/response formats, authentication, and error handling. |

### For Feature Implementation

These guides provide step-by-step instructions for implementing specific features.

| Document | Description |
| :--- | :--- |
| [Email Trial Detection Guide](EMAIL_TRIAL_DETECTION.md) | How to integrate Gmail, Outlook, and iCloud for automated trial detection. |
| [Zoho Integration Guide](ZOHO_INTEGRATION_GUIDE.md) | How to integrate with Zoho Billing, Books, CRM, and Desk. |
| [Frontend Development Guide](FRONTEND_DEVELOPMENT_GUIDE.md) | How to build the UI using Refine and the Metronic theme. |

### For Operations & Deployment

These documents cover how to deploy, test, monitor, and maintain the platform.

| Document | Description |
| :--- | :--- |
| [Development SOP](DEVELOPMENT_SOP.md) | Standard operating procedures for AI agents: coding standards, PR process, and workflows. |
| [Deployment Guide](DEPLOYMENT_GUIDE.md) | Step-by-step guide for deploying to Vercel and Supabase. |
| [Testing Strategy](TESTING_STRATEGY.md) | Unit, integration, and E2E testing frameworks, coverage requirements, and CI/CD. |
| [Monitoring & Observability](MONITORING_OBSERVABILITY.md) | Logging, error tracking, uptime monitoring, and alerting. |
| [Development & Enhancement Guide](DEVELOPMENT_ENHANCEMENT_GUIDE.md) | How to evolve the PRD, schema, and features over time. |

### For Security & Compliance

| Document | Description |
| :--- | :--- |
| [Security, Privacy & Compliance Blueprint](SECURITY_PRIVACY_COMPLIANCE.md) | ISO 27001, SOC 2, and GDPR requirements. |

### For Support & Incident Response

| Document | Description |
| :--- | :--- |
| [AI Support & Zoho Desk Playbook](AI_SUPPORT_ZOHO_PLAYBOOK.md) | How the AI chatbot handles support and escalates to Zoho Desk. |
| [Incident Response Runbook](INCIDENT_RESPONSE_RUNBOOK.md) | Procedures for detecting, responding to, and recovering from incidents. |

### Diagrams

Visual diagrams are stored in the `diagrams/` directory.

| Diagram | Description |
| :--- | :--- |
| [System Architecture](diagrams/DETAILED_SYSTEM_ARCHITECTURE.png) | High-level view of all system components. |
| [Email Trial Detection Flow](diagrams/EMAIL_TRIAL_DETECTION_FLOW.png) | Sequence diagram for the email trial detection feature. |

## 4. Technology Stack Summary

| Layer | Technology |
| :--- | :--- |
| **Frontend** | React 18, Refine 3.x, Tailwind CSS 3.x, Metronic Theme |
| **Backend API** | Vercel Serverless Functions (Node.js 18) |
| **Database** | Supabase PostgreSQL 15 with Row-Level Security |
| **Authentication** | Supabase Auth (JWT) |
| **Background Jobs** | Supabase Edge Functions (Deno) |
| **Billing** | Zoho Billing + Authorize.net |
| **Support** | Zoho Desk |
| **Monitoring** | Vercel Analytics, Sentry, Uptime Robot |

## 5. Getting Started for AI Agents

If you are an AI agent tasked with working on this platform, follow this recommended reading order:

1.  **Understand the "Why":** Start with the [Product Vision & Marketing Blueprint](PRODUCT_VISION_AND_MARKETING.md).
2.  **Understand the "What":** Read the [Product Requirements Document](PRODUCT_REQUIREMENTS.md).
3.  **Understand the "How":** Study the [System Architecture](SYSTEM_ARCHITECTURE.md) and [Data Model](DATA_MODEL_SCHEMA.md).
4.  **Follow the Rules:** Adhere to the [Development SOP](DEVELOPMENT_SOP.md) for all coding tasks.
5.  **Build Features:** Use the specific implementation guides (Email, Zoho, Frontend) as needed.
6.  **Deploy & Monitor:** Follow the [Deployment Guide](DEPLOYMENT_GUIDE.md) and [Monitoring Guide](MONITORING_OBSERVABILITY.md).

---

**Last Updated:** December 26, 2025  
**Documentation Version:** 1.1  
**Maintained by:** AI Agents via Google Antigravity
