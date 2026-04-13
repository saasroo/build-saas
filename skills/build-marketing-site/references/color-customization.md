# Color Customization

How to customize Saasify's color palette to match brand colors.

## Where to Edit

Edit `tailwind.config.js` in the **site root** (not the one inside the theme directory).

This file was copied from `themes/hugo-saasify-theme/tailwind.config.copy.js` during installation.

## Default Color Configuration

```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./layouts/**/*.html"],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eef1fc',
          100: '#dde3f9',
          200: '#bbc7f3',
          300: '#99abec',
          400: '#778fe6',
          500: '#5573df',
          600: '#425ad6',
          700: '#3548ab',
          800: '#283680',
          900: '#1b2456',
        },
        secondary: {
          50: '#faf5ff',
          100: '#f3e8ff',
          200: '#e9d5ff',
          300: '#d8b4fe',
          400: '#c084fc',
          500: '#a855f7',
          600: '#9333ea',
          700: '#7e22ce',
          800: '#6b21a8',
          900: '#581c87',
        },
      },
      fontFamily: {
        sans: ['Inter', 'system-ui', 'sans-serif'],
        heading: ['Plus Jakarta Sans', 'sans-serif'],
      },
    },
  },
  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography'),
  ],
}
```

## How TailwindCSS Color Palettes Work

Tailwind uses a shade scale from 50 (lightest) to 900 (darkest):

| Shade | Usage |
|-------|-------|
| 50 | Background highlights, hover states |
| 100 | Light backgrounds |
| 200 | Borders, dividers |
| 300 | Disabled states |
| 400 | Placeholder text |
| 500 | Secondary text, icons |
| 600 | **Primary brand color** -- buttons, links, accents |
| 700 | Hover states for buttons |
| 800 | Active/pressed states |
| 900 | Dark text, headings |

## Generating a Color Palette from a Brand Hex

Use the brand color as the **600 shade**, then generate lighter and darker variations.

### Method: Manual Approximation

Given a brand hex (e.g., `#2563eb` for blue):

1. **600** = brand color exactly
2. **500** = slightly lighter (increase lightness ~8%)
3. **400** = lighter still
4. **300** = noticeably lighter
5. **200** = very light
6. **100** = near-white tint
7. **50** = barely-there tint
8. **700** = slightly darker (decrease lightness ~8%)
9. **800** = darker still
10. **900** = very dark

### Common Brand Color Palettes

**Blue (#2563eb)**:
```javascript
primary: {
  50: '#eff6ff',
  100: '#dbeafe',
  200: '#bfdbfe',
  300: '#93c5fd',
  400: '#60a5fa',
  500: '#3b82f6',
  600: '#2563eb',
  700: '#1d4ed8',
  800: '#1e40af',
  900: '#1e3a8a',
},
```

**Green (#16a34a)**:
```javascript
primary: {
  50: '#f0fdf4',
  100: '#dcfce7',
  200: '#bbf7d0',
  300: '#86efac',
  400: '#4ade80',
  500: '#22c55e',
  600: '#16a34a',
  700: '#15803d',
  800: '#166534',
  900: '#14532d',
},
```

**Orange (#ea580c)**:
```javascript
primary: {
  50: '#fff7ed',
  100: '#ffedd5',
  200: '#fed7aa',
  300: '#fdba74',
  400: '#fb923c',
  500: '#f97316',
  600: '#ea580c',
  700: '#c2410c',
  800: '#9a3412',
  900: '#7c2d12',
},
```

**Purple (#9333ea)**:
```javascript
primary: {
  50: '#faf5ff',
  100: '#f3e8ff',
  200: '#e9d5ff',
  300: '#d8b4fe',
  400: '#c084fc',
  500: '#a855f7',
  600: '#9333ea',
  700: '#7e22ce',
  800: '#6b21a8',
  900: '#581c87',
},
```

**Red (#dc2626)**:
```javascript
primary: {
  50: '#fef2f2',
  100: '#fee2e2',
  200: '#fecaca',
  300: '#fca5a5',
  400: '#f87171',
  500: '#ef4444',
  600: '#dc2626',
  700: '#b91c1c',
  800: '#991b1b',
  900: '#7f1d1d',
},
```

**Teal (#0d9488)**:
```javascript
primary: {
  50: '#f0fdfa',
  100: '#ccfbf1',
  200: '#99f6e4',
  300: '#5eead4',
  400: '#2dd4bf',
  500: '#14b8a6',
  600: '#0d9488',
  700: '#0f766e',
  800: '#115e59',
  900: '#134e4a',
},
```

## Gradient Colors

Gradients appear in two places and should be updated to match brand colors:

### 1. CTA Section (hugo.toml)

```toml
[params.cta]
  gradient_from = "#2563eb"  # primary-600
  gradient_to = "#9333ea"    # secondary-600
  gradient_angle = 30
```

### 2. Hero Section (in content/_index.md)

```markdown
{{< hero
    gradient-from="#dbeafe"
    gradient-to="#f3e8ff"
    gradient-angle="180"
>}}
```

Note: Hero gradients typically use lighter shades (100-200) for a subtle background, while CTA gradients use darker shades (600) for a bold, prominent section.

## Font Customization

The default fonts are:
- **Body**: Inter
- **Headings**: Plus Jakarta Sans

To change fonts, update the `fontFamily` section in tailwind.config.js and add the font imports via a custom head partial at `layouts/partials/custom-head.html`.
