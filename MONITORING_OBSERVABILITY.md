# Monitoring and Observability Guide

## 1. Introduction

This document outlines the monitoring and observability strategy for the Subscription Management Micro-SaaS platform. You, the AI agent, must implement these monitoring practices to ensure system health, performance, and reliability.

## 2. Monitoring Stack

The platform uses the following monitoring tools:

| Tool | Purpose |
|------|---------|
| Vercel Analytics | Frontend performance and user analytics |
| Supabase Logs | Database query logs and performance |
| Sentry | Error tracking and crash reporting |
| Uptime Robot | Uptime monitoring and alerting |
| LogTail | Centralized log aggregation |

## 3. Key Metrics to Monitor

### 3.1. Application Performance Metrics

*   **Response Time:** API endpoint response times (p50, p95, p99)
*   **Throughput:** Requests per second
*   **Error Rate:** Percentage of failed requests
*   **Database Query Performance:** Query execution time

### 3.2. Business Metrics

*   **Active Users:** Daily and monthly active users
*   **Subscription Count:** Total subscriptions tracked by users
*   **Trial Detection Rate:** Number of trials detected per day
*   **Email Integration Status:** Number of connected email accounts
*   **Notification Delivery Rate:** Percentage of notifications successfully delivered

### 3.3. Infrastructure Metrics

*   **CPU Usage:** Vercel function CPU usage
*   **Memory Usage:** Vercel function memory usage
*   **Database Connections:** Number of active database connections
*   **Storage Usage:** Supabase storage usage

## 4. Logging Strategy

### 4.1. Log Levels

You must use the following log levels consistently:

| Level | Usage |
|-------|-------|
| ERROR | System errors, exceptions, and failures |
| WARN | Potential issues, deprecated features, rate limiting |
| INFO | Important business events (user signup, subscription created) |
| DEBUG | Detailed debugging information (development only) |

### 4.2. Structured Logging

All logs must be structured in JSON format:

```typescript
import { logger } from './utils/logger';

logger.info('Subscription created', {
  userId: user.id,
  subscriptionId: subscription.id,
  subscriptionName: subscription.name,
  amount: subscription.amount,
  timestamp: new Date().toISOString()
});
```

### 4.3. Log Retention

*   **Production Logs:** Retained for 30 days
*   **Error Logs:** Retained for 90 days
*   **Audit Logs:** Retained for 1 year

### 4.4. What NOT to Log

You must never log the following:

*   Passwords or authentication tokens
*   Full email content
*   Credit card numbers or payment details
*   OAuth access tokens or refresh tokens
*   API keys or secrets

## 5. Error Tracking with Sentry

### 5.1. Sentry Setup

```typescript
import * as Sentry from "@sentry/react";

Sentry.init({
  dsn: process.env.REACT_APP_SENTRY_DSN,
  environment: process.env.NODE_ENV,
  tracesSampleRate: 0.1,
  beforeSend(event, hint) {
    // Filter out sensitive data
    if (event.request) {
      delete event.request.cookies;
      delete event.request.headers?.Authorization;
    }
    return event;
  }
});
```

### 5.2. Error Context

When logging errors, you must include relevant context:

```typescript
try {
  await createSubscription(data);
} catch (error) {
  Sentry.captureException(error, {
    tags: {
      operation: 'create_subscription',
      userId: user.id
    },
    extra: {
      subscriptionData: data,
      timestamp: new Date().toISOString()
    }
  });
  throw error;
}
```

## 6. Uptime Monitoring

### 6.1. Health Check Endpoint

You must implement a health check endpoint:

```typescript
// /api/health
export async function GET() {
  const checks = {
    database: await checkDatabaseConnection(),
    zoho: await checkZohoAPI(),
    email: await checkEmailService()
  };

  const isHealthy = Object.values(checks).every(check => check.status === 'ok');

  return Response.json({
    status: isHealthy ? 'healthy' : 'degraded',
    checks,
    timestamp: new Date().toISOString()
  }, {
    status: isHealthy ? 200 : 503
  });
}
```

### 6.2. Uptime Monitoring Configuration

Configure Uptime Robot to monitor:

*   Main application URL (every 5 minutes)
*   Health check endpoint (every 5 minutes)
*   API endpoints (every 10 minutes)

Alert channels:

*   Email to on-call engineer
*   Slack webhook to #alerts channel

## 7. Performance Monitoring

### 7.1. API Performance Tracking

You must track the performance of all API endpoints:

