# Token Output Templates

Three output formats for `design-intelligence`. Always generate all three.

## Format 1: Token Reference Doc

Save to `.planning/design/system/tokens.md`. Human-readable markdown for designers + all downstream commands.

```markdown
# Design Tokens

## Colors
| Role | Token | Value | Usage |
|------|-------|-------|-------|
| Primary | --color-primary | #hex | CTAs, links, active states |
| Primary hover | --color-primary-hover | #hex | Hover/active button state |
| Background | --color-bg | #hex | Page background |
| Surface | --color-surface | #hex | Cards, modals, panels |
| Text | --color-text | #hex | Body text |
| Text secondary | --color-text-secondary | #hex | Captions, helper text |
| Border | --color-border | #hex | Dividers, input borders |
| Success | --color-success | #hex | Success state |
| Warning | --color-warning | #hex | Warning state |
| Error | --color-error | #hex | Error state |

## Typography
- Font family: [name]
- Scale: see typography.md

## Spacing
- Base: 8px
- Scale: xs (4) / sm (8) / md (16) / lg (24) / xl (32) / 2xl (48) / 3xl (64)

## Radius
- Card: [value]
- Button: [value]
- Badge: [value]
- Input: [value]

## Shadows
- sm: [value]
- md: [value]
- lg: [value]
```

## Format 2: Tailwind Config Snippet

```typescript
// tailwind.config.ts → theme.extend
{
  colors: {
    primary: '[value]',
    'primary-hover': '[value]',
    accent: '[value]',
    background: '[value]',
    surface: '[value]',
    text: { DEFAULT: '[value]', secondary: '[value]', muted: '[value]' },
    border: '[value]',
    success: '[value]',
    warning: '[value]',
    error: '[value]',
  },
  fontFamily: {
    sans: ['[font]', 'sans-serif'],
  },
  borderRadius: {
    card: '[value]',
    button: '[value]',
    badge: '[value]',
  },
  boxShadow: {
    card: '[value]',
    elevated: '[value]',
  },
}
```

## Format 3: CSS Variables

```css
:root {
  /* Colors */
  --color-primary: [value];
  --color-primary-hover: [value];
  --color-accent: [value];
  --color-bg: [value];
  --color-surface: [value];
  --color-text: [value];
  --color-text-secondary: [value];
  --color-text-muted: [value];
  --color-border: [value];
  --color-success: [value];
  --color-warning: [value];
  --color-error: [value];

  /* Typography */
  --font-family: '[font]', sans-serif;
  --font-size-h1: 36px;
  --font-size-h2: 30px;
  --font-size-h3: 24px;
  --font-size-body: 16px;
  --font-size-caption: 12px;

  /* Spacing (8px base) */
  --space-xs: 4px;
  --space-sm: 8px;
  --space-md: 16px;
  --space-lg: 24px;
  --space-xl: 32px;
  --space-2xl: 48px;

  /* Radius */
  --radius-card: [value];
  --radius-button: [value];
  --radius-badge: [value];

  /* Shadows */
  --shadow-sm: [value];
  --shadow-md: [value];
  --shadow-lg: [value];
}
```

## Component Inventory by Project Type

Save to `.planning/design/system/components.md`.

### Dashboard / SaaS
Sidebar navigation, Top bar, Stat cards, Data tables, Charts, Modals, Forms, Toast notifications, Dropdown menus, Badges, Avatars, Breadcrumbs

### Marketplace / E-commerce
Product cards, Search bar, Filters, Cart, Checkout form, Reviews, Ratings, Image galleries, Price displays, Category navigation

### Mobile app
Bottom tab bar, Pull-to-refresh, Swipeable cards, Action sheets, Floating action button, List items, Empty states, Onboarding screens

### Landing page
Hero section, Feature grid, Testimonials, Pricing table, CTA buttons, Footer, Navigation, Social proof
