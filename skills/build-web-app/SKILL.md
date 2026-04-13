---
name: build-web-app
description: Build a SaaS web application using the Fireact framework (Firebase + React + TypeScript + Stripe). Triggers on "build a web app", "create a SaaS app", "scaffold SaaS application", "fireact app", "SaaS with auth and billing".
---

# Build Web App

This skill guides you through building a complete SaaS web application using the [Fireact](https://fireact.dev) open-source framework. Fireact provides authentication, subscription management, team collaboration, and Stripe billing out of the box — built on Firebase, React, TypeScript, and TailwindCSS.

## When to Use This Skill

- User wants to create a new SaaS web application
- User wants an app with authentication, billing, and team management
- User asks about Fireact or wants to scaffold a Firebase + React + Stripe app
- User wants to add subscription management to a new project

## What This Skill Does

1. **Scaffolds** a complete SaaS app using the `create-fireact-app` CLI
2. **Configures** Firebase (Auth, Firestore, Cloud Functions)
3. **Sets up** Stripe billing with subscription plans
4. **Enables** team management with invitations and permissions
5. **Customizes** branding and UI with TailwindCSS
6. **Deploys** to Firebase Hosting

## How to Use

```
Build me a SaaS app for a project management tool with free and pro plans
```

```
Create a web app with Google auth, Stripe billing at $0/$29/$99, and team support
```

```
Scaffold a Fireact app for my AI writing tool
```

## Instructions

### Step 1: Gather Requirements

Ask the user for the following (provide sensible defaults where possible):

```
Product name:
What the app does (one-line):
Auth providers needed (email/password, Google, GitHub):
Billing plans:
  - Plan name, price, features (for each tier)
Team/multi-user support needed? (yes/no):
Target directory for the project:
```

If the user provides an app description without answering all questions, infer reasonable defaults:
- Default auth: email/password + Google
- Default plans: Free ($0), Pro ($29/mo), Enterprise ($99/mo)
- Default team support: yes
Confirm before proceeding.

### Step 2: Prerequisites

Verify the following are installed:

```bash
# Node.js v18+
node -v

# Firebase CLI
firebase --version
# Install if missing: npm install -g firebase-tools

# Stripe CLI (for webhook testing)
stripe --version
# Install if missing: brew install stripe/stripe-cli/stripe
```

The user also needs:
- A **Firebase project** (create at https://console.firebase.google.com)
  - Enable Authentication (Email/Password + any OAuth providers)
  - Enable Firestore Database
  - Enable Cloud Functions (requires Blaze/pay-as-you-go plan)
- A **Stripe account** (create at https://dashboard.stripe.com)
  - Create products and prices matching the billing plans

See `references/installation.md` for detailed setup instructions.

### Step 3: Scaffold the Project

```bash
# Option A: Global install
npm install -g create-fireact-app
create-fireact-app <project-name>

# Option B: npx (no install needed)
npx create-fireact-app <project-name>
```

The CLI will interactively ask for:
- Firebase project configuration (API key, auth domain, project ID, etc.)
- Stripe configuration (publishable key, secret key, webhook secret)
- Subscription plan details (name, Stripe price ID, features)

After scaffolding:
```bash
cd <project-name>
npm run build && cd functions && npm run build && cd ..
```

See `references/installation.md` for the complete setup flow.

### Step 4: Configure Authentication

The scaffolded app includes authentication out of the box. Configure based on user requirements:

**Email/Password** (enabled by default):
- Sign up, sign in, password reset, email verification all work immediately

**Google OAuth** (if requested):
1. Enable Google sign-in in Firebase Console → Authentication → Sign-in method
2. Set `socialLogin.google` to `true` in `src/config/app.config.json`

**GitHub OAuth** (if requested):
1. Create a GitHub OAuth App at https://github.com/settings/developers
2. Enable GitHub sign-in in Firebase Console → Authentication → Sign-in method
3. Add the GitHub client ID and secret to Firebase

**Role-Based Access Control:**
- Three default permission levels: `access`, `editor`, `admin`
- Configured in `functions/src/config/app.config.json`
- `admin` permission is the highest level and includes all lower permissions

See `references/auth-setup.md` for detailed configuration.

### Step 5: Configure Billing

Set up Stripe subscription plans to match the user's pricing:

1. **Create products in Stripe Dashboard:**
   - Go to Products → Add Product for each plan
   - Create a recurring price for each product
   - Copy the Price ID (e.g., `price_1234...`)

2. **Update Stripe configuration:**
   Edit `functions/src/config/stripe.config.json`:
   ```json
   {
     "key": "sk_test_YOUR_STRIPE_SECRET_KEY",
     "plans": [
       {
         "id": "free",
         "name": "Free",
         "stripePriceId": "",
         "price": 0,
         "currency": "usd",
         "frequency": "month",
         "features": ["Feature 1", "Feature 2"],
         "free": true
       },
       {
         "id": "pro",
         "name": "Pro",
         "stripePriceId": "price_YOUR_PRO_PRICE_ID",
         "price": 29,
         "currency": "usd",
         "frequency": "month",
         "features": ["Everything in Free", "Feature 3", "Feature 4"]
       }
     ]
   }
   ```

3. **Set up webhook forwarding for local development:**
   ```bash
   stripe listen --forward-to http://127.0.0.1:5001/<firebase-project-id>/us-central1/stripeWebhook
   ```
   Copy the webhook signing secret and update `stripe.config.json`.

4. **Rebuild functions after config changes:**
   ```bash
   cd functions && npm run build && cd ..
   ```

See `references/billing-setup.md` for detailed configuration.

### Step 6: Configure Team Management (if needed)

Team management is built into Fireact. If the user needs it:

- **Team creation**: Users create a "subscription" (which acts as a team/workspace)
- **Invitations**: Team owners invite members via email
- **Permissions**: Three levels — `access` (view), `editor` (edit), `admin` (full control)
- **Data isolation**: Each team's data is isolated in Firestore under its subscription ID

No additional configuration is needed for basic team management — it works out of the box after scaffolding.

See `references/team-management.md` for customization options.

### Step 7: Customize Branding

- Edit TailwindCSS styles in the project's CSS files
- Update the app name in `src/config/app.config.json`
- Add a logo and favicon to the public directory
- Customize page routes in `src/config/app.config.json` under `pages`

### Step 8: Test Locally

```bash
# Start Firebase emulators (Auth, Firestore, Functions, Hosting)
firebase emulators:start

# In a separate terminal, forward Stripe webhooks
stripe listen --forward-to http://127.0.0.1:5001/<project-id>/us-central1/stripeWebhook
```

Test the complete flow:
1. Sign up for a new account
2. Create a subscription (select a plan)
3. Complete Stripe checkout (use test card `4242 4242 4242 4242`)
4. Verify dashboard loads with subscription data
5. Invite a team member (if team management is enabled)
6. Test plan upgrade/downgrade
7. Test cancellation

### Step 9: Deploy to Production

1. **Update configuration for production:**
   - Set `emulators.enabled` to `false` in `src/config/firebase.config.json`
   - Use production Stripe API keys in `functions/src/config/stripe.config.json`

2. **Build for production:**
   ```bash
   npm run build
   cd functions && npm run build && cd ..
   ```

3. **Deploy to Firebase:**
   ```bash
   firebase deploy
   ```

4. **Set up production Stripe webhook:**
   - Add webhook endpoint in Stripe Dashboard:
     `https://us-central1-<project-id>.cloudfunctions.net/stripeWebhook`
   - Update the webhook secret in `stripe.config.json` and redeploy functions

See `references/deployment.md` for detailed deployment instructions including CI/CD setup.

## Important Notes

- **Firebase Blaze plan is required** — Cloud Functions need the pay-as-you-go plan (still has a free tier)
- **Stripe webhook secret changes** between local and production — update `stripe.config.json` accordingly
- **Rebuild functions after config changes** — always run `cd functions && npm run build` after editing config files
- **Firestore security rules** must be deployed separately: `firebase deploy --only firestore`
- **The monorepo uses symlinks** — the `packages/` directory contains symlinks to `src/`. Don't break these when editing.
- **Use test mode in Stripe** during development — test card: `4242 4242 4242 4242`, any future expiry, any CVC
- **Troubleshooting**: See `references/troubleshooting.md` for common issues and solutions
