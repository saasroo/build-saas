# i18n Setup

Guide for adding multilingual support to a Saasify site.

## hugo.toml Language Configuration

### Basic Setup

```toml
defaultContentLanguage = "en"
defaultContentLanguageInSubdir = false  # true puts English at /en/

[languages]
  [languages.en]
    languageCode = "en-us"
    languageName = "English"
    title = "Your Site"
    weight = 1
    contentDir = "content"

  [languages.es]
    languageCode = "es-es"
    languageName = "Espanol"
    title = "Your Site"
    weight = 2
    contentDir = "content/es"
```

### Language-Specific Parameters

Each language MUST have its own params for: CTA, footer, header buttons, and blog config.

```toml
# English params
[languages.en.params]
  [languages.en.params.cta]
    enable = true
    title = "Ready to Get Started?"
    description = "Join thousands of companies using our platform."
    gradient_from = "#2563eb"
    gradient_to = "#7c3aed"
    gradient_angle = 30
    [languages.en.params.cta.primary_button]
      text = "Get Started Free"
      url = "/get-started"
    [languages.en.params.cta.secondary_button]
      text = "Book Demo"
      url = "/demo"

  [languages.en.params.footer]
    column_1_title = "PRODUCT"
    column_2_title = "COMPANY"
    column_3_title = "LEGAL"

  [languages.en.params.header]
    [languages.en.params.header.buttons]
      [languages.en.params.header.buttons.signIn]
        text = "Sign in"
        url = "/signin"
      [languages.en.params.header.buttons.getStarted]
        text = "Get Started"
        url = "/get-started"

  [languages.en.params.blog]
    enable = true
    title = "Latest Articles"
    subtitle = "Insights and updates"

# Spanish params
[languages.es.params]
  [languages.es.params.cta]
    enable = true
    title = "Listo para empezar?"
    description = "Unete a miles de empresas que usan nuestra plataforma."
    gradient_from = "#2563eb"
    gradient_to = "#7c3aed"
    gradient_angle = 30
    [languages.es.params.cta.primary_button]
      text = "Empezar gratis"
      url = "/es/get-started"
    [languages.es.params.cta.secondary_button]
      text = "Reservar demo"
      url = "/es/demo"

  [languages.es.params.footer]
    column_1_title = "PRODUCTO"
    column_2_title = "EMPRESA"
    column_3_title = "LEGAL"

  [languages.es.params.header]
    [languages.es.params.header.buttons]
      [languages.es.params.header.buttons.signIn]
        text = "Iniciar sesion"
        url = "/es/signin"
      [languages.es.params.header.buttons.getStarted]
        text = "Comenzar"
        url = "/es/get-started"

  [languages.es.params.blog]
    enable = true
    title = "Ultimos articulos"
    subtitle = "Novedades y actualizaciones"
```

### Language-Specific Menus

Each language needs its own main menu AND all three footer menus. Non-default language URLs must include the language prefix.

```toml
# English menus
[[languages.en.menu.main]]
  name = "Features"
  weight = 1
  [languages.en.menu.main.params]
    has_submenu = true
    submenu = [
      { name = "Performance", url = "/features/performance/" },
      { name = "Design", url = "/features/design/" }
    ]

[[languages.en.menu.main]]
  name = "Pricing"
  url = "/pricing"
  weight = 2

[[languages.en.menu.footer_column_1]]
  name = "Performance"
  url = "/features/performance/"
  weight = 1

[[languages.en.menu.footer_column_2]]
  name = "About Us"
  url = "/company"
  weight = 1

[[languages.en.menu.footer_column_3]]
  name = "Privacy Policy"
  url = "/privacy"
  weight = 1

# Spanish menus (note /es/ prefix in all URLs)
[[languages.es.menu.main]]
  name = "Caracteristicas"
  weight = 1
  [languages.es.menu.main.params]
    has_submenu = true
    submenu = [
      { name = "Rendimiento", url = "/es/features/performance/" },
      { name = "Diseno", url = "/es/features/design/" }
    ]

[[languages.es.menu.main]]
  name = "Precios"
  url = "/es/pricing"
  weight = 2

[[languages.es.menu.footer_column_1]]
  name = "Rendimiento"
  url = "/es/features/performance/"
  weight = 1

[[languages.es.menu.footer_column_2]]
  name = "Sobre nosotros"
  url = "/es/company"
  weight = 1

[[languages.es.menu.footer_column_3]]
  name = "Privacidad"
  url = "/es/privacy"
  weight = 1
```

