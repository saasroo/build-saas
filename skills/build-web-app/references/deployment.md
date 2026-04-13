# Deployment Reference

This guide covers deploying a Fireact application to Firebase Hosting and Cloud Functions for production.

## Pre-Deployment Checklist

Before deploying to production, verify each of the following:

1. **Disable emulators** — open `src/config/firebase.config.json` and set:
   ```json
   {
     "emulators": {
       "enabled": false
     }
   }
   ```

2. **Use production Firebase config** — the `firebase` block in `src/config/firebase.config.json` should contain your production project credentials (not an emulator-only project)

3. **Use production Stripe keys** — open `functions/src/config/stripe.config.json` and replace test keys (`sk_test_...`, `pk_test_...`) with live keys (`sk_live_...`, `pk_live_...`)

4. **Update the production webhook secret** — your production Stripe webhook endpoint will have a different `whsec_` secret than your local dev one (see webhook setup below)

5. **Review Firestore security rules** — ensure `firestore.rules` has appropriate production-level restrictions, not test-mode open rules

## Building for Production

Both the React app and the Cloud Functions must be compiled before deployment.

```bash
# From the project root
npm run build
cd functions && npm run build && cd ..
```

Both commands must complete without errors. TypeScript compilation errors will block deployment.

To verify the frontend build output:

```bash
ls -la dist/
# Should show index.html, assets/, etc.
```

## Deploying to Firebase

### Deploy everything at once

```bash
firebase deploy
```

This deploys Hosting (React app), Cloud Functions, and Firestore rules and indexes.

### Deploy specific services

```bash
# React app only (fast — no function build needed)
firebase deploy --only hosting

# Cloud Functions only
firebase deploy --only functions

# Firestore security rules and indexes only
firebase deploy --only firestore

# Multiple services
firebase deploy --only hosting,functions
```

Use selective deployment during iteration to save time — e.g., if you only changed the frontend, deploy only hosting.

## Setting Up Production Stripe Webhook

After deploying Cloud Functions for the first time:

1. Go to Stripe Dashboard → Developers → Webhooks → Add endpoint
2. Enter the endpoint URL:
   ```
   https://us-central1-<your-project-id>.cloudfunctions.net/stripeWebhook
   ```
3. Select the events to listen for (at minimum):
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

## Environment-Specific Deployments

For staging and production environments, use Firebase project aliases.

### Set up project aliases

```bash
firebase use --add
# Select your staging Firebase project, name the alias "staging"

firebase use --add
# Select your production Firebase project, name the alias "production"
```

This creates a `.firebaserc` like:

```json
{
  "projects": {
    "default": "my-project-dev",
    "staging": "my-project-staging",
    "production": "my-project-production"
  }
}
```

### Deploy to a specific environment

```bash
# Switch to staging and deploy
firebase use staging
firebase deploy

# Switch to production and deploy
firebase use production
firebase deploy
```

Each environment has its own Firebase project with its own Firestore, Auth, and Functions. Maintain separate config files per environment or use CI/CD to inject the correct config at build time.

## CI/CD with GitHub Actions

Create `.github/workflows/deploy.yml` in your repository to automatically deploy on every push to `main`.

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

### Getting the Firebase CI token

```bash
firebase login:ci
# Follow the browser prompt to authenticate
# Copy the token printed to the terminal
```

Then add it as a GitHub secret:
1. GitHub repo → Settings → Secrets and variables → Actions
2. Click "New repository secret"
3. Name: `FIREBASE_TOKEN`
4. Value: paste the token from `firebase login:ci`

### Notes on CI config files

Your `stripe.config.json` and `firebase.config.json` contain sensitive keys that should not be committed to the repository. Options for handling this in CI:

- Store config contents as GitHub secrets and write them to files in a build step before `npm run build`
- Use a secrets manager (e.g., Google Secret Manager) and fetch at deploy time
- Use separate config files per environment and `.gitignore` production configs

## Post-Deployment Verification

After every production deployment, verify the following:

1. **Hosting URL loads** — open `https://<your-project>.web.app` (or your custom domain), confirm the page renders
2. **Authentication works** — sign up with a new test account, confirm sign-in redirects correctly
3. **Cloud Functions respond** — complete a test action that triggers a function (e.g., create a subscription)
4. **Firestore connectivity** — confirm data is being written and read correctly
5. **Stripe webhooks fire** — check Stripe Dashboard → Developers → Webhooks → your endpoint → recent deliveries, confirm events are receiving `200` responses
6. **No errors in logs** — Firebase Console → Functions → Logs; check for any exceptions

### Monitoring

- **Firebase Console → Functions → Logs**: real-time Cloud Function execution logs
- **Firebase Console → Analytics**: usage and error tracking (if enabled)
- **Stripe Dashboard → Webhooks**: delivery status and response codes for all webhook events
- **Firebase Console → Firestore → Usage**: read/write metrics

For production error tracking, consider integrating [Sentry](https://sentry.io) or [LogRocket](https://logrocket.com) for frontend errors, and structured logging in Cloud Functions.
