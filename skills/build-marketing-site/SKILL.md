---
name: build-marketing-site
description: Build a SaaS marketing website using the Saasify Hugo theme. Triggers on "build a marketing site", "create a landing page", "SaaS website", "marketing website", "saasify site".
---

# Build Marketing Site

This skill creates a complete SaaS marketing website using the [Saasify](https://saasify.dev) open-source Hugo theme. It handles everything from installation through content creation — producing a fully configured, branded site with homepage, pricing, features, blog, docs, and more using Saasify's 21 built-in shortcodes.

## When to Use This Skill

- User wants to create a new marketing website for a SaaS product
- User wants to build a landing page or marketing site with Hugo
- User asks to install or set up the Saasify theme
- User wants to customize an existing Saasify site
- User needs to add pages (pricing, about, features, blog) to a Saasify site

## What This Skill Does

1. **Scaffolds** a complete Hugo site with the Saasify theme installed
2. **Configures** hugo.toml with branding, navigation, CTA, footer, and social links
3. **Customizes** TailwindCSS color palettes to match brand colors
4. **Creates** homepage with hero, features, testimonials, client logos, and CTA
5. **Generates** additional pages: pricing, about/company, features, blog, docs, careers
6. **Sets up** i18n for multilingual sites when requested
7. **Prepares** static asset directory structure with placeholder notes

## How to Use

```
Build me a marketing site for my project management tool called TaskFlow
```

```
Create a SaaS website for an AI writing assistant with pricing at $0/$29/$99
```

```
Install the Saasify theme and set up a landing page for my API product
```

## Instructions

### Step 1: Gather Requirements

Ask the user for the following (provide sensible defaults where possible):

```
Product name:
One-line description:
Target audience:
Key features (3-6):
Pricing tiers (name, price, features for each):
Brand colors (primary hex, secondary hex):
Pages needed (homepage, pricing, about, features, blog, docs, careers):
CTA text and URL:
Social media URLs:
Google Analytics ID (optional):
Languages needed (default: English only):
Target directory for the site:
```

If the user provides a product description without answering all questions, infer reasonable defaults and confirm before proceeding. For example, derive features from the description, suggest standard SaaS pricing tiers, and use common blue/purple as default brand colors.

### Step 2: Install Saasify

Create the Hugo site and install the theme. Follow the exact steps in `references/installation.md`.

Key commands:
```bash
hugo new site <site-name>
cd <site-name>
git init
git submodule add https://github.com/chaoming/hugo-saasify-theme.git themes/hugo-saasify-theme
git submodule update --init --recursive
cp themes/hugo-saasify-theme/package.json .
cp themes/hugo-saasify-theme/postcss.config.js .
cp themes/hugo-saasify-theme/tailwind.config.copy.js ./tailwind.config.js
npm install
```

### Step 3: Configure hugo.toml

Build a complete `hugo.toml` from the user's requirements. Use the template in `references/hugo-config.md` as the base structure.

Key sections to customize from user input:
- `baseURL`, `title` — product name and domain
- `[params]` — description, author, logo path, analytics IDs
- `[params.header]` — logo, menu dropdown styling, sign-in/get-started buttons
- `[params.footer]` — column titles matching the site's page structure
- `[params.cta]` — CTA title, description, gradient colors, button text/URLs
- `[params.blog]` — enable if blog is requested
- `[params.social]` — social media URLs
- `[[menu.main]]` — navigation items matching requested pages
- `[[menu.footer_column_1/2/3]]` — footer navigation links
- `[build]` — must include `writeStats = true`
- `[module]` — must require Hugo Extended >= 0.80.0

### Step 4: Customize Brand Colors

Edit `tailwind.config.js` in the site root to set the user's brand colors. Follow the palette generation guide in `references/color-customization.md`.

- Set the user's primary brand color as the `600` shade
- Generate a full 50-900 palette around it
- Do the same for the secondary color
- Also update gradient colors in hugo.toml CTA config and hero shortcode calls

### Step 5: Create Homepage

Build `content/_index.md` following the patterns in `references/homepage-patterns.md`.

The homepage should include:
1. **Front matter** — title, client_logos array, testimonials array
2. **Hero shortcode** — headline, sub_headline, buttons, hero_image, gradient colors
3. **Client logos shortcode** — `{{< client-logos animate="true" >}}`
4. **Features section** — `{{< features-section >}}` wrapping 3+ `{{< feature >}}` shortcodes
5. **Testimonials** — `{{< testimonials >}}` (reads from front matter)
6. **CTA** — `{{< cta >}}` (reads from hugo.toml config)

Important:
- Testimonial data goes in front matter YAML, not in the shortcode call
- Client logo data goes in front matter YAML
- Feature shortcodes use comma-separated `features` param for bullet lists
- Alternate `imagePosition="right"` and `"left"` for features

### Step 6: Create Additional Pages

Create each requested page following the templates in `references/page-patterns.md`.

**Pricing** (`content/pricing.md`):
- `layout: "pricing"` in front matter
- `{{< pricing-table-1 >}}` with JSON plan data (double quotes required)
- Optional `{{< faq >}}` with JSON Q&A data
- `featured: true` on the recommended plan

**About/Company** (`content/company.md`):
- `layout: "company"` in front matter
- `{{< section-container >}}` for each section
- `{{< team-member >}}` shortcodes for leadership
- `{{< stat >}}` shortcodes for key metrics
- `{{< value-card >}}` for company values

**Features** (`content/features/<name>.md`):
- `layout: "feature"` in front matter
- `features` array in front matter with title/description pairs
- `badge` and `badgeColor` in front matter
- Markdown body for detailed content

**Blog** (`content/blog/<slug>.md`):
- Front matter: title, date, author, description, categories, tags, featured_image
- `{{< toc >}}` at the top for table of contents
- Standard markdown body

**Documentation** (`content/docs/_index.md` + child pages):
- `weight` in front matter controls sidebar order
- Child pages auto-populate the docs sidebar

**Careers** (`content/careers.md`):
- `layout: "career"` in front matter
- `culture_section`, `benefits_section`, `positions_section` in front matter

**Simple pages** (`content/privacy.md`, `content/terms.md`):
- `layout: "simple"` in front matter
- Standard markdown body

### Step 7: Static Assets

Create the directory structure for images:

```bash
mkdir -p static/images/logos
mkdir -p static/images/blog
mkdir -p static/images/features
mkdir -p static/images/company
mkdir -p static/images/team
```

Note to the user which placeholder images are needed:
- `static/images/logo.svg` — site logo
- `static/images/hero-dashboard.svg` — hero section image
- `static/images/feature-1.svg` through `feature-N.svg` — feature images
- `static/images/blog/` — blog post featured images
- Client logo images in `static/images/logos/`
- Team member photos in `static/images/company/` or `static/images/team/`
- Testimonial avatars

### Step 8: i18n Setup (if multilingual)

Only if the user requests multiple languages. Follow `references/i18n-setup.md`.

Key steps:
1. Add language blocks to hugo.toml (`[languages.xx]`)
2. Add language-specific params (CTA, header buttons, footer titles, blog config)
3. Add language-specific menus (main + 3 footer columns) with URL prefixes
4. Create `i18n/<lang>.toml` translation files
5. Create `content/<lang>/` directory with translated content
6. Footer column titles must be UPPERCASE

### Step 9: Preview

Start the development server:

```bash
npm start
```

This runs TailwindCSS compilation + Hugo server. The site will be at `http://localhost:1313`.

**Do NOT use `hugo server` directly** — it won't compile TailwindCSS and styles will be missing.

## Important Notes

- **Always use `npm start`** (not `hugo server`) for development — TailwindCSS requires the npm build pipeline
- **`writeStats = true`** is required in hugo.toml `[build]` section for TailwindCSS purging to work
- **JSON shortcodes require double quotes** — single quotes will cause parse errors in pricing-table, faq, benefits-grid, and client-logos shortcodes
- **Testimonials go in front matter**, not in the shortcode body — the `{{< testimonials >}}` shortcode reads from the page's YAML front matter
- **Client logos go in front matter** as a YAML array with `name` and `logo` fields
- **Hugo Extended is required** — the standard Hugo binary won't work because TailwindCSS needs the extended CSS processing pipeline
- **Shortcode reference**: See `references/shortcode-reference.md` for all 21 shortcodes with their parameters
- When generating content, write keyword-rich headlines, compelling CTAs, and benefit-focused copy
