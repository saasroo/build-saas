# r/webdev

**Title:** Open-source Claude Code plugin that scaffolds a full SaaS stack (Hugo marketing site + React/Firebase/Stripe web app)

---

Released an open-source plugin for Claude Code that generates two things:

1. A marketing site using Hugo + Saasify theme (21 shortcodes, i18n, 90+ Lighthouse)
2. A web app using React 19 + Firebase + Stripe via Fireact (auth, billing, teams, RBAC)

Tech stack:
- Marketing: Hugo Extended, TailwindCSS, PostCSS
- App: React 19, TypeScript, Vite, Firebase (Auth, Firestore, Cloud Functions), Stripe
- Both: TailwindCSS

You can also just read the playbook docs without Claude Code.

Repo: github.com/saasroo/build-saas

The underlying tools (Saasify and Fireact) are also open source. MIT everything.
