---
name: build-saas
description: Build a complete SaaS product from scratch — marketing site with Saasify + web app with Fireact. Triggers on "build a SaaS", "create a SaaS", "SaaS from scratch", "full SaaS build".
---

# Build SaaS

This skill guides you through building a complete SaaS product using two open-source tools:

- **[Saasify](https://saasify.dev)** — Hugo theme for SaaS marketing websites (landing pages, pricing, blog, docs)
- **[Fireact](https://fireact.dev)** — Framework for SaaS web apps (auth, billing, teams — Firebase + React + Stripe)

Each module works independently. You can build just a marketing site, just a web app, or both together.

## When to Use This Skill

- User wants to build a SaaS product from scratch
- User wants both a marketing site and a web app
- User says "build a SaaS", "create a SaaS", "SaaS from scratch", or "full SaaS build"

For individual modules, use the specific skills directly:
- `build-marketing-site` — marketing site only
- `build-web-app` — web app only

## Instructions

### Step 1: Determine What the User Needs

Ask the user what they want to build:

1. **Marketing site only** — A public-facing website with landing page, pricing, features, blog, docs
2. **Web app only** — A SaaS application with authentication, billing, and team management
3. **Both (full SaaS build)** — Marketing site + web app, linked together

If the user's request makes the answer obvious (e.g., "I need a landing page" → marketing site only), skip the question and proceed.

### Step 2: Route to the Appropriate Skill

- **Marketing site only** → Invoke the `build-marketing-site` skill
- **Web app only** → Invoke the `build-web-app` skill
- **Both** → Invoke `build-marketing-site` first, then `build-web-app`, then proceed to Step 3

### Step 3: Link the Two Products (Full Build Only)

After both modules are complete, bridge them:

1. **Update marketing site CTA URLs** — Edit `hugo.toml` to point "Get Started" and "Sign In" buttons to the web app's signup/login URLs
2. **Update marketing site navigation** — Ensure header buttons link to the web app:
   ```toml
   [params.header.buttons]
     [params.header.buttons.signIn]
       text = "Sign in"
       url = "https://<app-domain>/sign-in"
     [params.header.buttons.getStarted]
       text = "Get Started"
       url = "https://<app-domain>/sign-up"
   ```
3. **Sync pricing** — Verify the marketing site's pricing page matches the Stripe plans configured in the web app
4. **Consistent branding** — Verify both `tailwind.config.js` files use the same brand color palette
5. **Cross-links** — Add "Back to homepage" link in the web app pointing to the marketing site

### Summary

After completion, the user will have:
- A marketing site (Hugo + Saasify) with homepage, pricing, features, blog, and docs
- A web app (React + Firebase + Stripe) with auth, billing, and team management
- Both linked together with consistent branding and navigation
