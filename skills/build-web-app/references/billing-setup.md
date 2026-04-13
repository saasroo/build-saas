# Billing Setup Reference

This guide covers how to configure Stripe billing in a Fireact application — from creating products in Stripe to configuring webhook handling.

## Stripe Account Setup

1. Create or log into your account at [dashboard.stripe.com](https://dashboard.stripe.com)
2. Make sure you're in **test mode** during development (toggle at top left)
3. Test keys start with `pk_test_` and `sk_test_`
4. Live keys start with `pk_live_` and `sk_live_` — only use these in production

## Creating Products and Prices in Stripe

Each subscription plan in Fireact maps to a Stripe Price.

1. Stripe Dashboard → Products → click "Add product"
2. Enter a product name (e.g., "Pro Plan")
3. Under Pricing, select "Recurring"
4. Set the amount, currency, and billing interval (monthly or yearly)
5. Click "Save product"
6. On the product page, find the Price entry and copy its **Price ID** — it starts with `price_1...`
7. Repeat for each plan

For a free plan, you do not need a Stripe price — see the config section below.

## Configuring `functions/src/config/stripe.config.json`

This file holds your Stripe credentials and subscription plan definitions. It is read by Cloud Functions at runtime.

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
- `currency` — ISO currency code (e.g., `"usd"`)
- `frequency` — billing interval for display: `"month"` or `"year"`
- `features` — array of feature strings shown on the pricing/plan selection UI

After changing this file, rebuild functions:

```bash
cd functions && npm run build && cd ..
```

## Webhook Setup

Fireact's backend handles Stripe webhook events to keep subscription data in sync with Firestore.

### Local Development

Run the Stripe CLI to forward webhooks to your local Functions emulator:

```bash
stripe listen --forward-to http://127.0.0.1:5001/<your-project-id>/us-central1/stripeWebhook
```

The CLI prints a webhook signing secret starting with `whsec_`. Copy it, update `endpointSecret` in `stripe.config.json`, rebuild functions, and restart emulators:

```bash
cd functions && npm run build && cd ..
firebase emulators:start
```

Note: Each time you restart `stripe listen`, a new `whsec_` secret is issued. You must update the config and rebuild whenever the secret changes.

### Production Webhook

1. Stripe Dashboard → Developers → Webhooks → Add endpoint
2. Endpoint URL:
   ```
   https://us-central1-<your-project-id>.cloudfunctions.net/stripeWebhook
   ```
3. Under "Events to send", select (at minimum):
   - `customer.subscription.created`
   - `customer.subscription.updated`
   - `customer.subscription.deleted`
   - `invoice.payment_succeeded`
   - `invoice.payment_failed`
4. Click "Add endpoint"
5. On the endpoint page, reveal the "Signing secret" — it starts with `whsec_`
6. Update `endpointSecret` in `functions/src/config/stripe.config.json`
7. Rebuild and redeploy functions:
   ```bash
   cd functions && npm run build && cd ..
   firebase deploy --only functions
   ```

### Webhook Events Handled

| Event | What happens |
|---|---|
| `customer.subscription.created` | Creates or updates subscription record in Firestore |
| `customer.subscription.updated` | Updates plan, status, and billing details in Firestore |
| `customer.subscription.deleted` | Marks subscription as canceled in Firestore |
| `invoice.payment_succeeded` | Updates subscription status to `active` |
| `invoice.payment_failed` | Updates subscription status to `past_due` or `unpaid` |

All webhook event handling is in `functions/src/functions/stripe.ts`. Stripe's signature is verified before any processing — mismatched signatures are rejected with a 400 response.

## Customer Portal

Fireact integrates with Stripe's hosted customer billing portal, giving users self-service access to:
- View and download invoices
- Update payment method
- Change subscription plan (upgrade/downgrade)
- Cancel subscription

The portal is launched from the app's billing page. Cloud Functions create a portal session using the Stripe API and return a redirect URL. The user is sent to Stripe's hosted portal and redirected back to the app afterward.

To configure the portal (payment methods accepted, cancellation behavior, etc.), go to:
Stripe Dashboard → Settings → Billing → Customer portal

## Testing with Stripe Test Cards

Use these card numbers in test mode. Any future expiry date and any 3-digit CVC work.

| Scenario | Card Number |
|---|---|
| Successful payment | `4242 4242 4242 4242` |
| Requires 3DS authentication | `4000 0025 0000 3155` |
| Card declined | `4000 0000 0000 9995` |

Example: `4242 4242 4242 4242` / `12/29` / `123` / ZIP: `10001`

Always test the full subscription flow (create → invoice paid → webhook received → Firestore updated) before going to production.

## Configuration Change Workflow

Any time you change `stripe.config.json`:

```bash
# Rebuild functions
cd functions && npm run build && cd ..

# For local dev: restart emulators
firebase emulators:start

# For production: redeploy functions
firebase deploy --only functions
```

The config is bundled into the compiled Cloud Functions JavaScript — it is not read at runtime from disk in production.
