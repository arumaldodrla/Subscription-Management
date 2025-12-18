# Data Model and Schema Specification

## 1. Introduction

This document provides the complete data model specification for the Subscription Management Micro-SaaS platform. You, the AI agent, must use this specification as the authoritative source for all database schema definitions, relationships, and constraints.

## 2. Database Technology

The platform uses **Supabase Postgres** as the primary database. All tables must have Row-Level Security (RLS) enabled to ensure multi-tenant data isolation.

## 3. Core Tables

### 3.1. users

Stores user account information managed by Supabase Auth.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | uuid | PRIMARY KEY | User unique identifier (from Supabase Auth) |
| email | text | UNIQUE, NOT NULL | User email address |
| full_name | text | | User's full name |
| phone | text | | Optional phone number |
| created_at | timestamptz | DEFAULT now() | Account creation timestamp |
| updated_at | timestamptz | DEFAULT now() | Last update timestamp |

**RLS Policy:** Users can only read and update their own record.

### 3.2. workspaces

Represents a shared environment for users, families, or teams.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | uuid | PRIMARY KEY | Workspace unique identifier |
| name | text | NOT NULL | Workspace name |
| owner_id | uuid | FOREIGN KEY (users.id) | Workspace owner |
| created_at | timestamptz | DEFAULT now() | Creation timestamp |
| updated_at | timestamptz | DEFAULT now() | Last update timestamp |

**RLS Policy:** Users can access workspaces where they are members.

### 3.3. workspace_members

Manages workspace membership and roles.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | uuid | PRIMARY KEY | Membership unique identifier |
| workspace_id | uuid | FOREIGN KEY (workspaces.id) | Associated workspace |
| user_id | uuid | FOREIGN KEY (users.id) | Member user |
| role | text | NOT NULL | Role: 'admin', 'viewer' |
| created_at | timestamptz | DEFAULT now() | Membership creation timestamp |

**RLS Policy:** Users can read memberships for their workspaces.

### 3.4. payment_methods

Stores payment method information.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | uuid | PRIMARY KEY | Payment method unique identifier |
| workspace_id | uuid | FOREIGN KEY (workspaces.id) | Associated workspace |
| type | text | NOT NULL | Type: 'card', 'paypal', 'bank', 'other' |
| nickname | text | | User-defined nickname |
| last4 | text | | Last 4 digits of card/account |
| expiry_month | integer | | Card expiry month (1-12) |
| expiry_year | integer | | Card expiry year (YYYY) |
| issuer | text | | Card issuer or bank name |
| created_at | timestamptz | DEFAULT now() | Creation timestamp |
| updated_at | timestamptz | DEFAULT now() | Last update timestamp |

**RLS Policy:** Users can access payment methods for their workspaces.

### 3.5. subscriptions

The core table for storing subscription data.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | uuid | PRIMARY KEY | Subscription unique identifier |
| workspace_id | uuid | FOREIGN KEY (workspaces.id) | Associated workspace |
| name | text | NOT NULL | Subscription service name |
| description | text | | Additional description |
| category | text | | Category: 'streaming', 'software', etc. |
| status | text | NOT NULL | Status: 'active', 'trial_pending', 'trial_active', 'paused', 'cancelled' |
| billing_period | text | | Period: 'monthly', 'annual', 'custom' |
| amount | numeric | | Subscription cost |
| currency | text | DEFAULT 'USD' | Currency code |
| next_renewal_date | date | | Next renewal date |
| trial_start_date | date | | Trial start date (if applicable) |
| trial_end_date | date | | Trial end date (if applicable) |
| importance | text | | Importance: 'must-have', 'nice-to-have', 'trial', 'experiment' |
| tags | jsonb | DEFAULT '[]' | Custom tags array |
| payment_method_id | uuid | FOREIGN KEY (payment_methods.id) | Associated payment method |
| source | text | | Source: 'manual', 'email_detected', 'import' |
| is_private | boolean | DEFAULT false | Private vs shared in workspace |
| notes | text | | User notes |
| cancellation_url | text | | URL for cancellation |
| created_at | timestamptz | DEFAULT now() | Creation timestamp |
| updated_at | timestamptz | DEFAULT now() | Last update timestamp |

**RLS Policy:** Users can access subscriptions for their workspaces, respecting the is_private flag.

