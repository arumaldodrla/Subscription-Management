# Development & Enhancement Guide (AI-First)

## 1. Introduction

This document provides guidance for you, the AI agent, on the continuous development, enhancement, and maintenance of the Subscription Management Micro-SaaS platform. Your primary objective is to evolve the system while preserving its stability, security, and performance.

## 2. Evolving the Platform

### 2.1. Interpreting and Updating the PRD

When a request for a new feature or a change in scope is made, you must first update the Product Requirements Document (PRD). This ensures that the documentation remains the single source of truth for the platform's functionality.

### 2.2. Schema Evolution

When a database schema change is required, you must create a new migration file in the `supabase/migrations` directory. Migrations must be backward-compatible to avoid breaking existing data. You must never make breaking changes to the database schema in a production environment.

### 2.3. Feature Flags and Safe Rollouts

For significant new features, you must use feature flags to enable a gradual rollout. This allows for testing the feature with a small subset of users before making it generally available. You should implement a simple feature flag system using a table in the Supabase database.

## 3. Self-Maintenance

### 3.1. Weekly Self-Checks

You are responsible for performing weekly self-checks of the platform. This includes:

*   Running all tests (unit, integration, and E2E).
*   Running a linter to check for code quality issues.
*   Performing a security scan to identify potential vulnerabilities.

### 3.2. Scheduled Tasks

You can use Supabase Edge Functions to create and manage scheduled tasks, such as sending weekly email digests or performing data cleanup. You must write these functions to be idempotent and to handle errors gracefully.

### 3.3. Log Review and Issue Triage

You must regularly review the application logs to identify and triage issues. When an error is detected, you must create a new issue in the project's issue tracker with all the relevant information, including the error message, stack trace, and any associated user context.

## 4. Performance & Optimization

### 4.1. Query Optimization

You must write efficient database queries and use indexes appropriately to ensure optimal performance. You should use the `EXPLAIN` command to analyze the performance of complex queries.

### 4.2. Caching

For frequently accessed data that does not change often, you should implement a caching layer to reduce the load on the database. You can use a simple in-memory cache or a distributed cache like Redis.

### 4.3. Minimizing Over-Fetching

You must design your API endpoints to avoid over-fetching data. Use GraphQL or allow clients to specify the fields they need in the API response to minimize the amount of data transferred over the network.
