# Fireact Architecture Reference

This guide describes the architecture of a Fireact-scaffolded SaaS application. Use it as a reference when customizing the app or understanding how the pieces fit together.

## System Overview

A Fireact app has two main layers:

**Frontend**: A React 19 + TypeScript single-page application built with Vite and styled with TailwindCSS. It runs entirely in the browser and communicates with Firebase services directly for auth and data, and via Cloud Functions for business logic.

**Backend**: Firebase-hosted services — Firebase Authentication for identity, Cloud Firestore as the NoSQL database, and Cloud Functions (Node.js) for server-side business logic including all Stripe interactions.

**External**: Stripe handles all payment processing and subscription lifecycle management. The backend never stores payment card data.

## Tech Stack

| Layer | Technology | Version |
|---|---|---|
| UI framework | React | 19 |
| Language | TypeScript | 5.x |
| Build tool | Vite | Latest |
| Styling | TailwindCSS | Latest |
| Routing | React Router | v7 |
| i18n | i18next | Latest |
| Auth | Firebase Authentication | Latest |
| Database | Cloud Firestore | Latest |
| Backend functions | Firebase Cloud Functions (Node.js) | Node 18 |
| Static hosting | Firebase Hosting | Latest |
| Payments | Stripe | Latest |

## Repository and Monorepo Structure

The Fireact source repository uses a multi-repo structure with Git submodules, but when you scaffold a project with `create-fireact-app`, you get a single self-contained project directory.

Inside the source repository, the `source/` submodule uses a **symlink-based monorepo**:

```
source/
├── src/                    # Actual source code (single source of truth)
│   ├── components/
│   ├── contexts/
│   ├── hooks/
│   ├── config/
│   └── utils/
├── functions/              # Cloud Functions code
│   └── src/
│       └── functions/
├── packages/
│   ├── app/               # NPM package for the React app
│   │   └── src/          # Symlinks pointing to ../../../src/
│   └── functions/         # NPM package for Cloud Functions
│       └── src/          # Symlinks pointing to ../../../functions/src/
└── package.json
```

The symlinks in `packages/app/src/` point back to `../../../src/`:

```bash
components -> ../../../src/components
contexts   -> ../../../src/contexts
hooks      -> ../../../src/hooks
config     -> ../../../src/config
```

This means there is one copy of the code, and both the development environment and the published NPM packages reference it. Changes in `src/` are immediately reflected everywhere without synchronization.

When you scaffold a new app, the CLI pulls from the published packages. Your project will have a straightforward `src/` directory without the monorepo overhead.

## Frontend Architecture

### Directory Structure

```
src/
├── components/
│   ├── auth/              # SignIn, SignUp, ResetPassword, EmailVerification,
│   │                      #   ChangePassword, DeleteAccount
│   ├── common/            # Reusable UI components
│   └── navigation/        # Sidebar, header, nav components
├── contexts/
│   ├── AuthContext.tsx
│   ├── ConfigContext.tsx
│   ├── LoadingContext.tsx
│   └── SubscriptionContext.tsx
├── hooks/
│   ├── useAuth.ts
│   ├── useConfig.ts
│   ├── useLoading.ts
│   └── useSubscription.ts
├── layouts/
│   ├── PublicLayout.tsx         # Wraps unauthenticated pages
│   ├── AuthenticatedLayout.tsx  # Wraps pages requiring login
│   └── SubscriptionLayout.tsx   # Wraps pages requiring a subscription
├── i18n/
│   ├── index.ts
│   └── locales/
│       ├── en/translation.json
│       └── es/translation.json
└── types.ts
```

### React Contexts

State is managed with the React Context API. Four contexts wrap the app in this order:

1. **ConfigContext** — Loads and exposes the app configuration (Firebase config, Stripe publishable key, feature flags, pages). Populated from `src/config/` files at startup.

2. **AuthContext** — Tracks the currently signed-in Firebase user, their profile data from Firestore, and their list of subscriptions. Provides sign-in, sign-out, and profile update methods.

3. **LoadingContext** — A global loading state used to show/hide loading indicators during async operations.

4. **SubscriptionContext** — Holds the currently active subscription (set when a user navigates into a subscription workspace). Exposes subscription data and the user's role/permissions within that subscription.

Each context has a corresponding hook (`useAuth`, `useConfig`, `useLoading`, `useSubscription`) for consuming it in components.

### Routing Structure

The app uses React Router v7 with nested routes and route protection:

```
Public routes (no auth required):
  /sign-in          → SignIn component
  /sign-up          → SignUp component
  /reset-password   → ResetPassword component

Authenticated routes (PrivateRoute — requires Firebase auth):
  /profile          → Profile page
  /create-subscription → Create a new subscription/team

  Subscription routes (ProtectedSubscriptionRoute — requires valid subscription + permissions):
    /:subscriptionId/dashboard  → Subscription dashboard
    /:subscriptionId/settings   → Subscription settings
    /:subscriptionId/billing    → Billing management
    /:subscriptionId/users      → Team member management
```

