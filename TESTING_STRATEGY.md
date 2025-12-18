# Testing Strategy

## 1. Introduction

This document outlines the comprehensive testing strategy for the Subscription Management Micro-SaaS platform. You, the AI agent, must implement and maintain tests according to this specification to ensure code quality and system reliability.

## 2. Testing Pyramid

The platform follows the standard testing pyramid:

```
        /\
       /  \
      / E2E \
     /______\
    /        \
   /Integration\
  /____________\
 /              \
/  Unit Tests    \
/________________\
```

*   **Unit Tests:** 70% of test coverage
*   **Integration Tests:** 20% of test coverage
*   **End-to-End Tests:** 10% of test coverage

## 3. Unit Testing

### 3.1. Frontend Unit Tests

**Framework:** Jest + React Testing Library

**What to Test:**

*   Individual React components
*   Utility functions
*   Custom hooks
*   Form validation logic

**Example:**

```typescript
import { render, screen, fireEvent } from '@testing-library/react';
import { SubscriptionForm } from './SubscriptionForm';

describe('SubscriptionForm', () => {
  test('displays validation error when name is empty', async () => {
    render(<SubscriptionForm />);
    
    const submitButton = screen.getByRole('button', { name: /save/i });
    fireEvent.click(submitButton);
    
    expect(await screen.findByText(/name is required/i)).toBeInTheDocument();
  });

  test('submits form with valid data', async () => {
    const onSubmit = jest.fn();
    render(<SubscriptionForm onSubmit={onSubmit} />);
    
    fireEvent.change(screen.getByLabelText(/service name/i), {
      target: { value: 'Netflix' }
    });
    fireEvent.change(screen.getByLabelText(/cost/i), {
      target: { value: '15.99' }
    });
    
    fireEvent.click(screen.getByRole('button', { name: /save/i }));
    
    await waitFor(() => {
      expect(onSubmit).toHaveBeenCalledWith({
        name: 'Netflix',
        amount: 15.99
      });
    });
  });
});
```

### 3.2. Backend Unit Tests

**Framework:** Jest

**What to Test:**

*   API route handlers
*   Business logic functions
*   Email parsing logic
*   Data transformation functions

**Example:**

```typescript
import { parseTrialEmail } from './emailParser';

describe('parseTrialEmail', () => {
  test('extracts trial information from Netflix email', () => {
    const subject = 'Welcome to your Netflix free trial';
    const body = 'Your 30-day free trial has started. After the trial, you will be charged $15.99/month.';
    const sender = 'noreply@netflix.com';
    const receivedDate = new Date('2024-01-01');

    const result = parseTrialEmail(subject, body, sender, receivedDate);

    expect(result).toEqual({
      serviceName: 'Netflix',
      trialStartDate: new Date('2024-01-01'),
      trialEndDate: new Date('2024-01-31'),
      postTrialPrice: 15.99,
      billingPeriod: 'monthly',
      confidence: expect.any(Number)
    });
  });

  test('returns null for non-trial emails', () => {
    const subject = 'Your monthly invoice';
    const body = 'Your payment of $15.99 was successful.';
    const sender = 'billing@netflix.com';
    const receivedDate = new Date('2024-01-01');

    const result = parseTrialEmail(subject, body, sender, receivedDate);

    expect(result).toBeNull();
  });
});
```

## 4. Integration Testing

### 4.1. API Integration Tests

**Framework:** Jest + Supertest

**What to Test:**

*   API endpoints with database interactions
*   Authentication and authorization flows
*   Webhook handlers
*   Third-party API integrations (mocked)

**Example:**

