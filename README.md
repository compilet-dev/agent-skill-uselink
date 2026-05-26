# Uselink Skills for Claude Code

Agent skills that publish engineering artifacts to [uselink](https://uselink.com) — shareable docs in seconds, no deploy pipeline needed.

Each skill follows the same pattern: **gather data → generate HTML → publish via CLI → return shareable link.**

## Table of Contents

- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Available Skills](#available-skills)
- [Typical Workflows](#typical-workflows)
- [How Skills Work](#how-skills-work)
- [Contributing](#contributing)

## Prerequisites

The uselink CLI must be installed and configured:

```bash
# Install
npm install -g uselink

# Login (interactive — enter your API key)
uselink login
```

Verify:
```bash
which uselink        # should resolve
cat ~/.uselink/config.json  # should have api_key
```

## Installation

### Via `npx skills` (Recommended)

```bash
# Install all skills
npx skills add spartan-hieuvo/claude-skills

# Install a specific skill
npx skills add spartan-hieuvo/claude-skills --skill uselink-repo-summary

# List available skills
npx skills add spartan-hieuvo/claude-skills --list

# Install globally (available in all projects)
npx skills add spartan-hieuvo/claude-skills -g
```

### Via Claude Code (Manual)

```bash
git clone https://github.com/spartan-hieuvo/claude-skills.git
cp -R claude-skills/skills/* /path/to/your-project/.claude/skills/
```

Or install from the marketplace manifest:

```bash
claude skills install ./marketplace.json
```

## Available Skills

| Skill | What it does |
|-------|-------------|
| [uselink-publish](skills/uselink-publish/) | Publish any Markdown or HTML file to uselink |
| [uselink-report](skills/uselink-report/) | Generate code review, architecture, or sprint reports and publish |
| [uselink-share-spec](skills/uselink-share-spec/) | Share planning specs from `.planning/` with stakeholders |
| [uselink-repo-summary](skills/uselink-repo-summary/) | Scan a GitHub repo and publish an architecture overview |
| [uselink-pr-digest](skills/uselink-pr-digest/) | Summarize a PR as a stakeholder-friendly page and publish |
| [uselink-changelog](skills/uselink-changelog/) | Generate release notes from git history and publish |
| [uselink-api-docs](skills/uselink-api-docs/) | Scan controllers/routes and publish API documentation |
| [uselink-onboarding](skills/uselink-onboarding/) | Generate a new-developer onboarding guide and publish |

## Typical Workflows

### 1. Share a repo with a new team member

```
/uselink-repo-summary     → architecture overview link
/uselink-onboarding       → getting-started guide link
```

Send both links — the new hire reads them before touching the code.

### 2. Ship a release and notify stakeholders

```
/uselink-changelog        → release notes link
/uselink-pr-digest #42    → detailed PR summary link
```

Share the changelog in Slack. Link the PR digest for anyone who wants details.

### 3. Document the API for a partner

```
/uselink-api-docs         → API reference link
```

No Swagger setup, no API gateway. Just scan the code and share.

### 4. Sprint review

```
/uselink-report           → sprint summary with commits, PRs, contributors
```

Open the link in the meeting. Everyone sees what shipped.

### 5. Code review for the PM

```
/uselink-pr-digest #87    → "what changed and why" page
```

The PM reads plain English, not diffs.

## How Skills Work

Every skill is a `SKILL.md` file with YAML frontmatter that teaches Claude Code a specific workflow:

```
1. Check prerequisites (uselink CLI + config)
2. Gather data (git, gh, file reads)
3. Generate HTML (inline CSS, dark mode support)
4. Write to /tmp/uselink-<type>-<timestamp>.html
5. Run: uselink publish <file> --title "..." --format html
6. Return the shareable URL
```

### Invoke a skill

```
/uselink-repo-summary
/uselink-changelog
/uselink-pr-digest #42
```

Or just describe what you want — Claude matches the right skill:

```
"Summarize this repo and share it with the PM"
"Generate release notes for this week"
"Create API docs and publish them"
```

## Contributing

- Each skill is one folder under `skills/` with a `SKILL.md`
- Skills must check prerequisites before publishing
- HTML must use inline CSS only (uselink strips external stylesheets)
- All repo-sourced strings must be HTML-escaped
- Include a `## Gotchas` section with real failure patterns

## License

MIT
