# Color Palettes by Personality

Reference palettes for `design-intelligence`. Pick the row that matches the project personality, then customize the primary/accent for the brand.

## Dark theme baseline

```
Background:     #0F172A (slate-900 family)
Surface:        rgba(30, 41, 59, 0.5)  (glass effect)
Primary:        [based on personality — see table below]
Primary Hover:  [10% darker than primary]
Accent:         [complementary or analogous — use sparingly, max 10-15%]
Text:           #F8FAFC (near white)
Text Secondary: #94A3B8 (slate-400)
Text Muted:     #64748B (slate-500)
Border:         rgba(148, 163, 184, 0.1)
Success:        #22C55E
Warning:        #F59E0B
Error:          #EF4444
```

## Light theme baseline

```
Background:     #FFFFFF or #F8FAFC
Surface:        #FFFFFF
Primary:        [based on personality]
Primary Hover:  [10% darker than primary]
Accent:         [complementary]
Text:           #0F172A (near black)
Text Secondary: #475569 (slate-600)
Text Muted:     #64748B (slate-500)
Border:         #E2E8F0 (slate-200)
Success:        #16A34A
Warning:        #D97706
Error:          #DC2626
```

## Color selection by personality

| Personality | Primary range | Accent range | Notes |
|-------------|--------------|--------------|-------|
| Professional / Corporate | Blue (#2563EB → #1E40AF) | Slate or Amber | Default for B2B |
| Bold / Creative | Purple (#7C3AED), Pink (#EC4899) | Yellow or Cyan | Use restraint — one bold color, not three |
| Minimal / Technical | Gray (#374151), Black (#111827) | One bright accent | Mono-chrome with a single pop color |
| Warm / Friendly | Orange (#EA580C), Teal (#0D9488) | Amber or Rose | Avoid neon |
| Premium / Luxury | Deep Blue (#1E3A5F), Gold (#B8860B) | Silver or Champagne | Saturation matters — desaturate for elegance |
| Data-heavy | Neutral blue (#3B82F6) | Green for positive, Red for negative | Reserve color for meaning, not decoration |

## Anti-patterns

- **Purple/violet gradient on white**: the AI-generic look. Avoid unless the brand is explicitly playful.
- **Three or more accent colors**: pick one. The "rainbow dashboard" reads as amateurish.
- **Pure black (#000) text on pure white (#FFF)**: too high-contrast, looks harsh. Use #0F172A on #FFFFFF.
- **Color-only state indicators**: red/green color-blind users can't distinguish. Always pair with icon or text.