```typescript
import request from 'supertest';
import { app } from '../app';
import { supabaseClient } from '../utility/supabaseClient';

describe('Subscription API', () => {
  let authToken: string;

  beforeAll(async () => {
    // Create test user and get auth token
    const { data } = await supabaseClient.auth.signInWithPassword({
      email: 'test@example.com',
      password: 'testpassword'
    });
    authToken = data.session!.access_token;
  });

  afterAll(async () => {
    // Clean up test data
    await supabaseClient.from('subscriptions').delete().eq('name', 'Test Subscription');
  });

  test('POST /api/subscriptions creates a new subscription', async () => {
    const response = await request(app)
      .post('/api/subscriptions')
      .set('Authorization', `Bearer ${authToken}`)
      .send({
        name: 'Test Subscription',
        amount: 9.99,
        billing_period: 'monthly'
      });

    expect(response.status).toBe(201);
    expect(response.body.data).toHaveProperty('id');
    expect(response.body.data.name).toBe('Test Subscription');
  });

  test('GET /api/subscriptions returns user subscriptions', async () => {
    const response = await request(app)
      .get('/api/subscriptions')
      .set('Authorization', `Bearer ${authToken}`);

    expect(response.status).toBe(200);
    expect(Array.isArray(response.body.data)).toBe(true);
  });

  test('POST /api/subscriptions returns 401 without auth token', async () => {
    const response = await request(app)
      .post('/api/subscriptions')
      .send({
        name: 'Test Subscription',
        amount: 9.99
      });

    expect(response.status).toBe(401);
  });
});
```

### 4.2. Email Integration Tests

**What to Test:**

*   Gmail API integration (mocked)
*   Outlook Graph API integration (mocked)
*   IMAP connection for iCloud (mocked)
*   Trial detection and parsing

**Example:**

```typescript
import { processGmailNotification } from '../services/emailService';
import { supabaseClient } from '../utility/supabaseClient';

jest.mock('../utility/gmailClient');

describe('Email Integration', () => {
  test('creates pending trial when trial email is detected', async () => {
    const mockMessage = {
      id: 'msg123',
      payload: {
        headers: [
          { name: 'Subject', value: 'Welcome to your Spotify Premium trial' },
          { name: 'From', value: 'noreply@spotify.com' }
        ],
        body: {
          data: Buffer.from('Your 30-day free trial has started.').toString('base64')
        }
      }
    };

    await processGmailNotification('user123', mockMessage);

    const { data } = await supabaseClient
      .from('subscriptions')
      .select('*')
      .eq('status', 'trial_pending')
      .eq('name', 'Spotify')
      .single();

    expect(data).toBeDefined();
    expect(data.trial_end_date).toBeDefined();
  });
});
```

## 5. End-to-End Testing

### 5.1. E2E Test Framework

**Framework:** Playwright

**What to Test:**

*   Critical user flows
*   Multi-step processes
*   Cross-browser compatibility

### 5.2. Critical User Flows

**Flow 1: User Registration and Onboarding**

```typescript
import { test, expect } from '@playwright/test';

test('user can register and complete onboarding', async ({ page }) => {
  // Navigate to registration page
  await page.goto('/register');

  // Fill registration form
  await page.fill('input[name="email"]', 'newuser@example.com');
  await page.fill('input[name="password"]', 'SecurePassword123!');
  await page.fill('input[name="full_name"]', 'John Doe');
  await page.click('button[type="submit"]');

  // Wait for redirect to onboarding
  await expect(page).toHaveURL(/\/onboarding/);

  // Complete onboarding steps
  await page.click('button:has-text("Skip for now")'); // Skip email connection

  // Verify dashboard is displayed
  await expect(page).toHaveURL(/\/dashboard/);
  await expect(page.locator('h1')).toContainText('Dashboard');
});
```

**Flow 2: Add Subscription**

```typescript
test('user can add a new subscription', async ({ page }) => {
  // Login
  await page.goto('/login');
  await page.fill('input[name="email"]', 'test@example.com');
  await page.fill('input[name="password"]', 'testpassword');
  await page.click('button[type="submit"]');

  // Navigate to subscriptions
  await page.click('a[href="/subscriptions"]');

  // Click add subscription
  await page.click('button:has-text("Add Subscription")');

  // Fill subscription form
  await page.fill('input[name="name"]', 'Netflix');
  await page.fill('input[name="amount"]', '15.99');
  await page.selectOption('select[name="billing_period"]', 'monthly');
  await page.fill('input[name="next_renewal_date"]', '2024-02-01');

  // Submit form
  await page.click('button[type="submit"]');

  // Verify subscription appears in list
  await expect(page.locator('text=Netflix')).toBeVisible();
  await expect(page.locator('text=$15.99')).toBeVisible();
});
```

