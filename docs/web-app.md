# Build a SaaS Web App with Fireact

A step-by-step guide to building a production-ready SaaS application using the [Fireact](https://fireact.dev) open-source framework.

## What You'll Build

A complete SaaS app with:
- User authentication (email/password, Google, GitHub)
- Subscription billing with Stripe
- Team management with invitations and role-based permissions
- Firebase backend (Firestore, Cloud Functions)
- TailwindCSS responsive UI
- One-command deploy to Firebase Hosting

---

## Prerequisites

Make sure you have these installed before starting:

- **Node.js v18+** — Fireact requires Node.js v18 or higher. Check with `node --version`. Install via [nvm](https://github.com/nvm-sh/nvm) (`nvm install 18 && nvm use 18`) or download from [nodejs.org](https://nodejs.org).
- **Firebase CLI** — `npm install -g firebase-tools`, then `firebase login`
- **Stripe CLI** — for local webhook testing:
  ```bash
  # macOS
  brew install stripe/stripe-cli/stripe
  stripe login

  # Windows
  scoop bucket add stripe https://github.com/stripe/scoop-stripe-cli.git
  scoop install stripe

  # Linux: download from https://github.com/stripe/stripe-cli/releases
  ```
- A **Firebase project** on the Blaze (pay-as-you-go) plan — Cloud Functions require Blaze
- A **Stripe account** — start in test mode

---

## Step 1: Plan Your App

Before running the CLI, decide on a few things. The scaffolding process will ask you for them.

**Authentication:**
- Do you want email/password only, or also Google and/or GitHub OAuth?
- If GitHub OAuth: you'll need to create a GitHub OAuth App (covered in Step 5)

**Billing plans:**
- How many plans? (Free + 1–2 paid tiers is most common)
- What are the prices and billing intervals (monthly/yearly)?
- You'll create these in Stripe first and paste the Price IDs into the CLI

**Team support:**
- Fireact treats each subscription as a team workspace by default
- Team members can have different permission levels: `access`, `editor`, `admin`
- The subscription creator is always the owner

Having clear answers now means you can run through the CLI in one pass without stopping.

---

## Step 2: Set Up Firebase

You need a Firebase project with Authentication, Firestore, and Cloud Functions enabled.

### Create a Firebase project

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Click "Add project"
3. Give it a name, follow the prompts
4. Note the **Project ID** — you'll need it throughout this guide

### Enable Firebase services

**Authentication:**
1. Build → Authentication → Get started
2. Sign-in method tab → Enable "Email/Password"
3. (Optional) Enable Google and/or GitHub — see Step 5 for GitHub setup

**Firestore:**
1. Build → Firestore Database → Create database
2. Choose "Start in test mode" for development
3. Pick a region close to your users (you cannot change this later)

**Cloud Functions:**
1. Build → Functions → Get started
2. Upgrade to the **Blaze plan** if prompted — Functions require Blaze

### Register a web app and get the config

1. Project Settings (gear icon) → scroll to "Your apps"
2. Click "Add app" → Web
3. Give it a nickname (e.g., "My SaaS"), click "Register app"
4. The `create-fireact-app` CLI will fetch the SDK config automatically — you just need to select it

---

## Step 3: Set Up Stripe

You need a Stripe account with at least one subscription product and price created.

### Create subscription products and prices

1. Go to [dashboard.stripe.com](https://dashboard.stripe.com) — make sure you're in **test mode** (toggle top left)
2. Products → Add product
3. Give it a name (e.g., "Pro Plan")
4. Under Pricing: choose "Recurring", set the amount and interval (monthly/yearly)
5. Save — on the product page, find the Price entry and copy the **Price ID** (starts with `price_1...`)
6. Repeat for each paid plan

You don't need a Stripe price for a free plan — free plans are handled with a config flag.

### Get API keys

Stripe Dashboard → Developers → API keys:
- **Publishable key**: starts with `pk_test_...`
- **Secret key**: starts with `sk_test_...`

Keep both handy — the CLI will ask for them.

---

## Step 4: Scaffold with create-fireact-app

The `create-fireact-app` CLI scaffolds a complete Fireact project and wires everything together interactively.

### Run the CLI

**Option A: Global install (recommended if you'll create multiple projects)**

```bash
npm install -g create-fireact-app
create-fireact-app my-saas-app
```

**Option B: npx (no install required)**

```bash
npx create-fireact-app my-saas-app
```

### What the CLI prompts you through

1. **Firebase project selection** — lists your Firebase projects; pick the one you just created
2. **Firebase web app selection** — lists web apps in the project; pick your web app; the CLI fetches SDK config automatically
3. **Stripe configuration** — enter your publishable key, secret key, then configure subscription plans with Price IDs one by one
4. **Dependency installation** — installs npm packages for both the React app and Cloud Functions automatically

The scaffolded project includes a Claude Code skill at `.claude/skills/fireact-builder/` for AI-assisted customization.

### Directory structure after scaffolding

```
my-saas-app/
├── .claude/                    # Claude Code skill for AI customization
│   └── skills/
│       └── fireact-builder/
├── src/                        # React application source
│   ├── components/            # UI components
│   ├── contexts/              # React contexts
│   ├── hooks/                 # Custom hooks
│   ├── config/                # Config files
│   │   ├── firebase.config.json   # Firebase SDK credentials + emulator settings
│   │   └── app.config.json        # Social login flags
│   └── i18n/                  # Translations
├── functions/                  # Cloud Functions backend
│   └── src/
│       ├── functions/         # Individual function modules
│       └── config/
│           ├── stripe.config.json  # Stripe keys and plans
│           └── app.config.json     # Permission definitions
├── public/                     # Static assets
├── firebase.json              # Firebase configuration
├── firestore.rules            # Firestore security rules
├── firestore.indexes.json     # Firestore indexes
└── package.json
```

### Post-installation build

After the CLI finishes, build both the React app and the Cloud Functions:

```bash
cd my-saas-app

# Build the React app
npm run build

# Build Cloud Functions
cd functions && npm run build && cd ..
```

Both builds must succeed before starting the emulators.

---

## Step 5: Configure Authentication

### Email/password (already done)

Email/password is enabled by default. No extra steps needed if you already enabled it in Firebase Console.

### Google OAuth

**Step 1: Enable in Firebase Console**

1. Firebase Console → Authentication → Sign-in method tab
2. Click "Google" → toggle "Enable"
3. Set a project support email
4. Save

**Step 2: Update app config**

Open `src/config/app.config.json` and set `google` to `true`:

```json
{
  "socialLogin": {
    "google": true,
    "github": false
  }
}
```

The Google sign-in button appears on the sign-in and sign-up pages automatically.

### GitHub OAuth

GitHub requires an extra step — you need to create a GitHub OAuth App first.

**Step 1: Create a GitHub OAuth App**

1. Go to [github.com/settings/developers](https://github.com/settings/developers)
2. Click "New OAuth App"
3. Fill in:
   - Application name: your app name
   - Homepage URL: your app URL (or `http://localhost:5002` for dev)
   - Authorization callback URL: leave blank for now — get this from Firebase (next step)
4. Click "Register application"
5. Copy the **Client ID** and generate a **Client Secret**

**Step 2: Enable in Firebase Console**

1. Firebase Console → Authentication → Sign-in method
2. Click "GitHub" → toggle "Enable"
3. Paste the Client ID and Client Secret from GitHub
4. Copy the "Authorization callback URL" that Firebase shows — go back to your GitHub OAuth App settings and paste it as the callback URL
5. Save

**Step 3: Update app config**

```json
{
  "socialLogin": {
    "google": false,
    "github": true
  }
}
```

Or enable both Google and GitHub:

```json
{
  "socialLogin": {
    "google": true,
    "github": true
  }
}
```

### RBAC configuration

Permission levels are defined in `functions/src/config/app.config.json`. The defaults cover most use cases:

```json
{
  "permissions": {
    "access": {
      "label": "Access",
      "default": true
    },
    "editor": {
      "label": "Editor",
      "default": false
    },
    "admin": {
      "label": "Admin",
      "default": false
    }
  }
}
```

| Permission | What it grants |
|---|---|
| `access` | Can log in and view the workspace. Given to all members by default. |
| `editor` | Can create and edit content/data within the workspace. |
| `admin` | Full control: manage members, manage billing, all editor permissions. |

The subscription creator is always the **owner** — a special role with all permissions that cannot be removed.

To add a custom permission (e.g., `"billing_viewer"`), add it to this file and rebuild functions:

```json
{
  "permissions": {
    "access": { "label": "Access", "default": true },
    "editor": { "label": "Editor", "default": false },
    "admin": { "label": "Admin", "default": false },
    "billing_viewer": { "label": "View Billing", "default": false }
  }
}
```

```bash
cd functions && npm run build && cd ..
```

After rebuilding, the new permission appears in the invite UI automatically.

---

## Step 6: Configure Billing

### The stripe.config.json file

Billing is configured in `functions/src/config/stripe.config.json`. This file holds your Stripe credentials and plan definitions:

```json
{
  "secretKey": "sk_test_...",
  "publishableKey": "pk_test_...",
  "endpointSecret": "whsec_...",
  "plans": [
    {
      "id": "free",
      "name": "Free",
      "free": true,
      "price": 0,
      "currency": "usd",
      "frequency": "month",
      "features": ["1 user", "Basic features"]
    },
    {
      "id": "starter",
      "name": "Starter",
      "stripePriceId": "price_1ABC...",
      "price": 29,
      "currency": "usd",
      "frequency": "month",
      "features": ["5 users", "All basic features", "Email support"]
    },
    {
      "id": "pro",
      "name": "Pro",
      "stripePriceId": "price_1DEF...",
      "price": 79,
      "currency": "usd",
      "frequency": "month",
      "features": ["Unlimited users", "All features", "Priority support"]
    }
  ]
}
```

**Plan fields:**
- `id` — unique identifier used internally
- `name` — display name shown in the UI
- `free: true` — marks a plan as free; omit `stripePriceId` for free plans
- `stripePriceId` — the `price_...` ID from Stripe; required for paid plans
- `price` — display price (integer, in dollars/major currency unit)
- `currency` — ISO currency code (`"usd"`, `"eur"`, etc.)
- `frequency` — billing interval for display: `"month"` or `"year"`
- `features` — array of feature strings shown in the plan selection UI

After any change to this file, rebuild functions before testing:

```bash
cd functions && npm run build && cd ..
```

### Local webhook setup

Stripe needs to notify your app when subscriptions are created, updated, or invoices paid. In local development, you forward Stripe webhook events to your Functions emulator using the Stripe CLI.

Open a second terminal and run:

```bash
stripe listen --forward-to http://127.0.0.1:5001/<your-project-id>/us-central1/stripeWebhook
```

The CLI prints a webhook signing secret starting with `whsec_`. Copy it and update `endpointSecret` in `stripe.config.json`. Then rebuild functions and restart emulators:

```bash
cd functions && npm run build && cd ..
firebase emulators:start
```

**Note**: Each time you restart `stripe listen`, a new `whsec_` secret is issued. You must update the config and rebuild whenever you restart.

### Test cards

Use these card numbers in Stripe test mode. Any future expiry date, any 3-digit CVC:

| Scenario | Card Number |
|---|---|
| Successful payment | `4242 4242 4242 4242` |
| Requires 3DS authentication | `4000 0025 0000 3155` |
| Card declined | `4000 0000 0000 9995` |

Example: card `4242 4242 4242 4242`, expiry `12/29`, CVC `123`, ZIP `10001`.

### Stripe Customer Portal

Fireact integrates with Stripe's hosted customer portal, giving subscribers self-service access to update payment methods, view invoices, change plans, and cancel. To configure portal behavior (cancellation policy, accepted payment methods, etc.), go to:

Stripe Dashboard → Settings → Billing → Customer portal

---

## Step 7: Configure Team Management

### How teams work

In Fireact, a **subscription is a team workspace**. When a user creates a subscription, they become the owner and can invite others. Every user can belong to multiple subscriptions.

### Firestore data model

```
subscriptions/{subscriptionId}
  name: string                # Team/workspace name
  ownerId: string             # Firebase UID of the creator
  planId: string              # References plans config
  stripeCustomerId: string
  stripeSubscriptionId: string
  status: string              # "active" | "past_due" | "canceled" | "unpaid"
  createdAt: timestamp
  updatedAt: timestamp

subscriptions/{subscriptionId}/users/{userId}
  role: string                # "owner" | "admin" | "member"
  permissions: object         # { access: true, editor: false, admin: false }

users/{userId}/subscriptions/{subscriptionId}
  id: string                  # same as subscriptionId
  role: string
  permissions: object
```

### Invitation flow

1. An admin goes to the team members page (`/{subscriptionId}/users`)
2. They fill in the invitee's email address and select their role/permissions
3. The `createInvite` Cloud Function creates an invite record and sends an invitation email
4. The invitee clicks the invite link, sees the invitation, and accepts or rejects it
5. On acceptance, `acceptInvite` creates the membership records in Firestore

When a member is removed, they lose access immediately. On their next navigation, `ProtectedSubscriptionRoute` redirects them away from the workspace.

### Checking permissions in your components

Use the `useSubscription` hook to check permissions in any component:

```typescript
const { subscription, permissions } = useSubscription();

// Show only to admins
if (permissions?.admin) {
  return <AdminPanel />;
}

// Show only to editors or admins
if (permissions?.editor || permissions?.admin) {
  return <EditButton />;
}

// Show only to users with billing_viewer permission (custom)
if (permissions?.billing_viewer || permissions?.admin) {
  return <BillingReport />;
}
```

### Storing your custom data

Store all subscription-specific data under a subcollection of the subscription document. This makes Firestore security rules simple — you inherit access rules from the parent:

```
# Recommended: subcollection
subscriptions/{subscriptionId}/yourData/{docId}

# Alternative: field-based (harder to secure)
yourCollection/{docId}
  subscriptionId: string
  ...
```

---

## Step 8: Customize Your App

### App name and branding

Update the app name in `package.json` and in `src/i18n/` translation files. For logo and colors, edit the TailwindCSS config and add your logo to `public/`.

The app uses TailwindCSS throughout — you can change the color scheme by adding a custom `primary` color palette in `tailwind.config.js`.

### Adding custom pages

Add new routes in your router file (typically `src/App.tsx` or a routes file). Pages that require authentication wrap with `PrivateRoute`. Pages that require subscription membership wrap with `ProtectedSubscriptionRoute`:

```typescript
// Authenticated page (any signed-in user)
<Route element={<PrivateRoute />}>
  <Route path="/dashboard" element={<Dashboard />} />
</Route>

// Subscription-scoped page (members of a specific subscription)
<Route element={<ProtectedSubscriptionRoute />}>
  <Route element={<SubscriptionLayout />}>
    <Route path="/:subscriptionId/reports" element={<Reports />} />
  </Route>
</Route>
```

---

## Step 9: Test Locally

### Start the Firebase emulators

```bash
firebase emulators:start
```

This starts all services:
- **React app (Hosting)**: http://localhost:5002
- **Auth emulator**: http://localhost:9099
- **Firestore emulator**: http://localhost:8080
- **Functions emulator**: http://localhost:5001
- **Emulator UI**: http://localhost:4000

### Start Stripe webhook forwarding (separate terminal)

```bash
stripe listen --forward-to http://127.0.0.1:5001/<your-firebase-project-id>/us-central1/stripeWebhook
```

If you haven't already, copy the `whsec_` secret printed by this command into `stripe.config.json` → `endpointSecret`, rebuild functions, and restart emulators.

### Testing checklist

Work through this flow to verify everything is wired together:

- [ ] Sign up with email/password — confirm redirect works, email verification prompt shows
- [ ] Verify email (check Auth emulator UI at http://localhost:4000 for the verification link — emulators don't send real emails)
- [ ] Sign in after verification — confirm redirect to subscription list
- [ ] Create a subscription — select a paid plan, complete payment with test card `4242 4242 4242 4242`
- [ ] Confirm in Stripe CLI output that webhook events were received (subscription.created, invoice.payment_succeeded)
- [ ] Confirm subscription appears in Firestore emulator at http://localhost:4000
- [ ] Invite a team member — use a different email address, accept the invite in a separate browser or incognito window
- [ ] Confirm invited member can access the subscription workspace
- [ ] Open the billing portal from the app — confirm it loads Stripe's hosted portal

### Persist emulator data between restarts

By default, emulator data is lost on restart. To persist it:

```bash
# Start with auto-export on exit
firebase emulators:start --export-on-exit=./emulator-data --import=./emulator-data
```

---

## Step 10: Deploy to Production

### Pre-deployment checklist

Before deploying, go through each of these:

1. **Disable emulators** — open `src/config/firebase.config.json` and set:
   ```json
   {
     "emulators": {
       "enabled": false
     }
   }
   ```

2. **Use production Firebase config** — the `firebase` block in `src/config/firebase.config.json` should have your production project credentials

3. **Switch to live Stripe keys** — open `functions/src/config/stripe.config.json` and replace `sk_test_...` and `pk_test_...` with `sk_live_...` and `pk_live_...`

4. **Update the production webhook secret** — your production webhook will have a different `whsec_` than your local dev one (see webhook setup below)

5. **Review Firestore security rules** — ensure `firestore.rules` restricts access appropriately, not test-mode open rules

### Build for production

```bash
# From the project root
npm run build
cd functions && npm run build && cd ..
```

Both must succeed without errors before deploying.

### Deploy to Firebase

```bash
# Deploy everything at once
firebase deploy
```

This deploys the React app (Hosting), Cloud Functions, and Firestore rules and indexes.

For faster iteration, deploy only what changed:

```bash
firebase deploy --only hosting      # React app only
firebase deploy --only functions    # Cloud Functions only
firebase deploy --only firestore    # Rules and indexes only
```

### Set up the production Stripe webhook

After your first deploy, register the production webhook endpoint in Stripe:

1. Stripe Dashboard → Developers → Webhooks → Add endpoint
2. Endpoint URL:
   ```
   https://us-central1-<your-project-id>.cloudfunctions.net/stripeWebhook
   ```
3. Under "Events to send", select at minimum:
   - `customer.subscription.created`
   - `customer.subscription.updated`
   - `customer.subscription.deleted`
   - `invoice.payment_succeeded`
   - `invoice.payment_failed`
4. Click "Add endpoint"
5. Reveal and copy the **Signing secret** (`whsec_...`)
6. Update `functions/src/config/stripe.config.json`:
   ```json
   {
     "endpointSecret": "whsec_your_production_secret_here"
   }
   ```
7. Rebuild and redeploy functions:
   ```bash
   cd functions && npm run build && cd ..
   firebase deploy --only functions
   ```

### Add your production domain to Firebase Auth

For OAuth providers (Google, GitHub) to work in production, add your domain to Firebase Console → Authentication → Settings → Authorized domains.

### CI/CD with GitHub Actions

Create `.github/workflows/deploy.yml` to deploy automatically on every push to `main`:

```yaml
name: Deploy to Firebase

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install dependencies
        run: |
          npm install
          cd functions && npm install

      - name: Build
        run: |
          npm run build
          cd functions && npm run build

      - name: Deploy to Firebase
        uses: w9jds/firebase-action@master
        with:
          args: deploy
        env:
          FIREBASE_TOKEN: ${{ secrets.FIREBASE_TOKEN }}
```

To get the Firebase CI token:

```bash
firebase login:ci
# Follow the browser prompt — copy the token printed to the terminal
```

Then add it to GitHub: repo → Settings → Secrets and variables → Actions → New secret → name `FIREBASE_TOKEN`.

**Note on sensitive config files:** `stripe.config.json` and `firebase.config.json` contain API keys and should not be committed to the repo. In CI, either store the config contents as GitHub secrets and write them to files in a build step, or use a secrets manager.

### Post-deployment verification

After deploying, verify each of these:

- [ ] Hosting URL loads — open `https://<your-project>.web.app`, confirm the page renders
- [ ] Sign up works — create a new account, verify email, sign in
- [ ] Subscription creation works — go through the plan selection and payment flow with a test card
- [ ] Stripe webhooks are firing — Stripe Dashboard → Developers → Webhooks → your endpoint → recent deliveries should show `200` responses
- [ ] Firebase Functions logs show no errors — Firebase Console → Functions → Logs

---

## Troubleshooting

### Installation issues

**`create-fireact-app` command not found after global install**

```bash
# Check where npm installs global packages
npm config get prefix

# Ensure the bin directory is in your PATH
export PATH=~/.npm-global/bin:$PATH   # add to ~/.zshrc or ~/.bashrc

# Or use npx instead
npx create-fireact-app my-app
```

**Node version incompatibility**

```bash
node -v   # must be v18+
nvm install 18 && nvm use 18
```

**npm install permission errors**

```bash
# Do NOT use sudo. Fix npm permissions instead:
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
export PATH=~/.npm-global/bin:$PATH
source ~/.zshrc   # or ~/.bashrc
```

### Firebase emulator issues

**Emulators fail to start**

```bash
# Check for port conflicts
lsof -i :9099   # Auth
lsof -i :8080   # Firestore
lsof -i :5001   # Functions
lsof -i :5002   # Hosting
kill -9 <PID>   # Kill conflicting process

# Build functions first — emulators need compiled code
cd functions && npm run build && cd ..

# Firestore emulator requires Java
java -version   # install if missing

# Clear emulator cache
rm -rf ~/.cache/firebase/emulators
```

**Firebase login fails**

```bash
# Try CI mode (avoids browser redirect issues)
firebase login --no-localhost

# Or clear cache and retry
rm -rf ~/.config/firebase
firebase login
```

**No web apps found in Firebase project**

Register a web app first: Firebase Console → Project Settings → Your apps → Add app → Web.

### Stripe issues

**Webhook signature verification errors**

Each time you restart `stripe listen`, a new `whsec_` secret is issued. Update `endpointSecret` in `stripe.config.json` with the new value, rebuild functions, and restart emulators:

```bash
cd functions && npm run build && cd ..
firebase emulators:start
```

**Payments declining in test mode**

Use the correct test cards:
- `4242 4242 4242 4242` — successful payment
- `4000 0025 0000 3155` — requires 3DS authentication
- `4000 0000 0000 9995` — declined

Any future expiry date, any 3-digit CVC.

**Stripe test mode vs live mode confusion**

Test keys start with `pk_test_` and `sk_test_`. Live keys start with `pk_live_` and `sk_live_`. Always use test keys during development, live keys only in production.

### Build issues

**TypeScript compilation errors**

```bash
# Clean and rebuild
rm -rf node_modules package-lock.json
npm install
rm -rf dist
npm run build
```

**Vite build fails**

```bash
# Clear Vite cache
rm -rf node_modules/.vite
npm run build
```

### Firestore issues

**Permission denied errors**

Check `firestore.rules`. In development, emulators use the local rules file — restart emulators after changing rules. Deploy updated rules with `firebase deploy --only firestore:rules`.

**"The query requires an index" error**

Click the index creation link in the error message — it goes directly to Firebase Console to create the required index. Or deploy indexes from `firestore.indexes.json`:

```bash
firebase deploy --only firestore:indexes
```

### Deployment issues

**Site shows blank page after deploy**

Check `firebase.json` has the `rewrites` rule for React Router:

```json
{
  "hosting": {
    "public": "dist",
    "rewrites": [
      {
        "source": "**",
        "destination": "/index.html"
      }
    ]
  }
}
```

Without the rewrite rule, direct URL access to any route other than `/` will return a 404.

**Functions not working in production but work locally**

Check function logs with `firebase functions:log`. Common causes: missing environment config, IAM permissions, or the functions weren't rebuilt before deploying.

---

## Quick Reference Commands

```bash
# Development
npm run build                        # Build React app
cd functions && npm run build        # Build Cloud Functions
firebase emulators:start             # Start all Firebase emulators
stripe listen --forward-to ...       # Forward Stripe webhooks to local emulator

# Troubleshooting
rm -rf node_modules dist             # Clean build artifacts
npm install                          # Reinstall dependencies
rm -rf node_modules/.vite            # Clear Vite cache
rm -rf ~/.cache/firebase/emulators   # Clear emulator cache

# Emulator data persistence
firebase emulators:start --export-on-exit=./emulator-data --import=./emulator-data

# Cloud Functions
cd functions && npm run build && cd ..    # Build functions
firebase functions:log                   # View function logs
firebase deploy --only functions         # Deploy functions only

# Production deployment
firebase deploy                          # Deploy everything
firebase deploy --only hosting           # Frontend only
firebase deploy --only functions         # Functions only
firebase deploy --only firestore         # Rules and indexes only

# Firebase project management
firebase use --add                       # Add project alias
firebase use staging                     # Switch to staging project
firebase use production                  # Switch to production project
firebase login:ci                        # Get CI token for GitHub Actions
```

---

## Next Steps

- Want a marketing site too? See [Build a SaaS Marketing Site](marketing-site.md)
- Full troubleshooting reference: [skills/build-web-app/references/troubleshooting.md](../skills/build-web-app/references/troubleshooting.md)
- Auth setup reference: [skills/build-web-app/references/auth-setup.md](../skills/build-web-app/references/auth-setup.md)
- Billing setup reference: [skills/build-web-app/references/billing-setup.md](../skills/build-web-app/references/billing-setup.md)
- Deployment reference: [skills/build-web-app/references/deployment.md](../skills/build-web-app/references/deployment.md)
