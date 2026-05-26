---
name: uselink-report
description: "Generate formatted HTML reports and publish them to uselink. Use when the user asks for a code review report, architecture overview, sprint summary, daily standup report, or any structured report they want shared via a link."
allowed-tools:
  - Bash
  - Read
  - Write
  - Glob
  - Grep
---

# Uselink Report Generator

> See `report-templates.md` for HTML templates and per-report-type data gathering commands.

## Prerequisites

Before generating any report, verify:

1. **CLI installed** -- `which uselink` must resolve. If not, tell the user to run `npm install -g uselink`.
2. **CLI configured** -- `~/.uselink/config.json` must exist with `api_key`. If not, tell the user to run `uselink login` in their terminal (interactive -- Claude cannot run it).

Stop here if either check fails. The user must fix these.

## Report Types

| Type | Trigger phrases | Data sources |
|------|----------------|--------------|
| Code review | "review report", "code review summary" | `git diff`, file reads, grep |
| Architecture | "architecture report", "system overview" | Directory scan, key config files |
| Sprint/daily | "sprint report", "daily summary", "standup" | `git log --since`, `gh pr list` |
| Custom | Anything else that asks for a "report" | Conversation context, user instructions |

If the type is ambiguous, ask. Do not guess.

## Workflow

1. **Determine report type** from the user's request.
2. **Gather data** using the commands and file reads listed in `report-templates.md` for that report type.
3. **Generate HTML** by filling the matching template from `report-templates.md`. Write to `/tmp/uselink-report-<type>-<timestamp>.html`.
4. **Publish** with `uselink publish <file> --title "<Report Title>" --format html`.
5. **Return the URL** as the primary output.

## Key Rules

- Always use `--format html`. Reports are HTML, not markdown.
- Title format: `<Type> Report -- <Project> -- <Date>`. Example: `Code Review -- my-project -- 2026-05-25`.
- Infer project name from the git remote or directory name.
- Date in titles uses `YYYY-MM-DD`.
- Do not ask the user which template to use -- pick based on the report type.
- The temp file is not cleaned up so the user can re-publish after edits.

## Content Rules

- Use real data from the repo. Never fabricate commits, file names, or findings.
- For code review reports, run the actual diff and analyze it. Do not summarize from memory.
- For sprint reports, use `git log` output, not guesses about what was worked on.
- Keep reports concise. A 200-line HTML report is better than a 2000-line one.

## Gotchas

- **`uselink login` is interactive.** Claude cannot run it. If config is missing, tell the user to run it themselves and stop.
- **HTML must use inline CSS only.** Uselink strips `<link>` and `<style>` tags that reference external files. All styles go in `style` attributes or a single `<style>` block in `<head>`. See templates in `report-templates.md`.
- **Publishing always creates a new document.** There is no upsert. If the user wants to update an existing report, they need to publish again and get a new URL.
- **The CLI prints the URL to stdout.** Capture full stdout from the publish command. The URL is typically the last line.
- **Large diffs can exceed context.** For code review reports on big PRs, summarize by file rather than including every line. Use `git diff --stat` first to scope the work, then read only the files with meaningful changes.
- **`git log` date formats matter.** Use `--format="%H|%an|%s|%ai"` for parseable output. Do not rely on the default pretty format.
- **Sprint "since" needs a sane default.** If the user does not specify a date range, default to 7 days for sprint reports and 1 day for daily summaries.