```typescript
import { performance } from 'perf_hooks';

export function withPerformanceTracking(handler) {
  return async (req, res) => {
    const start = performance.now();
    
    try {
      await handler(req, res);
    } finally {
      const duration = performance.now() - start;
      
      logger.info('API request completed', {
        method: req.method,
        path: req.url,
        duration: duration,
        status: res.statusCode
      });

      // Send to analytics
      trackMetric('api.response_time', duration, {
        endpoint: req.url,
        method: req.method
      });
    }
  };
}
```

### 7.2. Database Query Performance

Monitor slow queries using Supabase's built-in query analyzer. You must:

*   Identify queries taking longer than 200ms
*   Add indexes for frequently queried columns
*   Optimize complex queries with JOINs

## 8. Alerting Rules

### 8.1. Critical Alerts

You must configure alerts for the following critical conditions:

| Condition | Threshold | Action |
|-----------|-----------|--------|
| API Error Rate | > 5% | Page on-call engineer |
| Response Time | p95 > 1000ms | Send Slack alert |
| Database CPU | > 80% | Send Slack alert |
| Uptime | < 99.5% | Page on-call engineer |
| Failed Payments | > 10 in 1 hour | Send Slack alert |

### 8.2. Warning Alerts

| Condition | Threshold | Action |
|-----------|-----------|--------|
| API Error Rate | > 2% | Send Slack alert |
| Response Time | p95 > 500ms | Send Slack alert |
| Email Integration Failures | > 5 in 1 hour | Send Slack alert |

## 9. Dashboards

### 9.1. Operations Dashboard

Create a dashboard that displays:

*   Current system status (healthy/degraded)
*   Real-time error rate
*   API response times (p50, p95, p99)
*   Active users (last 24 hours)
*   Recent errors (last 10)

### 9.2. Business Metrics Dashboard

Create a dashboard that displays:

*   Total users
*   Total subscriptions tracked
*   Trials detected (today, this week, this month)
*   Email accounts connected
*   Notification delivery rate

## 10. Incident Response Integration

### 10.1. Automated Incident Creation

When critical alerts are triggered, you must automatically create an incident:

```typescript
async function createIncident(alert: Alert) {
  const incident = {
    title: alert.title,
    severity: alert.severity,
    description: alert.description,
    timestamp: new Date().toISOString(),
    status: 'open'
  };

  // Create incident in tracking system
  await incidentTracker.create(incident);

  // Notify on-call engineer
  await notifyOnCall(incident);

  // Create Slack thread
  await createSlackThread(incident);
}
```

## 11. Regular Monitoring Tasks

### 11.1. Daily Tasks

You, the AI agent, must perform the following daily tasks:

*   Review error logs for new or recurring issues
*   Check system performance metrics
*   Verify all scheduled jobs executed successfully
*   Monitor email integration status

### 11.2. Weekly Tasks

*   Analyze performance trends
*   Review and optimize slow database queries
*   Check for outdated dependencies with security vulnerabilities
*   Review and update alerting thresholds if needed

### 11.3. Monthly Tasks

*   Generate and review monthly uptime report
*   Analyze user growth and engagement metrics
*   Review and optimize infrastructure costs
*   Conduct security audit

## 12. Tracing and Debugging

### 12.1. Distributed Tracing

For complex operations that span multiple services, implement distributed tracing:

```typescript
import { trace } from '@opentelemetry/api';

const tracer = trace.getTracer('subscription-manager');

export async function processTrialDetection(emailId: string) {
  const span = tracer.startSpan('process_trial_detection');
  
  try {
    span.setAttribute('email_id', emailId);
    
    const email = await fetchEmail(emailId);
    span.addEvent('email_fetched');
    
    const trial = await parseTrialEmail(email);
    span.addEvent('email_parsed');
    
    if (trial) {
      await createPendingTrial(trial);
      span.addEvent('trial_created');
    }
    
    span.setStatus({ code: SpanStatusCode.OK });
  } catch (error) {
    span.recordException(error);
    span.setStatus({ code: SpanStatusCode.ERROR });
    throw error;
  } finally {
    span.end();
  }
}
```

## 13. Cost Monitoring

You must monitor infrastructure costs:

*   Vercel function invocations and bandwidth
*   Supabase database size and bandwidth
*   Third-party API usage (Zoho, email providers)

Set up budget alerts:

*   Warning at 80% of monthly budget
*   Critical alert at 100% of monthly budget
