---
type: skill-pack
name: uselink-skills
description: Agent skills for publishing engineering artifacts to uselink — repo summaries, PR digests, changelogs, API docs, onboarding guides, reports, and specs. Each skill gathers data, generates HTML, and publishes via the uselink CLI.
author: compilet-dev
version: 1.0.0
includes:
  - skills/uselink-publish/SKILL.md
  - skills/uselink-report/SKILL.md
  - skills/uselink-share-spec/SKILL.md
  - skills/uselink-repo-summary/SKILL.md
  - skills/uselink-pr-digest/SKILL.md
  - skills/uselink-changelog/SKILL.md
  - skills/uselink-api-docs/SKILL.md
  - skills/uselink-onboarding/SKILL.md
---

> **This file is NOT a skill.** It is the entry point for the uselink skill pack. Agents should read the individual skill files listed in `includes` above.

## Overview

[uselink](https://uselink.app) lets you publish Markdown and HTML to a shareable link in seconds — perfect for sharing AI-generated engineering artifacts with stakeholders who don't have repo access.

This skill pack teaches Claude Code to generate and publish various engineering documents to uselink automatically.

## Required Environment Setup

Before any skill can publish, the uselink CLI must be installed and configured:

```bash
# Check prerequisites
which uselink || echo "Install with: npm install -g uselink"
test -f ~/.uselink/config.json || echo "Run: uselink login"
```

`uselink login` is interactive (prompts for API key) — Claude cannot run it. The user must do this step manually.

## Skill Modules

| Module | What it does |
|--------|-------------|
| `uselink-publish` | Publish any Markdown or HTML file to uselink |
| `uselink-report` | Generate code review, architecture, or sprint reports and publish |
| `uselink-share-spec` | Share planning specs from `.planning/` with stakeholders |
| `uselink-repo-summary` | Scan a GitHub repo and publish an architecture overview |
| `uselink-pr-digest` | Summarize a PR as a stakeholder-friendly page and publish |
| `uselink-changelog` | Generate release notes from git history and publish |
| `uselink-api-docs` | Scan controllers/routes and publish API documentation |
| `uselink-onboarding` | Generate a new-developer onboarding guide and publish |

## How Agents Should Use This Skill Pack

Every skill follows the same pattern:

1. **Check prerequisites** — `which uselink` + config exists
2. **Gather data** — git log, file reads, grep, gh CLI
3. **Generate HTML** — using inline CSS (uselink strips external styles)
4. **Write to temp file** — `/tmp/uselink-<type>-<timestamp>.html`
5. **Publish** — `uselink publish <file> --title "..." --format html`
6. **Return the URL** — the CLI prints it to stdout

## Typical Agent Workflows

### A. Share a repo with a new team member

1. Use **uselink-repo-summary** to generate an architecture overview
2. Use **uselink-onboarding** to generate a setup guide
3. Send both links to the new hire

### B. Ship a release and notify stakeholders

1. Use **uselink-changelog** to generate release notes from the tag range
2. Use **uselink-pr-digest** on the key PRs for detailed context
3. Share the changelog link in Slack/email

### C. Document the API for a partner

1. Use **uselink-api-docs** to scan controllers and generate API reference
2. Share the link — no Swagger setup, no API gateway config

### D. Sprint review

1. Use **uselink-report** (sprint type) to generate a summary with commits, PRs, and contributors
2. Share the link in the sprint review meeting

## Repository Structure

```
agent-skill-uselink/
├── README.md
├── SKILL.md                          # This file — skill pack manifest
├── marketplace.json                  # Machine-readable skill registry
├── LICENSE
└── skills/
    ├── uselink-publish/SKILL.md      # Basic file publishing
    ├── uselink-report/               # Reports (code review, architecture, sprint)
    │   ├── SKILL.md
    │   └── report-templates.md
    ├── uselink-share-spec/SKILL.md   # Share planning specs
    ├── uselink-repo-summary/SKILL.md # Repo architecture overview
    ├── uselink-pr-digest/SKILL.md    # PR summaries for stakeholders
    ├── uselink-changelog/SKILL.md    # Release notes from git history
    ├── uselink-api-docs/SKILL.md     # API documentation from code
    └── uselink-onboarding/SKILL.md   # New-dev getting started guide
```
