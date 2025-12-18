# API Specification

## 1. Introduction

This document defines the complete API specification for the Subscription Management Micro-SaaS platform. You, the AI agent, must implement all endpoints according to this specification.

## 2. API Architecture

*   **Protocol:** RESTful API over HTTPS
*   **Base URL:** `https://api.subscriptionmanager.com` (production)
*   **Authentication:** JWT-based authentication using Supabase Auth
*   **Content Type:** `application/json`

## 3. Authentication

All API endpoints require authentication unless otherwise specified. The client must include a valid JWT token in the `Authorization` header:

```
Authorization: Bearer <jwt_token>
```

## 4. Error Handling

All errors must follow this standard format:

```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error message",
    "details": {}
  }
}
```

Common HTTP status codes:

| Status Code | Description |
|-------------|-------------|
| 200 | Success |
| 201 | Created |
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 500 | Internal Server Error |

## 5. Subscription Endpoints

### 5.1. List Subscriptions

**Endpoint:** `GET /api/subscriptions`

**Query Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| workspace_id | uuid | Filter by workspace |
| status | string | Filter by status |
| category | string | Filter by category |
| limit | integer | Number of results (default: 50) |
| offset | integer | Pagination offset |

**Response:**

```json
{
  "data": [
    {
      "id": "uuid",
      "name": "Netflix",
      "category": "streaming",
      "status": "active",
      "amount": 15.99,
      "currency": "USD",
      "billing_period": "monthly",
      "next_renewal_date": "2024-02-01",
      "payment_method_id": "uuid",
      "created_at": "2024-01-01T00:00:00Z"
    }
  ],
  "pagination": {
    "total": 100,
    "limit": 50,
    "offset": 0
  }
}
```

### 5.2. Create Subscription

**Endpoint:** `POST /api/subscriptions`

**Request Body:**

```json
{
  "workspace_id": "uuid",
  "name": "Netflix",
  "category": "streaming",
  "amount": 15.99,
  "currency": "USD",
  "billing_period": "monthly",
  "next_renewal_date": "2024-02-01",
  "payment_method_id": "uuid",
  "notes": "Family plan"
}
```

**Response:** `201 Created`

```json
{
  "data": {
    "id": "uuid",
    "name": "Netflix",
    ...
  }
}
```

### 5.3. Update Subscription

**Endpoint:** `PATCH /api/subscriptions/:id`

**Request Body:** (partial update)

```json
{
  "amount": 17.99,
  "next_renewal_date": "2024-03-01"
}
```

**Response:** `200 OK`

### 5.4. Delete Subscription

**Endpoint:** `DELETE /api/subscriptions/:id`

**Response:** `204 No Content`

## 6. Payment Method Endpoints

### 6.1. List Payment Methods

**Endpoint:** `GET /api/payment-methods`

**Query Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| workspace_id | uuid | Filter by workspace |

**Response:**

```json
{
  "data": [
    {
      "id": "uuid",
      "type": "card",
      "nickname": "Chase Sapphire",
      "last4": "1234",
      "expiry_month": 12,
      "expiry_year": 2025,
      "issuer": "Chase"
    }
  ]
}
```

### 6.2. Create Payment Method

**Endpoint:** `POST /api/payment-methods`

**Request Body:**

```json
{
  "workspace_id": "uuid",
  "type": "card",
  "nickname": "Chase Sapphire",
  "last4": "1234",
  "expiry_month": 12,
  "expiry_year": 2025,
  "issuer": "Chase"
}
```

**Response:** `201 Created`

## 7. Email Integration Endpoints

### 7.1. Connect Email Account

**Endpoint:** `POST /api/email/connect`

**Request Body:**

```json
{
  "provider": "gmail",
  "auth_code": "oauth_authorization_code"
}
```

**Response:** `201 Created`

```json
{
  "data": {
    "id": "uuid",
    "provider": "gmail",
    "email_address": "user@gmail.com",
    "status": "active"
  }
}
```

### 7.2. Disconnect Email Account

**Endpoint:** `DELETE /api/email/:id`

**Response:** `204 No Content`

### 7.3. Email Webhook (Internal)

**Endpoint:** `POST /api/email/webhook`

This endpoint receives notifications from email providers (Gmail Pub/Sub, Microsoft Graph).

**Request Body:** (varies by provider)

**Response:** `200 OK`

## 8. Trial Detection Endpoints

### 8.1. List Pending Trials

**Endpoint:** `GET /api/trials/pending`

**Response:**

```json
{
  "data": [
    {
      "id": "uuid",
      "name": "Canva",
      "trial_start_date": "2024-01-01",
      "trial_end_date": "2024-01-15",
      "source": "email_detected",
      "status": "trial_pending"
    }
  ]
}
```

### 8.2. Confirm Trial

**Endpoint:** `POST /api/trials/:id/confirm`

**Request Body:**

```json
{
  "name": "Canva Pro",
  "trial_end_date": "2024-01-15",
  "amount": 12.99,
  "billing_period": "monthly"
}
```

**Response:** `200 OK`

### 8.3. Dismiss Trial

**Endpoint:** `POST /api/trials/:id/dismiss`

**Response:** `200 OK`

## 9. Analytics Endpoints

### 9.1. Get Dashboard Summary

**Endpoint:** `GET /api/analytics/dashboard`

**Query Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| workspace_id | uuid | Workspace ID |

**Response:**

```json
{
  "data": {
    "total_monthly_spend": 150.00,
    "total_annual_spend": 1800.00,
    "active_subscriptions": 12,
    "active_trials": 3,
    "upcoming_renewals": [
      {
        "subscription_id": "uuid",
        "name": "Netflix",
        "renewal_date": "2024-02-01",
        "amount": 15.99
      }
    ],
    "at_risk_subscriptions": [
      {
        "subscription_id": "uuid",
        "name": "Spotify",
        "reason": "card_expiring",
        "payment_method": {
          "last4": "1234",
          "expiry_month": 2,
          "expiry_year": 2024
        }
      }
    ]
  }
}
```

## 10. Notification Endpoints

### 10.1. Get Notification Preferences

**Endpoint:** `GET /api/notifications/preferences`

**Response:**

```json
{
  "data": {
    "renewal_reminders": {
      "enabled": true,
      "days_before": [3, 7],
      "channels": ["email", "push"]
    },
    "trial_reminders": {
      "enabled": true,
      "days_before": [3],
      "channels": ["email", "push", "in-app"]
    }
  }
}
```

### 10.2. Update Notification Preferences

**Endpoint:** `PATCH /api/notifications/preferences`

**Request Body:**

```json
{
  "renewal_reminders": {
    "enabled": true,
    "days_before": [7, 14],
    "channels": ["email"]
  }
}
```

**Response:** `200 OK`

## 11. Rate Limiting

All API endpoints are subject to rate limiting:

*   **Authenticated requests:** 1000 requests per hour per user
*   **Webhook endpoints:** 10000 requests per hour

Rate limit headers are included in all responses:

```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1609459200
```

## 12. Versioning

The API uses URL versioning. The current version is `v1`. Future versions will be accessible via `/api/v2/...`.