### 3.6. email_accounts

Stores email account credentials for trial detection.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | uuid | PRIMARY KEY | Email account unique identifier |
| user_id | uuid | FOREIGN KEY (users.id) | Associated user |
| workspace_id | uuid | FOREIGN KEY (workspaces.id) | Associated workspace |
| provider | text | NOT NULL | Provider: 'gmail', 'outlook', 'icloud' |
| email_address | text | NOT NULL | Email address |
| access_token_enc | text | | Encrypted OAuth access token |
| refresh_token_enc | text | | Encrypted OAuth refresh token |
| token_expires_at | timestamptz | | Token expiration timestamp |
| imap_password_enc | text | | Encrypted IMAP password (iCloud) |
| last_checked_at | timestamptz | | Last email check timestamp |
| status | text | DEFAULT 'active' | Status: 'active', 'error', 'revoked' |
| created_at | timestamptz | DEFAULT now() | Creation timestamp |
| updated_at | timestamptz | DEFAULT now() | Last update timestamp |

**RLS Policy:** Users can only access their own email accounts.

**Security Note:** All token and password fields must be encrypted at rest using Supabase's encryption features.

### 3.7. notifications

Stores notification preferences and history.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | uuid | PRIMARY KEY | Notification unique identifier |
| user_id | uuid | FOREIGN KEY (users.id) | Associated user |
| subscription_id | uuid | FOREIGN KEY (subscriptions.id) | Associated subscription |
| type | text | NOT NULL | Type: 'renewal', 'trial_end', 'card_expiry' |
| scheduled_at | timestamptz | NOT NULL | Scheduled notification time |
| sent_at | timestamptz | | Actual send time |
| status | text | DEFAULT 'pending' | Status: 'pending', 'sent', 'failed' |
| channels | jsonb | DEFAULT '[]' | Channels: ['email', 'push', 'in-app'] |
| created_at | timestamptz | DEFAULT now() | Creation timestamp |

**RLS Policy:** Users can only access their own notifications.

### 3.8. reminder_rules

Stores user-defined reminder rules.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | uuid | PRIMARY KEY | Rule unique identifier |
| user_id | uuid | FOREIGN KEY (users.id) | Associated user |
| workspace_id | uuid | FOREIGN KEY (workspaces.id) | Associated workspace |
| event_type | text | NOT NULL | Event: 'renewal', 'trial_end', 'card_expiry' |
| days_before | integer | NOT NULL | Days before event to notify |
| channels | jsonb | DEFAULT '[]' | Notification channels |
| is_default | boolean | DEFAULT false | Whether this is a default rule |
| created_at | timestamptz | DEFAULT now() | Creation timestamp |

**RLS Policy:** Users can access reminder rules for their workspaces.

## 4. Indexes

You must create the following indexes for optimal query performance:

*   `subscriptions.workspace_id` - For workspace-based queries
*   `subscriptions.payment_method_id` - For payment method lookups
*   `subscriptions.next_renewal_date` - For renewal date queries
*   `subscriptions.status` - For status-based filtering
*   `notifications.user_id, scheduled_at` - For notification scheduling
*   `email_accounts.user_id` - For user email account lookups

## 5. Sample RLS Policies

### 5.1. subscriptions Table

```sql
-- Users can view subscriptions in their workspaces
CREATE POLICY "Users can view workspace subscriptions"
ON subscriptions FOR SELECT
USING (
  workspace_id IN (
    SELECT workspace_id FROM workspace_members 
    WHERE user_id = auth.uid()
  )
  AND (is_private = false OR workspace_id IN (
    SELECT id FROM workspaces WHERE owner_id = auth.uid()
  ))
);

-- Users can insert subscriptions in their workspaces
CREATE POLICY "Users can insert workspace subscriptions"
ON subscriptions FOR INSERT
WITH CHECK (
  workspace_id IN (
    SELECT workspace_id FROM workspace_members 
    WHERE user_id = auth.uid() AND role = 'admin'
  )
);
```

## 6. Migration Strategy

You must create database migrations in the `supabase/migrations` directory. Each migration file should be named with a timestamp prefix (e.g., `20240101000000_create_subscriptions_table.sql`).

All migrations must be idempotent and reversible.
