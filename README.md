# Claude Code Skills

A curated collection of production-tested skills for [Claude Code](https://claude.ai/code). Each skill teaches Claude domain-specific patterns, conventions, and anti-patterns — so it writes code that fits your stack instead of generic boilerplate.

## What are Skills?

Skills are markdown files that extend Claude Code's capabilities with specialized knowledge. Drop them into `.claude/skills/` in any project, and Claude automatically loads the right skill when you invoke it.

[Learn more about Claude Code Skills](https://docs.anthropic.com/en/docs/claude-code/skills)

## Skills

### Backend (Kotlin/Micronaut)

| Skill | Description |
|-------|-------------|
| [api-endpoint-creator](.claude/skills/api-endpoint-creator/) | Scaffold a complete RPC-style endpoint: Controller -> Manager -> Repository -> Tests |
| [backend-api-design](.claude/skills/backend-api-design/) | Design RPC-style APIs with layered architecture patterns |
| [kotlin-best-practices](.claude/skills/kotlin-best-practices/) | Kotlin coding standards -- null safety, Either error handling, Exposed ORM, coroutines |
| [database-patterns](.claude/skills/database-patterns/) | Database schema design, migrations, soft deletes, and Exposed ORM patterns |
| [database-table-creator](.claude/skills/database-table-creator/) | Create a database table with full Kotlin sync (SQL -> Table -> Entity -> Repo -> Tests) |
| [testing-strategies](.claude/skills/testing-strategies/) | Testing patterns for Micronaut/Kotlin: repository tests, integration tests, test data builders |
| [security-checklist](.claude/skills/security-checklist/) | Security audit: auth, authz, input validation, IDOR, secrets, response sanitization |

### Frontend & Design

| Skill | Description |
|-------|-------------|
| [design-intelligence](.claude/skills/design-intelligence/) | Bootstrap a complete design system: tokens, typography, spacing, Tailwind config, CSS variables |
| [design-workflow](.claude/skills/design-workflow/) | Anti-AI-generic design guidelines -- make UI that doesn't look like it was made by AI |
| [browser-qa](.claude/skills/browser-qa/) | Run real browser QA with Playwright -- find bugs users would find |
| [web-to-prd](.claude/skills/web-to-prd/) | Scan a live web app, extract features, generate PRD with epics/stories/priorities |

### Content & Business

| Skill | Description |
|-------|-------------|
| [article-writing](.claude/skills/article-writing/) | Write blog posts, guides, and tutorials that sound human, not AI |
| [content-engine](.claude/skills/content-engine/) | Turn one idea into platform-native content for X, LinkedIn, TikTok, YouTube, newsletters |
| [investor-outreach](.claude/skills/investor-outreach/) | Draft cold emails, warm intro blurbs, and follow-ups for investors |
| [investor-materials](.claude/skills/investor-materials/) | Create pitch decks, one-pagers, memos, and financial models |

## Installation

### Install all skills

```bash
cp -R .claude/skills/* /path/to/your-project/.claude/skills/
```

### Install a single skill

```bash
cp -R .claude/skills/api-endpoint-creator /path/to/your-project/.claude/skills/
```

### Using with Claude Code

Once installed, invoke a skill by name:

```
/api-endpoint-creator
/database-patterns
/security-checklist
```

Claude loads the skill context automatically and applies the patterns to your codebase.

## Skill Format

Each skill follows the standard `SKILL.md` format with YAML frontmatter:

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
```

Skills can include supporting files for code templates, examples, and checklists.

## Origin

These skills were extracted from a production Kotlin/Micronaut + React SaaS codebase. Every anti-pattern listed comes from a real bug, failed PR review, or production incident.

## License

MIT
