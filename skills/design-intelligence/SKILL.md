---
name: design-intelligence
description: Design system bootstrapping — generate complete tokens (colors, typography, spacing, radius, shadows) for a new project, output as Tailwind config + CSS variables + reference doc. Use when bootstrapping a new project's design system, picking colors/typography for a brand, or generating Tailwind/CSS tokens from project context.
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - Bash
  - WebSearch
---

# Design Intelligence — Token Generation

Generate a complete design system from project context. The reference data (palettes, typography scales, component inventories) lives in supporting files; this is the orchestration plan.

> See `palettes.md` for color schemes by personality and theme.
> See `typography.md` for font pairings, type scale, spacing, and radius/shadow tables.
> See `tokens-reference.md` for the 3 output format templates.

## When to Use

- Setting up a new project's design system (`/spartan:ux:system`)
- Choosing a palette and typography for a specific industry/brand
- Generating tokens to feed into Tailwind config + CSS variables

## Process

### 1. Gather context (skip questions the user already answered)

1. **What are you building?** dashboard / SaaS / marketplace / mobile / landing
2. **Who uses it?** developers / business users / consumers / admins
3. **Personality?** Pick 2-3:
   - Clean / Professional / Corporate
   - Bold / Playful / Creative
   - Minimal / Technical / Developer-focused
   - Warm / Friendly / Approachable
   - Premium / Luxury / Refined
   - Data-heavy / Dense / Information-rich
4. **Light, dark, or both?**
5. **Any brand colors decided?** If yes, use them as primary.
6. **Reference apps?** What apps have the quality you want?

### 2. Pick the palette

Use the personality + theme to look up the right row in `palettes.md`. Customize the primary/accent for the brand color. Don't invent values from scratch — start from the table.

### 3. Pick typography

Look up the pairing in `typography.md` based on personality. Use the standard 16px-base type scale unless there's a specific reason to deviate.

### 4. Pick spacing, radius, shadows

Use the 8px spacing scale (universal). Pick radius and shadow rows from `typography.md` based on personality.

### 5. Generate all 3 outputs

For every project:
1. **Token reference doc** at `.planning/design/system/tokens.md`
2. **Tailwind config snippet** (paste-ready into `tailwind.config.ts`)
3. **CSS variables block** (paste-ready into `globals.css`)

> See `tokens-reference.md` for the templates.

### 6. Generate component inventory

Based on project type (dashboard / marketplace / mobile / landing), list the components needed. Save to `.planning/design/system/components.md`.

> See `tokens-reference.md` § Component Inventory for the type-specific lists.

## Anti-Patterns

### Anti-Pattern: Inventing colors instead of using personality table

```
WRONG: pick "looks nice" #5B7FFF (random)
CORRECT: Professional → look up Blue (#2563EB) from palettes.md, customize for brand
```
The personality tables exist because most teams shouldn't be picking colors from scratch. Start from the table, then nudge.

### Anti-Pattern: Skipping the question pass

```
WRONG: assume "dashboard, professional, dark theme" because it's a SaaS
CORRECT: ask the 6 questions, even briefly, before generating
```
The questions are the only way to know if it's a B2B dashboard (Professional) or a creator tool (Bold). Skipping them produces a generic AI-default look.

### Anti-Pattern: Tailwind defaults under a project token

```
// WRONG — bg-blue-500 in code while a token --color-primary exists
<button className="bg-blue-500">

// CORRECT — bind the token
<button className="bg-primary">
```
Once tokens exist, Tailwind defaults are forbidden. The whole point of the design system is one source of truth.

### Anti-Pattern: Three accent colors

A SaaS dashboard with primary blue, accent pink, and accent green looks like a test page. Pick ONE accent. Use semantic colors (success/warning/error) for state — those don't count as "accents."

## Design System Constraint for Code Generation

When generating UI code and design tokens already exist:

> Read `.planning/design/system/tokens.md` or `.planning/design-config.md` FIRST. Your code MUST use these tokens. Don't use Tailwind defaults, generic colors, or arbitrary spacing.

The chord progression is set. Build beautifully within it.

## Checklist

- [ ] All 6 context questions answered (or explicitly skipped with reason)
- [ ] Palette picked from `palettes.md` (not invented)
- [ ] Typography picked from `typography.md` (not invented)
- [ ] All 3 output formats written: tokens.md, Tailwind, CSS variables
- [ ] Component inventory matches project type
- [ ] Color contrast verified (4.5:1 minimum for body text)
- [ ] `.planning/design-config.md` updated (or created if first time)
