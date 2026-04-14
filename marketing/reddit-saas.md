# r/SaaS

**Title:** I open-sourced a Claude Code plugin that ships a production-ready SaaS product — marketing site + web app with auth, billing, and teams

---

Every SaaS I've built starts the same way: set up a marketing site, wire up auth, integrate Stripe, add team management. It takes weeks before you write any actual product code.

So I packaged all of that into an open-source Claude Code plugin called Build SaaS.

You install it, tell Claude what your SaaS does, and it builds:

**Marketing site** (Hugo + Saasify theme):
- Homepage with hero, features, testimonials
- Pricing page with plan comparison
- Blog, docs, about page, careers
- SEO-optimized, 90+ Lighthouse, multilingual

**Web app** (React + Firebase + Fireact):
- Auth (email, Google, GitHub)
- Stripe billing with subscription plans
- Team management with invitations and permissions
- Role-based access control
- Firebase backend

If you don't use Claude Code, there's also a written playbook you can follow step by step.

I used this to build EquiRound (equiround.com) — a cap table and ESOP management tool. 57 components, 9 languages, production Stripe billing. From zero to live product.

GitHub: github.com/saasroo/build-saas

It's MIT licensed. Would love to hear what you think and what you'd want added.
