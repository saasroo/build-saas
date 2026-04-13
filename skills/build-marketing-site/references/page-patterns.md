# Page Patterns

Templates for all page types supported by the Saasify theme.

## Pricing Page

**File**: `content/pricing.md`

```markdown
---
title: "Pricing"
description: "Choose the perfect plan for your needs"
layout: "pricing"
---

{{< pricing-table-1 >}}
{
    "title": "Simple, Transparent Pricing",
    "description": "Choose a plan that fits your needs. Upgrade or downgrade at any time.",
    "plans": [
        {
            "name": "Starter",
            "price": "0",
            "description": "Perfect for getting started.",
            "features": [
                "Up to 3 projects",
                "Basic analytics",
                "Community support",
                "1 GB storage"
            ],
            "button": {
                "text": "Start Free",
                "url": "/signup"
            }
        },
        {
            "name": "Professional",
            "price": "49",
            "description": "For growing teams and businesses.",
            "featured": true,
            "features": [
                "Unlimited projects",
                "Advanced analytics",
                "Priority support",
                "50 GB storage",
                "Custom integrations"
            ],
            "button": {
                "text": "Get Started",
                "url": "/signup?plan=pro"
            }
        },
        {
            "name": "Enterprise",
            "price": "149",
            "description": "For large organizations with advanced needs.",
            "features": [
                "Everything in Professional",
                "Dedicated account manager",
                "Unlimited storage",
                "Custom contracts",
                "SLA guarantee"
            ],
            "button": {
                "text": "Contact Sales",
                "url": "/contact"
            }
        }
    ]
}
{{< /pricing-table-1 >}}

{{< faq >}}
{
    "title": "Frequently Asked Questions",
    "description": "Find answers to common questions about our plans.",
    "questions": [
        {
            "question": "Can I try before I buy?",
            "answer": "Yes! Our Starter plan is completely free. You can upgrade to a paid plan at any time."
        },
        {
            "question": "What payment methods do you accept?",
            "answer": "We accept all major credit cards, PayPal, and bank transfers for enterprise customers."
        },
        {
            "question": "Can I cancel anytime?",
            "answer": "Yes, you can cancel your subscription at any time. No questions asked, no hidden fees."
        }
    ]
}
{{< /faq >}}
```

**Important**: JSON must use double quotes. `featured: true` highlights a plan as recommended.

## Company / About Page

**File**: `content/company.md`

```markdown
---
title: "About Our Company"
layout: "company"
description: "Learn about our mission and the team behind the product."
---

{{< section-container class="bg-gradient-to-b from-blue-50 via-blue-50 to-white pt-20 pb-32" >}}
    <div class="text-center">
        <h1 class="text-4xl md:text-5xl font-bold mb-6">Building the Future of [Industry]</h1>
        <p class="text-xl text-gray-600 mb-16">Our mission statement goes here</p>
        <div class="max-w-3xl mx-auto bg-white rounded-xl shadow-sm p-8">
            <h2 class="text-3xl font-bold mb-4">Our Mission</h2>
            <p class="text-xl text-gray-600">
                Mission description paragraph.
            </p>
        </div>
    </div>
{{< /section-container >}}

{{< section-container class="py-20 bg-gray-50" >}}
    <div class="max-w-6xl mx-auto">
        <h2 class="text-3xl font-bold text-center mb-12">Leadership Team</h2>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
            {{< team-member
                name="Jane Doe"
                title="Chief Executive Officer"
                image="/images/company/exec-1.svg"
                linkedin="#"
            >}}
            {{< team-member
                name="John Smith"
                title="Chief Technology Officer"
                image="/images/company/exec-2.svg"
                linkedin="#"
            >}}
            {{< team-member
                name="Alice Johnson"
                title="Chief Product Officer"
                image="/images/company/exec-3.svg"
                linkedin="#"
            >}}
        </div>
    </div>
{{< /section-container >}}

{{< section-container class="py-20" >}}
    <div class="max-w-6xl mx-auto">
        <h2 class="text-3xl font-bold text-center mb-12">Company Values</h2>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
            {{< value-card
                title="Innovation First"
                icon="lightbulb"
                description="We push boundaries and embrace new technologies."
            >}}
            {{< value-card
                title="Customer Success"
                icon="users"
                description="Our customers' success is our success."
            >}}
            {{< value-card
                title="Transparency"
                icon="eye"
                description="Open communication and building trust."
            >}}
        </div>
    </div>
{{< /section-container >}}

{{< section-container class="py-20 bg-gray-50" >}}
    <div class="max-w-6xl mx-auto">
        <div class="grid grid-cols-1 md:grid-cols-4 gap-8 text-center">
            {{< stat number="2024" label="Founded" >}}
            {{< stat number="50+" label="Team Members" >}}
            {{< stat number="1k+" label="Customers" >}}
            {{< stat number="99.9%" label="Uptime" >}}
        </div>
    </div>
{{< /section-container >}}
```

