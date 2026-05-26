# Typography Reference

Font pairings and type scale for `design-intelligence`.

## Font pairing by personality

| Personality | Recommendation | Why |
|-------------|----------------|-----|
| Professional | DM Sans, Plus Jakarta Sans | Clean geometric, good for UI |
| Technical | JetBrains Mono (code) + Inter (UI) | Developer-friendly |
| Creative | Outfit, Space Grotesk | Distinctive but readable |
| Warm | Nunito, Quicksand | Rounded, friendly feel |
| Premium | Playfair Display (headings) + Lato (body) | Elegant contrast |
| Data-heavy | Inter, Roboto Mono (numbers) | Tabular nums, high readability |

## Type scale (16px base)

```
h1:        36px / 700 / 1.2 line-height
h2:        30px / 700 / 1.25
h3:        24px / 600 / 1.3
h4:        20px / 600 / 1.35
h5:        18px / 600 / 1.4
h6:        16px / 600 / 1.4
body:      16px / 400 / 1.6
body-sm:   14px / 400 / 1.5
caption:   12px / 500 / 1.4
```

## Anti-patterns

- **Same weight everywhere**: 400 for everything is flat. Mix 400/500/700 for visual hierarchy.
- **Too many fonts**: pick 1-2 max. A third font is almost always a mistake.
- **Body text under 14px**: hard to read. Mobile body should be 16px.
- **Line height under 1.4 for body**: cramped. Aim 1.5-1.7 for body, 1.2-1.3 for headings.

## Spacing & radius

### Spacing scale (8px base)
```
xs:   4px   (0.25rem)
sm:   8px   (0.5rem)
md:   16px  (1rem)
lg:   24px  (1.5rem)
xl:   32px  (2rem)
2xl:  48px  (3rem)
3xl:  64px  (4rem)
```

### Radius by personality

| Personality | Card | Button | Badge | Input |
|-------------|------|--------|-------|-------|
| Professional | 8px | 6px | 4px | 6px |
| Minimal | 0-4px | 4px | 2px | 4px |
| Friendly | 12-16px | 8px | 9999px | 8px |
| Premium | 12px | 8px | 6px | 8px |

### Shadow by personality

| Personality | Style |
|-------------|-------|
| Professional | Subtle: `0 1px 3px rgba(0,0,0,0.1)` |
| Minimal | None or very subtle borders |
| Friendly | Soft: `0 4px 12px rgba(0,0,0,0.08)` |
| Premium | Layered: `0 1px 2px rgba(0,0,0,0.06), 0 4px 12px rgba(0,0,0,0.08)` |
| Dark theme | Glow: `0 0 20px rgba(primary, 0.15)` |
