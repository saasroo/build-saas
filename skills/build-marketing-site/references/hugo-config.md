# hugo.toml Template

Complete single-language configuration template. Replace all `{{PLACEHOLDER}}` values with user-provided data.

```toml
baseURL = "{{BASE_URL}}"
title = "{{SITE_TITLE}}"
theme = "hugo-saasify-theme"

# Enable features
enableEmoji = true
enableGitInfo = true

# Taxonomies (for blog)
[taxonomies]
  category = 'categories'
  tag = 'tags'

# Pagination
[pagination]
  pagerSize = 6
  path = "page"

# =============================================================================
# Site Parameters
# =============================================================================
[params]
  description = "{{SITE_DESCRIPTION}}"
  author = "{{AUTHOR_NAME}}"
  logo = "/images/logo.svg"

  # Analytics (optional)
  # googleAnalytics = "G-XXXXXXXXXX"
  # googleTagManager = "GTM-XXXXXXX"

  # Social Media Links (include only those that apply)
  [params.social]
    # linkedin = "https://linkedin.com/company/{{COMPANY}}"
    # twitter = "https://twitter.com/{{HANDLE}}"
    # github = "https://github.com/{{ORG}}"
    # youtube = "https://youtube.com/@{{CHANNEL}}"
    # facebook = "https://facebook.com/{{PAGE}}"
    # instagram = "https://instagram.com/{{HANDLE}}"

  # Footer Configuration
  [params.footer]
    column_1_title = "PRODUCT"
    column_2_title = "COMPANY"
    column_3_title = "LEGAL"

  # Header Configuration
  [params.header]
    background = "bg-white/80 backdrop-blur-sm"
    border = "border-b border-gray-100"

    [params.header.logo]
      src = "/images/logo.svg"

    [params.header.menu]
      spacing = "space-x-8"

      [params.header.menu.dropdown]
        width = "w-72"
        container_padding = "py-6"
        item_padding = "px-8 py-3"
        background = "bg-white"
        border = "border border-gray-100"
        shadow = "shadow-xl"
        radius = "rounded-lg"
        text_color = "text-gray-700"
        hover_background = "hover:bg-gray-50"
        text_size = "text-sm"

    [params.header.buttons]
      [params.header.buttons.signIn]
        text = "Sign in"
        url = "/signin"
      [params.header.buttons.getStarted]
        text = "{{CTA_BUTTON_TEXT}}"
        url = "{{CTA_BUTTON_URL}}"

  # Blog Configuration
  [params.blog]
    enable = true
    title = "Latest Articles"
    subtitle = "{{BLOG_SUBTITLE}}"

    [params.blog.cta]
      enable = true

    [params.blog.sidebar]
      [params.blog.sidebar.recent]
        enable = true
        count = 5
      [params.blog.sidebar.categories]
        enable = true
      [params.blog.sidebar.tags]
        enable = true
        count = 20

  # Global CTA Configuration
  [params.cta]
    enable = true
    title = "{{CTA_TITLE}}"
    description = "{{CTA_DESCRIPTION}}"
    gradient_from = "{{PRIMARY_COLOR_HEX}}"
    gradient_to = "{{SECONDARY_COLOR_HEX}}"
    gradient_angle = 30

    [params.cta.primary_button]
      text = "{{CTA_PRIMARY_TEXT}}"
      url = "{{CTA_PRIMARY_URL}}"
    [params.cta.secondary_button]
      text = "{{CTA_SECONDARY_TEXT}}"
      url = "{{CTA_SECONDARY_URL}}"

# =============================================================================
# Navigation Menus
# =============================================================================

# Main Navigation
[[menu.main]]
  name = "Features"
  weight = 1
  [menu.main.params]
    has_submenu = true
    submenu = [
      { name = "{{FEATURE_1_NAME}}", url = "/features/{{FEATURE_1_SLUG}}/" },
      { name = "{{FEATURE_2_NAME}}", url = "/features/{{FEATURE_2_SLUG}}/" },
      { name = "{{FEATURE_3_NAME}}", url = "/features/{{FEATURE_3_SLUG}}/" }
    ]

[[menu.main]]
  name = "Pricing"
  url = "/pricing"
  weight = 2

[[menu.main]]
  name = "Docs"
  url = "/docs"
  weight = 3

[[menu.main]]
  name = "Blog"
  url = "/blog"
  weight = 4

[[menu.main]]
  name = "Company"
  weight = 5
  [menu.main.params]
    has_submenu = true
    submenu = [
      { name = "About Us", url = "/company/" },
      { name = "Careers", url = "/careers/" }
    ]

# Footer Column 1 (Product)
[[menu.footer_column_1]]
  name = "{{FEATURE_1_NAME}}"
  url = "/features/{{FEATURE_1_SLUG}}/"
  weight = 1
[[menu.footer_column_1]]
  name = "{{FEATURE_2_NAME}}"
  url = "/features/{{FEATURE_2_SLUG}}/"
  weight = 2
[[menu.footer_column_1]]
  name = "{{FEATURE_3_NAME}}"
  url = "/features/{{FEATURE_3_SLUG}}/"
  weight = 3

# Footer Column 2 (Company)
[[menu.footer_column_2]]
  name = "Blog"
  url = "/blog"
  weight = 1
[[menu.footer_column_2]]
  name = "About Us"
  url = "/company"
  weight = 2
[[menu.footer_column_2]]
  name = "Careers"
  url = "/careers"
  weight = 3

# Footer Column 3 (Legal)
[[menu.footer_column_3]]
  name = "Privacy Policy"
  url = "/privacy"
  weight = 1
[[menu.footer_column_3]]
  name = "Terms of Service"
  url = "/terms"
  weight = 2

# =============================================================================
# Build & Technical Settings (REQUIRED -- do not remove)
# =============================================================================

[module]
  [module.hugoVersion]
    extended = true
    min = "0.80.0"

[build]
  writeStats = true

[build.buildStats]
  enable = true

[security.funcs]
  getenv = ['^HUGO_', '^CI$']

[markup]
  [markup.highlight]
    noClasses = false
    lineNos = true
    codeFences = true
    guessSyntax = true
    lineNumbersInTable = true
  [markup.goldmark.renderer]
    unsafe = true
  [markup.tableOfContents]
    endLevel = 3
    ordered = false
    startLevel = 2
```

## Notes

- The `[build] writeStats = true` section is **required** for TailwindCSS to work. Without it, CSS purging fails and styles won't load.
- The `[module.hugoVersion]` section ensures Hugo Extended is used.
- `[markup.goldmark.renderer] unsafe = true` allows raw HTML in markdown content, which is needed for shortcodes that output HTML.
- For multilingual sites, wrap language-specific params, menus, and CTA config under `[languages.xx]` blocks. See `i18n-setup.md`.
- Footer column titles should be UPPERCASE for visual consistency.
- Dropdown menus use `has_submenu = true` with a `submenu` array of `{ name, url }` objects.
- Simple menu items just need `name`, `url`, and `weight`.
