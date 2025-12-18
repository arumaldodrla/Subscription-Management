# Frontend Development Guide: Refine + Metronic

## 1. Introduction

This document provides comprehensive guidance for developing the frontend of the Subscription Management Micro-SaaS platform using Refine and the Metronic Tailwind theme. You, the AI agent, must follow these guidelines when building or modifying frontend components.

## 2. Technology Stack

*   **Framework:** Refine (React-based)
*   **UI Library:** React 18+
*   **Styling:** Tailwind CSS
*   **Theme:** Metronic Tailwind
*   **State Management:** React Query (built into Refine)
*   **Routing:** React Router (built into Refine)
*   **Form Handling:** React Hook Form
*   **Data Fetching:** Refine Data Provider (Supabase)

## 3. Project Structure

```
frontend/
├── src/
│   ├── components/
│   │   ├── layout/
│   │   │   ├── Header.tsx
│   │   │   ├── Sidebar.tsx
│   │   │   └── Footer.tsx
│   │   ├── subscription/
│   │   │   ├── SubscriptionCard.tsx
│   │   │   ├── SubscriptionList.tsx
│   │   │   └── SubscriptionForm.tsx
│   │   ├── trial/
│   │   │   ├── TrialCard.tsx
│   │   │   └── TrialConfirmation.tsx
│   │   └── common/
│   │       ├── Button.tsx
│   │       ├── Modal.tsx
│   │       └── Card.tsx
│   ├── pages/
│   │   ├── dashboard/
│   │   │   └── index.tsx
│   │   ├── subscriptions/
│   │   │   ├── list.tsx
│   │   │   ├── create.tsx
│   │   │   ├── edit.tsx
│   │   │   └── show.tsx
│   │   ├── trials/
│   │   │   └── pending.tsx
│   │   ├── payment-methods/
│   │   │   └── list.tsx
│   │   └── settings/
│   │       └── index.tsx
│   ├── providers/
│   │   ├── dataProvider.ts
│   │   └── authProvider.ts
│   ├── hooks/
│   │   ├── useSubscriptions.ts
│   │   └── useNotifications.ts
│   ├── utils/
│   │   ├── formatters.ts
│   │   └── validators.ts
│   └── App.tsx
```

## 4. Refine Setup

### 4.1. App.tsx Configuration

```typescript
import { Refine } from "@refinedev/core";
import { RefineKbar, RefineKbarProvider } from "@refinedev/kbar";
import routerBindings from "@refinedev/react-router-v6";
import dataProvider from "@refinedev/supabase";
import { BrowserRouter, Routes, Route } from "react-router-dom";
import { supabaseClient } from "./utility/supabaseClient";
import authProvider from "./providers/authProvider";

function App() {
  return (
    <BrowserRouter>
      <RefineKbarProvider>
        <Refine
          dataProvider={dataProvider(supabaseClient)}
          authProvider={authProvider}
          routerProvider={routerBindings}
          resources={[
            {
              name: "subscriptions",
              list: "/subscriptions",
              create: "/subscriptions/create",
              edit: "/subscriptions/edit/:id",
              show: "/subscriptions/show/:id",
            },
            {
              name: "payment-methods",
              list: "/payment-methods",
            },
            {
              name: "trials",
              list: "/trials/pending",
            },
          ]}
        >
          {/* Routes */}
        </Refine>
        <RefineKbar />
      </RefineKbarProvider>
    </BrowserRouter>
  );
}

export default App;
```

### 4.2. Supabase Data Provider

```typescript
import { createClient } from "@supabase/supabase-js";

const supabaseUrl = process.env.REACT_APP_SUPABASE_URL!;
const supabaseAnonKey = process.env.REACT_APP_SUPABASE_ANON_KEY!;

export const supabaseClient = createClient(supabaseUrl, supabaseAnonKey);
```

## 5. Metronic Integration

### 5.1. Using Metronic Components

Metronic provides pre-built Tailwind CSS components. You must use these components for consistency:

**Button Example:**

```typescript
export function Button({ children, variant = "primary", ...props }) {
  const baseClasses = "btn";
  const variantClasses = {
    primary: "btn-primary",
    secondary: "btn-secondary",
    success: "btn-success",
  };

  return (
    <button className={`${baseClasses} ${variantClasses[variant]}`} {...props}>
      {children}
    </button>
  );
}
```

**Card Example:**

```typescript
export function Card({ title, children, actions }) {
  return (
    <div className="card">
      <div className="card-header">
        <h3 className="card-title">{title}</h3>
        {actions && <div className="card-toolbar">{actions}</div>}
      </div>
      <div className="card-body">{children}</div>
    </div>
  );
}
```

### 5.2. Metronic Layout

You must use the Metronic layout structure:

```typescript
export function Layout({ children }) {
  return (
    <div className="d-flex flex-column flex-root">
      <div className="page d-flex flex-row flex-column-fluid">
        <Sidebar />
        <div className="wrapper d-flex flex-column flex-row-fluid">
          <Header />
          <div className="content d-flex flex-column flex-column-fluid">
            <div className="container-xxl">{children}</div>
          </div>
          <Footer />
        </div>
      </div>
    </div>
  );
}
```

## 6. Key Pages Implementation

### 6.1. Dashboard

The dashboard must display:

*   Total monthly and annual spending
*   Number of active subscriptions and trials
*   Upcoming renewals (next 7 and 30 days)
*   At-risk subscriptions (card expiring)
*   Quick actions (Add Subscription, Connect Email)

