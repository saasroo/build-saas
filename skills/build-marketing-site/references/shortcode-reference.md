# Shortcode Reference

Quick reference for all 21 shortcodes in the Saasify theme.

## Summary Table

| Shortcode | Type | Data Source | Closing Tag? |
|-----------|------|-------------|-------------|
| `hero` | Inline params | Parameters | No (`>}}`) |
| `hero-image` | Inline params | Parameters | No |
| `section-container` | Wrapper | Inner content | Yes (`{{< /section-container >}}`) |
| `feature` | Inline params | Parameters | No |
| `features-section` | Wrapper | Inner content (features) | Yes |
| `features-list` | Wrapper | Inner content (markdown list) | Yes |
| `benefits-grid` | Wrapper | Inner JSON | Yes |
| `value-card` | Inline params | Parameters | No |
| `testimonials` | Inline params | Page front matter | No |
| `client-logos` | Inline params | Page front matter | No |
| `case-study-card` | Inline params | Parameters | No |
| `pricing-table-1` | Wrapper | Inner JSON | Yes |
| `pricing-table-2` | Wrapper | Inner JSON | Yes |
| `team-member` | Inline params | Parameters | No |
| `investor-logo` | Inline params | Parameters | No |
| `stat` | Inline params | Parameters | No |
| `cta` | Inline params | hugo.toml `[params.cta]` | No |
| `faq` | Wrapper | Inner JSON | Yes |
| `toc` | Standalone | Page headings | No |
| `code` | Wrapper | Inner content | Yes |
| `figure` | Inline params | Parameters | No |

## Hero & Layout

### hero

```markdown
{{< hero
    headline="Build Your Product <span class='text-primary-600'>Faster</span>"
    sub_headline="A description of your product value proposition."
    primary_button_text="Get Started Free"
    primary_button_url="/signup"
    secondary_button_text="View Demo"
    secondary_button_url="/demo"
    hero_image="/images/hero-dashboard.svg"
    gradient-from="#dbeafe"
    gradient-to="#f3e8ff"
    gradient-angle="180"
>}}
```

### hero-image

```markdown
{{< hero-image src="/images/product-screenshot.png" alt="Product Dashboard" >}}
```

### section-container

```markdown
{{< section-container background="bg-gray-50" >}}
## Content Here
Wrapped in consistent section spacing.
{{< /section-container >}}
```

Also accepts `class` parameter for full Tailwind class control:
```markdown
{{< section-container class="bg-gradient-to-b from-blue-50 to-white pt-20 pb-32" >}}
```

## Features & Benefits

### features-section + feature

```markdown
{{< features-section
    title="Why Choose Us"
    description="Everything you need to succeed."
>}}

{{< feature
    title="Feature Name"
    description="Feature description text."
    badge="Category"
    badgeColor="#2563eb"
    image="/images/feature-1.svg"
    buttonText="Learn More"
    buttonLink="/features/name/"
    features="Benefit one,Benefit two,Benefit three"
    imagePosition="right"
>}}

{{< /features-section >}}
```

### features-list

```markdown
{{< features-list title="Key Features" >}}
- Feature one
- Feature two
- Feature three
{{< /features-list >}}
```

### benefits-grid

```markdown
{{< benefits-grid columns="3" >}}
{
  "items": [
    {
      "icon": "clock",
      "title": "Save Time",
      "description": "Automate repetitive tasks"
    },
    {
      "icon": "dollar",
      "title": "Reduce Costs",
      "description": "Cut expenses by up to 50%"
    },
    {
      "icon": "users",
      "title": "Scale Easily",
      "description": "Grow effortlessly"
    }
  ]
}
{{< /benefits-grid >}}
```

### value-card

```markdown
{{< value-card
    icon="heart"
    title="Customer First"
    description="We put customers at the center of everything."
>}}
```

## Social Proof

### testimonials

```markdown
{{< testimonials
    title="Loved by Teams Worldwide"
    description="See what our customers say"
    animate="true"
    background-color="#f1f5f9"
>}}
```

