# Homepage Patterns

Complete template for `content/_index.md` -- the site homepage.

## Full Template

```markdown
---
title: Home
client_logos:
  - name: "Customer 1"
    logo: "/images/logos/customer-1.png"
  - name: "Customer 2"
    logo: "/images/logos/customer-2.png"
  - name: "Customer 3"
    logo: "/images/logos/customer-3.png"
  - name: "Customer 4"
    logo: "/images/logos/customer-4.png"
  - name: "Customer 5"
    logo: "/images/logos/customer-5.png"
testimonials:
  - name: "John Smith"
    title: "CTO at TechStartup"
    avatar: "/images/testimonial-1.svg"
    quote: "We built our SaaS website in record time. The performance is incredible."
  - name: "Sarah Johnson"
    title: "Founder at WebFlow"
    avatar: "/images/testimonial-2.svg"
    quote: "Lightning-fast performance has significantly improved our conversion rates."
  - name: "Michael Chen"
    title: "Lead Developer at CloudTech"
    avatar: "/images/testimonial-3.svg"
    quote: "Professional website with incredibly fast build times and clean code."
---

{{< hero
    headline="Your Headline Here"
    sub_headline="Your subheadline describing the product value proposition in one or two sentences."
    primary_button_text="Get Started Free"
    primary_button_url="/get-started"
    secondary_button_text="View Demo"
    secondary_button_url="/demo"
    hero_image="/images/hero-dashboard.svg"
    gradient-from="#dbeafe"
    gradient-to="#f3e8ff"
    gradient-angle="180"
>}}

{{< client-logos animate="true" >}}

{{< features-section
    title="Key Features Title"
    description="Brief description of what makes your product special."
>}}

{{< feature
    title="Feature One Title"
    description="Description of this feature and its benefits to the user."
    badge="Category"
    badgeColor="#2563eb"
    image="/images/feature-1.svg"
    buttonText="Learn More"
    buttonLink="/features/feature-one/"
    features="Benefit one,Benefit two,Benefit three,Benefit four"
    imagePosition="right"
>}}

{{< feature
    title="Feature Two Title"
    description="Description of this feature and its benefits to the user."
    badge="Category"
    badgeColor="#7c3aed"
    image="/images/feature-2.svg"
    buttonText="Learn More"
    buttonLink="/features/feature-two/"
    features="Benefit one,Benefit two,Benefit three,Benefit four"
    imagePosition="left"
>}}

{{< feature
    title="Feature Three Title"
    description="Description of this feature and its benefits to the user."
    badge="Category"
    badgeColor="#16a34a"
    image="/images/feature-3.svg"
    buttonText="Learn More"
    buttonLink="/features/feature-three/"
    features="Benefit one,Benefit two,Benefit three,Benefit four"
    imagePosition="right"
>}}

{{< /features-section >}}

{{< testimonials
    title="Trusted by Modern Teams"
    description="See what our customers have to say about the product."
    animate="true"
    background-color="#f1f5f9"
>}}

{{< cta >}}
```

## Key Points

### Front Matter Data

- **client_logos**: Array of objects with `name` (string) and `logo` (image path). These are read by the `{{< client-logos >}}` shortcode automatically.
- **testimonials**: Array of objects with `name`, `title`, `avatar` (image path), and `quote`. Read by `{{< testimonials >}}` shortcode from front matter.

### Hero Shortcode

| Parameter | Required | Description |
|-----------|----------|-------------|
| `headline` | Yes | Main headline (supports HTML like `<span class='text-primary-600'>`) |
| `sub_headline` | Yes | Subheading text |
| `primary_button_text` | No | Primary CTA button label |
| `primary_button_url` | No | Primary button link |
| `secondary_button_text` | No | Secondary button label |
| `secondary_button_url` | No | Secondary button link |
| `hero_image` | No | Path to hero image |
| `background_image` | No | Background image (alternative to gradient) |
| `gradient-from` | No | Gradient start color (hex) |
| `gradient-to` | No | Gradient end color (hex) |
| `gradient-angle` | No | Gradient angle in degrees (default: 180) |

### Feature Shortcode

| Parameter | Required | Description |
|-----------|----------|-------------|
| `title` | Yes | Feature title |
| `description` | Yes | Feature description |
| `badge` | No | Badge label text |
| `badgeColor` | No | Badge color (hex) |
| `image` | No | Feature image path |
| `buttonText` | No | Link button text |
| `buttonLink` | No | Link button URL |
| `features` | No | Comma-separated list of bullet points |
| `imagePosition` | No | `"right"` or `"left"` (default: "right") |

### CTA Shortcode

The `{{< cta >}}` shortcode takes no parameters on the homepage -- it reads all configuration from `[params.cta]` in hugo.toml. This ensures consistent CTA across all pages.

### Testimonials Shortcode

| Parameter | Required | Description |
|-----------|----------|-------------|
| `title` | No | Section title |
| `description` | No | Section description |
| `background-color` | No | Background color (hex or Tailwind class) |
| `animate` | No | Enable auto-scroll carousel (default: true) |

Data comes from the page's front matter `testimonials` array, NOT from the shortcode body.

### Alternating Feature Layout

For visual variety, alternate `imagePosition` between `"right"` and `"left"` for consecutive features:
- Feature 1: `imagePosition="right"`
- Feature 2: `imagePosition="left"`
- Feature 3: `imagePosition="right"`

### Badge Colors

Use distinct colors for each feature badge to create visual differentiation:
- Blue: `#2563eb`
- Purple: `#7c3aed`
- Green: `#16a34a`
- Orange: `#ea580c`
- Red: `#dc2626`
- Teal: `#0d9488`
