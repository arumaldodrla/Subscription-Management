# API Specification

**Version:** 1.1  
**Last Updated:** December 26, 2025

## 1. Introduction & Principles

This document provides the complete specification for the RESTful API of the Subscription Management Micro-SaaS platform. The API is designed to be consumed by the frontend application (Refine) and potentially by future third-party integrations. You, the AI agent, must adhere strictly to these definitions when building or interacting with the API.

**Key Principles:**

*   **Protocol:** All communication is over HTTPS.
*   **Versioning:** The API is versioned in the URL. The current version is `v1`. All endpoints are prefixed with `/api/v1`.
*   **Authentication:** All endpoints (except health checks) are protected and require a valid JWT issued by Supabase Auth. The token must be passed in the `Authorization` header as a Bearer token.
*   **Data Format:** All request and response bodies are in JSON format.
*   **Error Handling:** The API uses standard HTTP status codes. Error responses include a structured JSON body with a `message` field.

## 2. Common Structures

### 2.1. Standard Error Response

Used for `4xx` and `5xx` status codes.

```json
{
  "message": "A human-readable error message.",
  "details": "Optional: further technical details or validation errors."
}
```

### 2.2. Pagination

List endpoints will return a `pagination` object alongside the `data` array.

```json
{
  "data": [...],
  "pagination": {
    "total": 100,
    "limit": 20,
    "offset": 0
  }
}
```

## 3. Endpoints

--- 

### 3.1. Subscriptions (`/api/v1/subscriptions`)

#### `GET /api/v1/subscriptions`

*   **Description:** Retrieves a paginated list of subscriptions for the user's current workspace.
*   **Query Parameters:**
    *   `status` (string, optional): Filter by status (e.g., `active`, `trial_active`).
    *   `limit` (integer, optional, default: 20): Number of results per page.
    *   `offset` (integer, optional, default: 0): Pagination offset.
*   **Success Response (200 OK):**

```json
{
  "data": [
    {
      "id": "uuid",
      "name": "Netflix",
      "status": "active",
      "amount": 15.99,
      "next_renewal_date": "2026-01-15"
    }
  ],
  "pagination": { "total": 1, "limit": 20, "offset": 0 }
}
```

#### `POST /api/v1/subscriptions`

*   **Description:** Creates a new subscription.
*   **Request Body:**

```json
{
  "name": "Spotify",
  "amount": 9.99,
  "billing_period": "monthly",
  "next_renewal_date": "2026-01-20",
  "payment_method_id": "uuid"
}
```

*   **Success Response (201 Created):** Returns the newly created subscription object.

#### `GET /api/v1/subscriptions/{id}`

*   **Description:** Retrieves a single subscription by its ID.
*   **Success Response (200 OK):** Returns the full subscription object.

#### `PATCH /api/v1/subscriptions/{id}`

*   **Description:** Updates an existing subscription.
*   **Request Body:** Contains a subset of the subscription fields to be updated.
*   **Success Response (200 OK):** Returns the updated subscription object.

#### `DELETE /api/v1/subscriptions/{id}`

*   **Description:** Deletes a subscription.
*   **Success Response (204 No Content):**

--- 

### 3.2. Payment Methods (`/api/v1/payment-methods`)

#### `GET /api/v1/payment-methods`

*   **Description:** Retrieves all payment methods for the user's current workspace.
*   **Success Response (200 OK):**

```json
{
  "data": [
    {
      "id": "uuid",
      "type": "card",
      "nickname": "Personal Visa",
      "last4": "4242",
      "expiry_year": 2028
    }
  ]
}
```

#### `POST /api/v1/payment-methods`

*   **Description:** Adds a new payment method.
*   **Success Response (201 Created):** Returns the newly created payment method object.

--- 

### 3.3. Email Integration (`/api/v1/email`)

#### `POST /api/v1/email/connect`

*   **Description:** Initiates the OAuth 2.0 flow to connect a user's email account.
*   **Request Body:** `{"provider": "gmail"}`
*   **Success Response (200 OK):** `{"authorization_url": "https://accounts.google.com/o/oauth2/..."}`

#### `POST /api/v1/email/connect/callback`

*   **Description:** The callback URL that the OAuth provider redirects to. The server exchanges the authorization code for tokens.
*   **Success Response (200 OK):** `{"message": "Email account connected successfully."}`

#### `POST /api/v1/email/webhook/gmail`

*   **Description:** A secure webhook to receive push notifications from Google Pub/Sub.
*   **Success Response (204 No Content):**

--- 

### 3.4. Trials (`/api/v1/trials`)

#### `GET /api/v1/trials/pending`

*   **Description:** Retrieves all subscriptions with the status `trial_pending`.
*   **Success Response (200 OK):** Returns an array of subscription objects.

#### `POST /api/v1/trials/{id}/confirm`

*   **Description:** Confirms a pending trial, changing its status to `trial_active`.
*   **Success Response (200 OK):** Returns the updated subscription object.

#### `POST /api/v1/trials/{id}/dismiss`

*   **Description:** Dismisses a pending trial, deleting the record.
*   **Success Response (204 No Content):**

--- 

### 3.5. Analytics (`/api/v1/analytics`)

#### `GET /api/v1/analytics/summary`

*   **Description:** Retrieves a summary of analytics for the dashboard.
*   **Success Response (200 OK):**

```json
{
  "total_monthly_spend": 150.00,
  "active_subscriptions_count": 12,
  "active_trials_count": 3,
  "upcoming_renewals_count": 5,
  "at_risk_subscriptions_count": 2
}
```

--- 

### 3.6. System (`/api/v1/system`)

#### `GET /api/v1/system/health`

*   **Description:** An unauthenticated endpoint to check the health of the API.
*   **Success Response (200 OK):**

```json
{
  "status": "healthy",
  "timestamp": "2025-12-26T10:00:00Z"
}
```
