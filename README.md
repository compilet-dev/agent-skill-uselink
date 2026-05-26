# Production Agent Skills

Production-tested agent skills for Claude Code, extracted from real Kotlin/Micronaut + React SaaS codebases. Every anti-pattern comes from a real bug, failed PR review, or production incident.

## Table of Contents

- [Installation](#installation)
- [Available Skills](#available-skills)
- [Typical Workflows](#typical-workflows)
- [How Skills Work](#how-skills-work)
- [Skill Reference](#skill-reference)
- [Contributing](#contributing)
- [License](#license)

## Installation

### Via `npx skills` (Recommended)

```bash
# Install all skills
npx skills add hieuvo/claude-skills

# Install a specific skill
npx skills add hieuvo/claude-skills --skill api-endpoint-creator

# List available skills
npx skills add hieuvo/claude-skills --list

# Install globally (available in all projects)
npx skills add hieuvo/claude-skills -g
```

### Via Claude Code (Manual)

```bash
# Clone and register in your project's CLAUDE.md
git clone https://github.com/hieuvo/claude-skills.git
```

Or install from the marketplace manifest:

```bash
claude skills install ./marketplace.json
```

Then copy the skills you need:

```bash
# All skills
cp -R skills/* /path/to/your-project/.claude/skills/

# Single skill
cp -R skills/api-endpoint-creator /path/to/your-project/.claude/skills/
```

## Available Skills

| Skill | Path | What it does |
|-------|------|-------------|
| API Endpoint Creator | `skills/api-endpoint-creator` | Scaffolds a complete RPC-style endpoint: Controller → Manager → Repository → Tests |
| Backend API Design | `skills/backend-api-design` | Designs RPC-style APIs with layered architecture (Controller → Manager → Repository) |
| Kotlin Best Practices | `skills/kotlin-best-practices` | Kotlin coding standards — null safety, Either error handling, Exposed ORM, coroutines |
| Database Patterns | `skills/database-patterns` | Database schema design, migrations, soft deletes, and Exposed ORM patterns |
| Database Table Creator | `skills/database-table-creator` | Creates database table with full Kotlin sync (SQL → Table → Entity → Repo → Tests) |
| Testing Strategies | `skills/testing-strategies` | Testing patterns for Micronaut/Kotlin: repository tests, integration tests, test data builders |
| Security Checklist | `skills/security-checklist` | Security audit: auth, authz, input validation, IDOR, secrets, response sanitization |
| Design Intelligence | `skills/design-intelligence` | Bootstraps a design system: tokens, typography, spacing, Tailwind config, CSS variables |
| Design Workflow | `skills/design-workflow` | Anti-AI-generic design guidelines — make UI that doesn't look AI-generated |
| Browser QA | `skills/browser-qa` | Runs real browser QA with Playwright — finds bugs users would find |
| Web to PRD | `skills/web-to-prd` | Scans a live web app, extracts features, generates PRD with epics/stories/priorities |
| Article Writing | `skills/article-writing` | Writes blog posts, guides, and tutorials that sound human, not AI |
| Content Engine | `skills/content-engine` | Turns one idea into platform-native content for X, LinkedIn, TikTok, YouTube, newsletters |
| Investor Outreach | `skills/investor-outreach` | Drafts cold emails, warm intro blurbs, and follow-ups for investors |
| Investor Materials | `skills/investor-materials` | Creates pitch decks, one-pagers, memos, and financial models |

## Typical Workflows

### 1. Build a new backend feature end-to-end

1. Use **database-table-creator** to generate the SQL migration, Table, Entity, and Repository
2. Use **api-endpoint-creator** to scaffold Controller → Manager → Repository with tests
3. Use **security-checklist** to audit the new endpoint for auth, input validation, and IDOR
4. Use **testing-strategies** to add integration tests via AbstractControllerTest

### 2. Design and build a frontend feature

1. Use **design-intelligence** to generate design tokens (colors, typography, spacing)
2. Use **design-workflow** to apply anti-AI-generic guidelines to UI components
3. Use **browser-qa** to run Playwright QA and catch visual bugs before PR

### 3. Reverse-engineer a competitor product

1. Use **web-to-prd** to scan a live web app and extract all features
2. Use **backend-api-design** to design your API contract for the same feature set
3. Use **database-patterns** to design the schema

### 4. Prepare for fundraising

1. Use **investor-materials** to create a pitch deck and one-pager
2. Use **investor-outreach** to draft personalized cold emails and follow-ups
3. Use **content-engine** to turn your pitch into launch content across platforms

### 5. Write and publish content

1. Use **article-writing** to draft a blog post or tutorial
2. Use **content-engine** to adapt it for X, LinkedIn, TikTok, and newsletters

## How Skills Work

Each skill is a folder containing a `SKILL.md` file with YAML frontmatter and structured instructions. Some skills include supporting files (code templates, examples, checklists).

### Skill structure

```
skills/
  skill-name/
    SKILL.md              # Main skill definition (frontmatter + instructions)
    code-patterns.md      # Code templates and examples (optional)
    examples.md           # Good/bad pattern comparisons (optional)
    checklists.md         # Review checklists (optional)
```

### SKILL.md format

```yaml
---
name: skill-name
description: "What it does. Use when [trigger conditions]."
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
---

# Skill Title

## When to Use
- Trigger condition 1
- Trigger condition 2

## Rules / Patterns / Anti-Patterns
...

## Gotchas
- Common mistake 1
- Common mistake 2
```

The `description` field must contain "Use when" with specific trigger conditions — this tells the agent WHEN to activate the skill.

## Skill Reference

### Backend Skills

| Skill | Key patterns |
|-------|-------------|
| api-endpoint-creator | RPC-style `@Post`, no path params, `@QueryValue`, `@Post("/list")` with offset/limit |
| backend-api-design | Layered architecture, thin controllers, Manager orchestration |
| kotlin-best-practices | No `!!`, Either error handling, no silent try-catch, no magic numbers |
| database-patterns | TEXT not VARCHAR, UUID PKs, soft deletes, partial indexes, gen_random_uuid() |
| database-table-creator | Full Kotlin sync: SQL → Table → Entity → Repository → Tests |
| testing-strategies | No manager unit tests — controller integration tests via AbstractControllerTest |
| security-checklist | OWASP top 10 for Micronaut: auth, IDOR, injection, secrets, sanitization |

### Frontend & Design Skills

| Skill | Key patterns |
|-------|-------------|
| design-intelligence | Token generation: HSL palettes, type scales, spacing grids, Tailwind config output |
| design-workflow | Anti-AI-generic: no purple gradients, break the grid, real copy, font weight contrast |
| browser-qa | Playwright-based: page load → interaction → assertion → screenshot |
| web-to-prd | Two-pass crawl, feature extraction, epic/story generation, Notion export |

### Content & Business Skills

| Skill | Key patterns |
|-------|-------------|
| article-writing | Human voice, no AI filler, strong opening, varied sentence structure |
| content-engine | Platform-native adaptation: X threads, LinkedIn posts, TikTok scripts, newsletters |
| investor-outreach | Mom Test principles, cold/warm/follow-up templates, personalization hooks |
| investor-materials | Consistent narrative across deck/memo/one-pager, realistic projections |

## Contributing

- Each skill is one folder under `skills/` with a `SKILL.md` and optional supporting files
- Frontmatter must include `name`, `description` (with "Use when" trigger), and `allowed-tools`
- Include a `## Gotchas` section with at least 3 items — real failure patterns, not generic advice

## License

MIT