### Route Protection

**PrivateRoute**: Checks `AuthContext` for a signed-in user. If not authenticated, redirects to `/sign-in`. All user-facing app pages sit inside this route.

**ProtectedSubscriptionRoute**: Sits inside `PrivateRoute`. Checks that the current subscription (from the URL param) exists in the user's subscription list and that the user has the required permissions (at minimum `access: true`). Redirects to the user's subscription list if access is denied.

## Backend Architecture

### Cloud Functions Structure

```
functions/src/
├── functions/
│   ├── subscription/
│   │   ├── createSubscription.ts      # Creates Stripe customer + subscription
│   │   ├── cancelSubscription.ts      # Cancels subscription via Stripe
│   │   └── changeSubscriptionPlan.ts  # Upgrades/downgrades plan
│   ├── billing/
│   │   ├── getBillingDetails.ts       # Fetches billing info from Stripe
│   │   ├── updateBillingDetails.ts    # Updates payment method
│   │   └── getPaymentMethods.ts       # Lists saved payment methods
│   ├── invite/
│   │   ├── createInvite.ts            # Creates invitation record in Firestore
│   │   ├── acceptInvite.ts            # Adds user to subscription on acceptance
│   │   └── rejectInvite.ts           # Marks invite as rejected
│   └── stripe.ts                      # Stripe webhook handler (HTTP function)
├── config/
│   ├── stripe.config.json            # Stripe keys, webhook secret
│   └── app.config.json               # Permission definitions
└── index.ts                           # Exports all functions
```

### Function Types

Most functions are **HTTPS callable functions** — they are called directly from the React app using the Firebase SDK. The SDK handles authentication automatically (the user's ID token is sent with every call). These functions validate the token on the server before executing.

The `stripeWebhook` function is a plain **HTTP function** — it receives POST requests from Stripe's servers and verifies them using Stripe's webhook signature mechanism (not Firebase auth).

## Firestore Data Model

### users collection

```
users/{userId}
  displayName: string
  email: string
  photoURL: string
  subscriptions/           # subcollection
    {subscriptionId}
      id: string           # same as document ID
      role: string         # "owner", "admin", "member"
      permissions: object  # { access: bool, editor: bool, admin: bool, ... }
```

### subscriptions collection

```
subscriptions/{subscriptionId}
  name: string
  ownerId: string                 # Firebase UID of the owner
  planId: string                  # references plans collection
  stripeCustomerId: string
  stripeSubscriptionId: string
  status: string                  # "active", "past_due", "canceled", "unpaid", "incomplete"
  createdAt: timestamp
  updatedAt: timestamp
```

### invites collection

```
invites/{inviteId}
  subscriptionId: string
  email: string
  role: string
  permissions: object
  status: string          # "pending", "accepted", "rejected"
  createdAt: timestamp
```

### plans collection

```
plans/{planId}
  id: string
  name: string
  stripePriceId: string
  currency: string        # "usd", "eur", etc.
  amount: number          # in cents
  features: array         # list of feature strings for display
```

## Security Rules Overview

Firestore security rules enforce role-based access at the database level. Key patterns:

- **subscriptions**: Only members of a subscription can read it. Only the owner can update it. Any authenticated user can create one.
- **users/{userId}/subscriptions**: A user can only read and write their own subscription membership records.
- **invites**: Only the invited email address can accept/reject. Subscription admins/owners can create invites.
- **plans**: Publicly readable (needed to display pricing). Write-protected.

Rules are stored in `firestore.rules` at the project root. Redeploy rules after changes:

```bash
firebase deploy --only firestore:rules
```

## Key Architectural Decisions

**Why Firebase?** Fully managed infrastructure — no servers to provision or maintain. Auth, database, functions, and hosting are integrated and auto-scale. The Firebase free tier covers development and low-traffic production; Blaze (pay-as-you-go) handles scale.

**Why Stripe for payments?** Stripe handles PCI compliance, subscription lifecycle (renewals, failures, dunning), and the customer billing portal. Payment card data never touches your Firestore database.

**Why React Context over Redux?** The app's state requirements are modest and well-defined (auth, config, loading, current subscription). Context with hooks is simpler, has no extra dependencies, and is sufficient for the scale Fireact targets.

**Why symlinks in the monorepo?** A single source of truth for code. Both the development app and the published NPM packages reference the same files, so there is no synchronization step and no risk of drift between what's developed and what's published.

**Why Vite?** Fast cold starts and hot module replacement during development. Efficient production builds with code splitting and tree shaking.
