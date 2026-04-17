# I Built a Claude Code Plugin That Ships a Production-Ready SaaS Product

Every SaaS I've built starts the same way.

Set up a marketing site. Wire up authentication. Integrate Stripe. Add team management. Handle permissions. Make it multilingual. Deploy it.

None of that is your product. It's infrastructure. And it takes weeks before you write a single line of code that actually matters.

So I packaged all of it into an open-source Claude Code plugin called **Build SaaS**.

{% github saasroo/build-saas %}

## What It Does

You install the plugin, tell Claude what your SaaS does, and it builds two things with you:

### 1. A Marketing Site (Hugo + Saasify)

A complete, production-ready marketing website:

- Homepage with hero section, feature showcase, testimonials, and CTA
- Pricing page with plan comparison and FAQ
- About page with team bios, company values, and stats
- Feature pages for each product capability
- Blog with categories, tags, and sidebar
- Documentation hub with sidebar navigation
- Careers page with job listings
- SEO-optimized, responsive, 90+ Lighthouse scores
- 21 built-in shortcodes for rapid page building
- Multilingual support (i18n) out of the box

Built on [Saasify](https://github.com/chaoming/hugo-saasify-theme), an open-source Hugo theme purpose-built for SaaS sites.

### 2. A Web App (React + Firebase + Fireact)

A full SaaS application with everything wired up:

- Authentication (email/password, Google OAuth, GitHub OAuth)
- Subscription billing with Stripe (free + paid plans)
- Team management with email invitations
- Role-based access control (access, editor, admin)
- Firebase backend (Firestore, Cloud Functions)
- TailwindCSS responsive UI
- Local development with Firebase emulators
- CI/CD deployment to Firebase Hosting

Built on [Fireact](https://github.com/fireact-dev/main), an open-source SaaS framework.

## How It Works

Install the plugin:

```bash
/plugin marketplace add saasroo/build-saas
/plugin install build-saas
```

Then just tell Claude what you want:

```
Build me a SaaS for project management with pricing at $0/$29/$99
```

The plugin includes three skills:

- **`build-saas`** — Orchestrator that asks what you need and guides the full build
- **`build-marketing-site`** — Builds the marketing site step by step
- **`build-web-app`** — Scaffolds and configures the web app

Each skill gathers your requirements (product name, features, pricing, brand colors, auth providers), then generates everything — config files, pages, components, the works.

If you build both, the orchestrator links them together: CTA buttons point to your app's signup, pricing stays in sync with Stripe plans, branding is consistent across both.

## Don't Use Claude Code? No Problem

The repo also includes a written playbook — the same workflows, written as step-by-step tutorials:

- [Build a Marketing Site](https://github.com/saasroo/build-saas/blob/main/docs/marketing-site.md)
- [Build a Web App](https://github.com/saasroo/build-saas/blob/main/docs/web-app.md)

You can follow these guides in any development environment. No AI required.

## We Used It Ourselves

We built [EquiRound](https://equiround.com) — a cap table management, ESOP tracking, and funding round dilution simulation platform — using the skills in this plugin.

- **Marketing site**: Hugo + Saasify with homepage, solutions pages, pricing, company page, blog, and 9 languages
- **Web app**: React 19 + Firebase + Fireact with Google OAuth, Stripe billing, team permissions, and 57 components across cap table, ESOP, simulation, and investor portal modules

From zero to a production SaaS.

## The Tech Stack

Here's what you end up with after a full build:

| Layer | Marketing Site | Web App |
|-------|---------------|---------|
| **Framework** | Hugo Extended | React 19 + TypeScript |
| **Styling** | TailwindCSS | TailwindCSS |
| **Build** | PostCSS + Hugo | Vite |
| **Auth** | — | Firebase Auth (email, Google, GitHub) |
| **Database** | — | Firestore |
| **Backend** | — | Firebase Cloud Functions |
| **Payments** | — | Stripe |
| **Hosting** | Any static host | Firebase Hosting |
| **i18n** | Hugo i18n | i18next |

Everything is open source. The plugin, the theme, the framework. MIT licensed.

## Project Structure

```
build-saas/
├── docs/                          # Readable playbook guides
│   ├── marketing-site.md          # Marketing site tutorial
│   └── web-app.md                 # Web app tutorial
├── skills/                        # Claude Code skills
│   ├── build-saas/                # Orchestrator skill
│   ├── build-marketing-site/      # Skill + 7 reference files
│   └── build-web-app/             # Skill + 7 reference files
└── templates/                     # Starter configuration files
    ├── marketing-site/            # hugo.toml, tailwind.config.js
    └── web-app/                   # firebase.json, .env.example
```

The skills include detailed reference files — installation guides, config templates, shortcode references, architecture docs, troubleshooting guides — so Claude has everything it needs without hitting external docs.

## Why I Built This

I run a portfolio of SaaS products. Every time I started a new one, I spent the first few weeks on the same foundation work. Marketing site. Auth. Billing. Teams. It's necessary but it's not differentiated.

I'd already open-sourced the two tools I use for this — Saasify for marketing sites and Fireact for web apps. But using them still required reading docs, copying config templates, and wiring things together manually.

A Claude Code plugin was the natural next step. Package the knowledge into skills that Claude can execute interactively, and suddenly "start a new SaaS" goes from weeks to a conversation.

## Get Started

```bash
/plugin marketplace add saasroo/build-saas
/plugin install build-saas
```

Or read the playbook: [github.com/saasroo/build-saas](https://github.com/saasroo/build-saas)

I'd love to hear what you think — what's missing, what would make it more useful, what you'd build with it. Open an issue or drop a comment below.

---

*Built by [Saasroo](https://saasroo.com). Powered by [Saasify](https://github.com/chaoming/hugo-saasify-theme) and [Fireact](https://github.com/fireact-dev/main).*