```typescript
import { useList } from "@refinedev/core";
import { Card } from "../../components/common/Card";

export function Dashboard() {
  const { data: subscriptions } = useList({
    resource: "subscriptions",
    filters: [{ field: "status", operator: "eq", value: "active" }],
  });

  const totalMonthlySpend = calculateMonthlySpend(subscriptions?.data || []);

  return (
    <div className="row g-5 g-xl-8">
      <div className="col-xl-3">
        <Card title="Monthly Spend">
          <div className="fs-2x fw-bold">${totalMonthlySpend.toFixed(2)}</div>
        </Card>
      </div>
      {/* More cards... */}
    </div>
  );
}
```

### 6.2. Subscription List

```typescript
import { useTable } from "@refinedev/core";
import { SubscriptionCard } from "../../components/subscription/SubscriptionCard";

export function SubscriptionList() {
  const { tableQueryResult } = useTable({
    resource: "subscriptions",
  });

  const subscriptions = tableQueryResult.data?.data || [];

  return (
    <div className="row g-5">
      {subscriptions.map((sub) => (
        <div key={sub.id} className="col-md-6 col-lg-4">
          <SubscriptionCard subscription={sub} />
        </div>
      ))}
    </div>
  );
}
```

### 6.3. Subscription Form

```typescript
import { useForm } from "@refinedev/react-hook-form";
import { Button } from "../../components/common/Button";

export function SubscriptionForm() {
  const {
    refineCore: { onFinish },
    register,
    handleSubmit,
    formState: { errors },
  } = useForm();

  return (
    <form onSubmit={handleSubmit(onFinish)}>
      <div className="mb-5">
        <label className="form-label">Service Name</label>
        <input
          {...register("name", { required: "Name is required" })}
          className="form-control"
        />
        {errors.name && <span className="text-danger">{errors.name.message}</span>}
      </div>

      <div className="mb-5">
        <label className="form-label">Cost</label>
        <input
          {...register("amount", { required: "Cost is required" })}
          type="number"
          step="0.01"
          className="form-control"
        />
      </div>

      <Button type="submit" variant="primary">
        Save Subscription
      </Button>
    </form>
  );
}
```

### 6.4. Trial Confirmation Page

```typescript
import { useOne, useUpdate } from "@refinedev/core";
import { useParams } from "react-router-dom";

export function TrialConfirmation() {
  const { id } = useParams();
  const { data } = useOne({
    resource: "subscriptions",
    id: id!,
  });

  const { mutate: confirmTrial } = useUpdate();

  const handleConfirm = () => {
    confirmTrial({
      resource: "subscriptions",
      id: id!,
      values: { status: "trial_active" },
    });
  };

  const trial = data?.data;

  return (
    <div className="card">
      <div className="card-body">
        <h2>Confirm Trial</h2>
        <p>We detected a trial for <strong>{trial?.name}</strong></p>
        <p>Trial ends: <strong>{trial?.trial_end_date}</strong></p>
        
        <div className="d-flex gap-3">
          <button onClick={handleConfirm} className="btn btn-primary">
            Confirm & Track
          </button>
          <button className="btn btn-secondary">Dismiss</button>
        </div>
      </div>
    </div>
  );
}
```

## 7. State Management

Refine uses React Query for state management. You should leverage the built-in hooks:

*   `useList()` - Fetch a list of resources
*   `useOne()` - Fetch a single resource
*   `useCreate()` - Create a resource
*   `useUpdate()` - Update a resource
*   `useDelete()` - Delete a resource

## 8. Authentication

### 8.1. Auth Provider

```typescript
import { AuthBindings } from "@refinedev/core";
import { supabaseClient } from "../utility/supabaseClient";

const authProvider: AuthBindings = {
  login: async ({ email, password }) => {
    const { data, error } = await supabaseClient.auth.signInWithPassword({
      email,
      password,
    });

    if (error) {
      return { success: false, error };
    }

    return { success: true };
  },
  logout: async () => {
    const { error } = await supabaseClient.auth.signOut();
    if (error) {
      return { success: false, error };
    }
    return { success: true };
  },
  check: async () => {
    const { data } = await supabaseClient.auth.getSession();
    if (data.session) {
      return { authenticated: true };
    }
    return { authenticated: false };
  },
  getIdentity: async () => {
    const { data } = await supabaseClient.auth.getUser();
    if (data?.user) {
      return data.user;
    }
    return null;
  },
};

export default authProvider;
```

## 9. Responsive Design

All components must be responsive. Use Tailwind's responsive classes:

```typescript
<div className="col-12 col-md-6 col-lg-4">
  {/* Content */}
</div>
```

## 10. Performance Optimization

*   Use React.memo() for expensive components
*   Implement virtualization for long lists (react-window)
*   Lazy load routes using React.lazy()
*   Optimize images with next-gen formats (WebP)

## 11. Accessibility

*   All interactive elements must be keyboard accessible
*   Use semantic HTML elements
*   Include ARIA labels where necessary
*   Ensure sufficient color contrast

## 12. Testing

You must write tests for all components:

```typescript
import { render, screen } from "@testing-library/react";
import { SubscriptionCard } from "./SubscriptionCard";

test("renders subscription name", () => {
  const subscription = {
    id: "1",
    name: "Netflix",
    amount: 15.99,
  };

  render(<SubscriptionCard subscription={subscription} />);
  expect(screen.getByText("Netflix")).toBeInTheDocument();
});
```
