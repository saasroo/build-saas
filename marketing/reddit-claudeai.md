# r/ClaudeAI

**Title:** I built a Claude Code plugin that ships a production-ready SaaS — marketing site + web app from a single conversation

---

I've been using Claude Code to build SaaS products and kept repeating the same setup: marketing site, auth, Stripe billing, team management. So I turned the workflow into a Claude Code plugin.

**How it works:**

```
claude plugin add github:saasroo/build-saas
```

Then just tell Claude what you're building:

```
Build me a SaaS for project management with pricing at $0/$29/$99
```

The plugin has three skills:

- **build-saas** — Asks what you need, routes to the right module, links everything together at the end
- **build-marketing-site** — Builds a Hugo marketing site using the Saasify theme (21 shortcodes, i18n, 90+ Lighthouse scores)
- **build-web-app** — Scaffolds a React + Firebase + Stripe app using the Fireact framework (auth, billing, teams, RBAC)

Each skill gathers your requirements through conversation, then generates the config, pages, and components. If you build both, it links the marketing site CTAs to your app's signup and syncs the branding.

The skills include 14 reference files so Claude has all the context it needs — installation guides, config templates, shortcode references, architecture docs — without hitting external documentation.

**Real-world proof:** I used this plugin to build EquiRound (equiround.com) — a cap table management platform with 57 React components, 9 languages, and Stripe billing.

Open source, MIT licensed: github.com/saasroo/build-saas

Would love feedback from other Claude Code users — what skills would you add?