## Translation Files

Create `i18n/<lang>.toml` for each language. Copy from the theme's English default and translate.

```bash
# Copy English translation file
cp themes/hugo-saasify-theme/i18n/en.toml i18n/en.toml

# For other languages, copy the example and translate
cp themes/hugo-saasify-theme/docs/zh-cn.toml.example i18n/<lang>.toml
```

### Key Translation Keys

```toml
# Blog
[readMore]
other = "Read More"

[minRead]
other = "min read"

[previousPost]
other = "Previous Post"

[nextPost]
other = "Next Post"

[dateFormat]
other = "January 2, 2006"

# Sidebar
[recentArticles]
other = "Recent Articles"

[categories]
other = "Categories"

[popularTags]
other = "Popular Tags"

[subscribeNewsletter]
other = "Subscribe to Newsletter"

[emailPlaceholder]
other = "Enter your email"

[subscribe]
other = "Subscribe"

# Navigation
[previous]
other = "Previous"

[next]
other = "Next"

[documentation]
other = "Documentation"

# Pricing
[popular]
other = "Popular"

[mostPopular]
other = "Most Popular"

[perMonth]
other = "/month"

# Homepage
[trustedByCompanies]
other = "Trusted by leading companies"

[lovedByTeams]
other = "Loved by teams worldwide"

# 404
[404Title]
other = "Page Not Found"

[backToHome]
other = "Back to Home"

# Footer
[copyright]
other = "All rights reserved."
```

## Content Directory Structure

```
content/
├── _index.md              # English homepage
├── pricing.md             # English pricing
├── company.md             # English about
├── blog/
│   └── post-1.md          # English blog posts
├── features/
│   └── performance.md     # English features
└── es/                    # Spanish content
    ├── _index.md          # Spanish homepage
    ├── pricing.md         # Spanish pricing
    ├── company.md         # Spanish about
    ├── blog/
    │   └── post-1.md      # Spanish blog posts
    └── features/
        └── performance.md # Spanish features
```

## Checklist for Adding a Language

- [ ] Add `[languages.xx]` block in hugo.toml
- [ ] Add language-specific CTA params
- [ ] Add language-specific footer params (UPPERCASE column titles)
- [ ] Add language-specific header button params
- [ ] Add language-specific blog params
- [ ] Add main navigation menu with language prefix in URLs
- [ ] Add all 3 footer column menus with language prefix in URLs
- [ ] Create `i18n/<lang>.toml` with all translation keys
- [ ] Create `content/<lang>/` directory
- [ ] Create translated homepage `content/<lang>/_index.md`
- [ ] Create translated pages in `content/<lang>/`

## Common Mistakes

1. **Missing footer params** -- footer column titles won't display without `[languages.xx.params.footer]`
2. **Missing URL prefixes** -- non-default language URLs must include `/xx/` prefix
3. **Hardcoded sidebar titles** -- don't set text in global `[params.blog.sidebar]`, let i18n handle it
4. **Incomplete menus** -- all 3 footer menus must be configured per language
5. **Wrong date format** -- each language should have its own `dateFormat` in i18n file

## Language Switcher

The language switcher appears automatically in the header when 2+ languages are configured. No additional setup needed.

## Automatic Language Detection

Optional: add VisitorAPI for auto-detection based on visitor location.

```toml
[params]
  visitorapi_pid = "YOUR_PROJECT_ID"
```

Sign up at visitorapi.com to get a project ID. When configured, first-time visitors are automatically redirected to their detected language.