## Feature Page

**File**: `content/features/<feature-slug>.md`

```markdown
---
title: "Feature Name"
description: "Brief description of this feature and its value."
layout: "feature"
badge: "Category"
badgeColor: "#2563eb"
features:
  - title: "Sub-feature One"
    description: "Detailed description of this sub-feature and how it helps users."
  - title: "Sub-feature Two"
    description: "Detailed description of this sub-feature and how it helps users."
  - title: "Sub-feature Three"
    description: "Detailed description of this sub-feature and how it helps users."
  - title: "Sub-feature Four"
    description: "Detailed description of this sub-feature and how it helps users."
demo:
  description: "See this feature in action."
  image: "/images/feature-demo.svg"
---

## Detailed Description

Markdown content providing more detail about the feature. This appears below the structured feature list rendered by the layout.

### Key Benefits

- Benefit one with explanation
- Benefit two with explanation
- Benefit three with explanation

### How It Works

Step-by-step explanation of the feature.
```

## Blog Post

**File**: `content/blog/<post-slug>.md`

```markdown
---
title: "Blog Post Title"
date: 2024-01-15
author: "Author Name"
description: "Meta description for the blog post (150-160 characters)."
categories: ["Category"]
tags: ["tag1", "tag2", "tag3"]
featured_image: "/images/blog/post-image.jpg"
---

{{< toc >}}

## Introduction

Opening paragraph that hooks the reader.

## Main Section

Content with subheadings, lists, code blocks, images, etc.

### Subsection

More detailed content.

## Conclusion

Summary and call to action.
```

## Documentation Page

**Hub**: `content/docs/_index.md`

```markdown
---
title: Documentation
weight: 1
layout: single
---

# Documentation

Welcome to the documentation. This guide covers everything you need to get started.

## Quick Start

1. **[Getting Started](/docs/getting-started/)** - Installation and setup
2. **[Features](/docs/features/)** - Explore all features
3. **[Customization](/docs/customization/)** - Make it your own
4. **[Deployment](/docs/deployment/)** - Go live
```

**Child page**: `content/docs/getting-started.md`

```markdown
---
title: "Getting Started"
weight: 2
---

## Installation

Step-by-step installation guide...
```

The `weight` field controls sidebar ordering. Lower weight = higher position.

## Careers Page

**File**: `content/careers.md`

```markdown
---
title: "Join Our Team"
description: "Be part of a team that's building the future."
layout: "career"

culture_section:
  title: "Our Culture"
  image: "/images/team.jpg"
  image_alt: "Our Team"
  image_caption: "Join our amazing team!"
  values:
    - icon: "star"
      title: "Innovation First"
      description: "We encourage creative thinking and empower our team to push boundaries."
    - icon: "handshake"
      title: "Collaborative Spirit"
      description: "We believe in teamwork and an environment where everyone's voice is heard."
    - icon: "seedling"
      title: "Growth Mindset"
      description: "We invest in development and provide opportunities for continuous learning."

benefits_section:
  title: "Why Join Us?"
  benefits:
    - icon: "heart"
      title: "Health & Wellness"
      description: "Comprehensive health coverage, wellness programs, and mental health support."
    - icon: "target"
      title: "Work-Life Balance"
      description: "Flexible working hours, remote options, and generous PTO policy."
    - icon: "book"
      title: "Learning & Development"
      description: "Professional development budget and regular learning sessions."

positions_section:
  title: "Open Positions"
  view_position_text: "View Position"
---

Company description and culture pitch in markdown body content.
```

Job postings are created as child pages under `content/careers/`:

```markdown
---
title: "Senior Software Engineer"
description: "Join our engineering team to build the next generation of our platform."
layout: "job-single"
department: "Engineering"
location: "Remote"
type: "Full-time"
---

## About the Role

Role description...

## Requirements

- Requirement one
- Requirement two

## Benefits

- Benefit one
- Benefit two
```

## Simple Pages (Privacy, Terms)

**File**: `content/privacy.md` or `content/terms.md`

```markdown
---
title: "Privacy Policy"
layout: "simple"
---

## Introduction

Your privacy policy content in standard markdown...

## Information We Collect

### Personal Information

* Name and email address
* Billing information
* Usage data
```

The `simple` layout provides a clean, full-width content area with no sidebar or special components.
