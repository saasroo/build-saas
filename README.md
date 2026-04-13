# Build SaaS

An open-source playbook and [Claude Code](https://claude.ai/code) plugin for building SaaS products from scratch.

Build a complete SaaS with two open-source tools:
- **[Saasify](https://saasify.dev)** — Hugo theme for marketing websites (landing pages, pricing, blog, docs)
- **[Fireact](https://fireact.dev)** — Framework for web apps (auth, billing, teams — Firebase + React + Stripe)

## See It in Action

This plugin was used to build [EquiRound](https://equiround.com) — a production SaaS for cap table management, ESOP tracking, and funding round dilution simulation. From zero to a live product with a marketing site and a full web app:

- **Marketing site** — Hugo + Saasify theme with homepage, solutions pages, pricing, company page, blog, and 9 languages
- **Web app** — React 19 + Firebase + Fireact with Google OAuth, Stripe billing, team permissions, and 57 components for cap table, ESOP, simulation, and investor portal

## Two Ways to Use This

Use the **Claude Code plugin** if you want an AI-guided experience — Claude will ask what you're building, make decisions with you, and generate your entire site or app interactively. Use the **playbook** if you prefer to follow a written guide at your own pace, or if you don't use Claude Code.

### 1. Install the Claude Code Plugin (Recommended)

Install as a [Claude Code](https://claude.ai/code) plugin and let Claude build your SaaS with you:

```bash
claude plugin add github:saasroo/build-saas
```

Then tell Claude what you want to build:

```
Build me a SaaS for a project management tool called TaskFlow
```

```
Create a marketing site for my AI writing assistant with pricing at $0/$29/$99
```

```
Scaffold a web app with Google auth, Stripe billing, and team support
```

The plugin includes three skills:
- **`build-saas`** — Orchestrator that guides you through the full SaaS build
- **`build-marketing-site`** — Marketing site builder (Saasify)
- **`build-web-app`** — Web app builder (Fireact)

### 2. Read the Playbook

Step-by-step guides you can follow in any development environment:

- **[Build a Marketing Site](docs/marketing-site.md)** — Create a SaaS marketing website with Saasify
- **[Build a Web App](docs/web-app.md)** — Scaffold a SaaS application with Fireact

## What You'll Build

### Marketing Site (Saasify)

A fully configured Hugo marketing site with:
- Homepage with hero, features, testimonials, and CTA
- Pricing page with plan comparison and FAQ
- About page with team, values, and stats
- Feature pages for each product capability
- Blog with categories, tags, and sidebar
- Documentation hub with sidebar navigation
- SEO-optimized, responsive, 90+ Lighthouse scores
- 21 built-in shortcodes for rapid page building
- Optional multilingual support (i18n)

### Web App (Fireact)

A production-ready SaaS application with:
- Authentication (email/password, Google, GitHub)
- Subscription billing with Stripe (free + paid plans)
- Team management with invitations and permissions
- Role-based access control (access, editor, admin)
- Firebase backend (Firestore, Cloud Functions)
- TailwindCSS responsive UI
- Local development with Firebase emulators
- CI/CD deployment to Firebase Hosting

## Prerequisites

### For Marketing Site
- Hugo Extended v0.80.0+ ([download](https://github.com/gohugoio/hugo/releases))
- Node.js v14+ and npm
- Git

### For Web App
- Node.js v18+
- Firebase CLI (`npm install -g firebase-tools`)
- Stripe CLI (`brew install stripe/stripe-cli/stripe`)
- A Firebase project ([console.firebase.google.com](https://console.firebase.google.com))
- A Stripe account ([dashboard.stripe.com](https://dashboard.stripe.com))

## Project Structure

```
build-saas/
├── docs/                          # Readable playbook guides
│   ├── marketing-site.md          # Marketing site tutorial
│   └── web-app.md                 # Web app tutorial
├── skills/                        # Claude Code skills
│   ├── build-saas/                # Orchestrator skill
│   ├── build-marketing-site/      # Marketing site skill + references
│   └── build-web-app/             # Web app skill + references
└── templates/                     # Starter configuration files
    ├── marketing-site/            # hugo.toml, tailwind.config.js
    └── web-app/                   # firebase.json, .env.example
```

## Credits

- **[Saasify](https://github.com/chaoming/hugo-saasify-theme)** — Open-source Hugo theme for SaaS marketing websites
- **[Fireact](https://github.com/fireact-dev/main)** — Open-source SaaS framework for Firebase + React + Stripe

Built by [Saasroo](https://saasroo.com).

## License

MIT