**Data source**: Page front matter `testimonials` array:
```yaml
testimonials:
  - name: "Jane Doe"
    title: "CEO, Company"
    avatar: "/images/avatar.jpg"
    quote: "Amazing product!"
```

### client-logos

```markdown
{{< client-logos animate="true" >}}
```

**Data source**: Page front matter `client_logos` array:
```yaml
client_logos:
  - name: "Company"
    logo: "/images/logos/company.png"
```

### case-study-card

```markdown
{{< case-study-card
    title="How Acme Increased Revenue by 300%"
    company="Acme Corp"
    industry="E-commerce"
    result="300% revenue increase"
    image="/images/case-studies/acme.jpg"
    url="/case-studies/acme"
>}}
```

## Pricing

### pricing-table-1

Three-column pricing table. JSON body with `title`, `description`, and `plans` array.

```markdown
{{< pricing-table-1 >}}
{
  "title": "Choose Your Plan",
  "description": "Start free, upgrade as you grow",
  "plans": [
    {
      "name": "Starter",
      "price": "0",
      "description": "For individuals",
      "featured": false,
      "features": ["Feature 1", "Feature 2"],
      "button": { "text": "Start Free", "url": "/signup" }
    },
    {
      "name": "Pro",
      "price": "49",
      "description": "For teams",
      "featured": true,
      "features": ["Everything in Starter", "Feature 3", "Feature 4"],
      "button": { "text": "Get Started", "url": "/signup?plan=pro" }
    },
    {
      "name": "Enterprise",
      "price": "149",
      "description": "For organizations",
      "featured": false,
      "features": ["Everything in Pro", "Feature 5"],
      "button": { "text": "Contact Sales", "url": "/contact" }
    }
  ]
}
{{< /pricing-table-1 >}}
```

### pricing-table-2

Alternative design. Same JSON structure but uses `highlighted` instead of `featured`.

### faq

```markdown
{{< faq >}}
{
  "title": "Frequently Asked Questions",
  "description": "Common questions answered",
  "questions": [
    {
      "question": "How do I get started?",
      "answer": "Sign up for a free account and follow our guide."
    }
  ]
}
{{< /faq >}}
```

## Team & Company

### team-member

```markdown
{{< team-member
    name="Jane Doe"
    title="CTO"
    image="/images/team/jane.jpg"
    bio="15 years of engineering leadership"
    linkedin="https://linkedin.com/in/janedoe"
    twitter="https://twitter.com/janedoe"
    github="https://github.com/janedoe"
>}}
```

### investor-logo

```markdown
{{< investor-logo name="VC Firm" image="/images/investors/vc.svg" >}}
```

### stat

```markdown
{{< stat number="10,000+" label="Happy Customers" >}}
```

## Content Components

### cta

```markdown
{{< cta >}}
```

Reads configuration from `[params.cta]` in hugo.toml. No parameters needed on the page.

### toc

```markdown
{{< toc >}}
```

Auto-generates table of contents from page headings.

### code

```markdown
{{< code lang="javascript" title="app.js" lineNumbers="true" highlight="2-4" >}}
function greet(name) {
  return `Hello, ${name}!`;
}
{{< /code >}}
```

### figure

```markdown
{{< figure
    src="/images/diagram.png"
    alt="Architecture"
    caption="System architecture overview"
    width="800"
>}}
```

## Critical Rules

1. **JSON shortcodes must use double quotes** -- single quotes cause parse errors
2. **Testimonials data goes in page front matter**, not in the shortcode body
3. **Client logos data goes in page front matter**, not in the shortcode body
4. **CTA reads from hugo.toml** `[params.cta]` -- no inline parameters needed
5. **features-section must have a closing tag** `{{< /features-section >}}`
6. **feature shortcodes go inside features-section** but don't need closing tags
7. **Comma-separated features param** in feature shortcode: `features="A,B,C"`
