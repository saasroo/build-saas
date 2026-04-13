# Build SaaS

An open-source playbook and [Claude Code](https://claude.ai/code) plugin for building SaaS products from scratch.

Build a complete SaaS with two open-source tools:
- **[Saasify](https://saasify.dev)** — Hugo theme for marketing websites (landing pages, pricing, blog, docs)
- **[Fireact](https://fireact.dev)** — Framework for web apps (auth, billing, teams — Firebase + React + Stripe)

## Two Ways to Use This

### 1. Read the Playbook

Step-by-step guides you can follow in any development environment:

- **[Build a Marketing Site](docs/marketing-site.md)** — Create a SaaS marketing website with Saasify
- **[Build a Web App](docs/web-app.md)** — Scaffold a SaaS application with Fireact

### 2. Install the Claude Code Plugin

If you use [Claude Code](https://claude.ai/code), install this as a plugin for an interactive, guided experience:

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

## Showcase

### EquiRound — Cap Table & Equity Management

[EquiRound](https://equiround.com) is a cap table management, ESOP tracking, and funding round dilution simulation SaaS built entirely with the tools in this playbook:

- **Marketing site** — Built with Hugo + Saasify theme. Includes homepage, solutions pages, pricing, company page, and blog. Multilingual (9 languages).
- **Web app** — Built with React 19 + TypeScript + Firebase + Fireact. Features authentication (Google OAuth + email), Stripe billing, team management with role-based permissions (founder, investor, advisor, employee), and 57 components across cap table, ESOP, simulation, and investor portal modules.

EquiRound demonstrates what you can build by following this playbook — from a marketing site to a full production SaaS app with complex domain logic on top of the Saasify + Fireact foundation.

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