**Flow 3: Email Trial Detection**

```typescript
test('user can connect email and confirm detected trial', async ({ page }) => {
  // Login
  await page.goto('/login');
  await page.fill('input[name="email"]', 'test@example.com');
  await page.fill('input[name="password"]', 'testpassword');
  await page.click('button[type="submit"]');

  // Navigate to settings
  await page.click('a[href="/settings"]');

  // Connect email (mock OAuth flow)
  await page.click('button:has-text("Connect Gmail")');
  // ... OAuth flow simulation ...

  // Wait for trial detection notification
  await expect(page.locator('.notification')).toContainText('Trial detected');

  // Click on notification
  await page.click('.notification');

  // Confirm trial
  await page.click('button:has-text("Confirm & Track")');

  // Verify trial appears in subscriptions
  await page.goto('/subscriptions');
  await expect(page.locator('.trial-badge')).toBeVisible();
});
```

## 6. Test Coverage Requirements

You must maintain the following minimum test coverage:

| Component | Coverage Target |
|-----------|----------------|
| Frontend Components | 80% |
| Backend API Routes | 90% |
| Business Logic Functions | 95% |
| Email Parsing | 90% |
| Overall | 85% |

## 7. Continuous Integration

### 7.1. CI Pipeline

You must configure a CI pipeline that runs on every pull request:

```yaml
name: Test Suite

on: [pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run unit tests
        run: npm run test:unit
      
      - name: Run integration tests
        run: npm run test:integration
      
      - name: Run E2E tests
        run: npm run test:e2e
      
      - name: Upload coverage
        uses: codecov/codecov-action@v2
```

## 8. Test Data Management

### 8.1. Test Database

You must use a separate test database for integration and E2E tests. The test database should be reset before each test suite:

```typescript
beforeAll(async () => {
  await resetTestDatabase();
  await seedTestData();
});

afterAll(async () => {
  await cleanupTestDatabase();
});
```

### 8.2. Test Fixtures

Create reusable test fixtures for common data:

```typescript
export const testSubscription = {
  name: 'Test Subscription',
  amount: 9.99,
  billing_period: 'monthly',
  status: 'active'
};

export const testUser = {
  email: 'test@example.com',
  full_name: 'Test User',
  password: 'TestPassword123!'
};
```

## 9. Performance Testing

### 9.1. Load Testing

You should perform load testing for critical endpoints:

```typescript
import { check } from 'k6';
import http from 'k6/http';

export let options = {
  vus: 100,
  duration: '30s',
};

export default function () {
  let response = http.get('https://api.subscriptionmanager.com/subscriptions', {
    headers: { 'Authorization': 'Bearer TOKEN' }
  });
  
  check(response, {
    'status is 200': (r) => r.status === 200,
    'response time < 200ms': (r) => r.timings.duration < 200,
  });
}
```

## 10. Security Testing

### 10.1. Automated Security Scans

You must run automated security scans:

*   **npm audit** - Check for vulnerable dependencies
*   **OWASP ZAP** - Web application security testing
*   **Snyk** - Continuous security monitoring

### 10.2. Authentication Testing

Test authentication and authorization:

```typescript
test('API rejects requests without valid token', async () => {
  const response = await request(app)
    .get('/api/subscriptions')
    .set('Authorization', 'Bearer invalid_token');

  expect(response.status).toBe(401);
});

test('User cannot access other users subscriptions', async () => {
  const response = await request(app)
    .get('/api/subscriptions/other-user-subscription-id')
    .set('Authorization', `Bearer ${authToken}`);

  expect(response.status).toBe(403);
});
```
