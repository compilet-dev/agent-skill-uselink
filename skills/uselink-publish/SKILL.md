---
name: uselink-publish
description: "Publish files to uselink via the CLI. Use when the user asks to publish a document, share a file as a link, push content to uselink, or says 'publish this'."
allowed-tools:
  - Bash
  - Read
  - Write
  - Glob
---

# Publish to Uselink

Publishes Markdown or HTML files to uselink and returns the shareable URL.

## Prerequisites

Before publishing, verify both conditions:

1. **CLI installed** -- `which uselink` must resolve. If missing, tell the user to run `npm install -g uselink`.
2. **CLI configured** -- `~/.uselink/config.json` must exist with `api_key` and `api_url`. If missing, tell the user to run `uselink login` (interactive -- Claude cannot run it).

Do not proceed past prerequisites until both pass. The user must fix these themselves.

## Publishing

### When the user gives a file path

```bash
uselink publish <file> --title "Title Here" --format markdown
```

- `--format` is `markdown` or `html`. Infer from extension: `.md` -> `markdown`, `.html`/`.htm` -> `html`.
- `--title` defaults to the filename stem if the user doesn't specify one.
- `--project <uuid>` is optional -- only pass it if the user provides a project/workspace ID.

### When the user says "publish this" about generated content

1. Write the content to a temp file: `/tmp/uselink-publish-<timestamp>.md` (or `.html`).
2. Publish the temp file.
3. Do not clean up the temp file -- the user may want to re-publish after edits.

### After publishing

The CLI prints the public URL to stdout. Extract it and return it to the user as the primary output.

## Format Detection

| Extension | Format flag |
|-----------|-------------|
| `.md`, `.mdx`, `.markdown` | `--format markdown` |
| `.html`, `.htm` | `--format html` |
| No extension or ambiguous | Ask the user, or default to `markdown` |

## Gotchas

- **`uselink login` is interactive and cannot be run by Claude.** It prompts for an API key via stdin. If config is missing, tell the user to run it in their terminal -- do not attempt it.
- **The CLI prints the URL to stdout, not stderr.** Capture the full stdout from the publish command. The URL is typically the last line of output.
- **Large files may take several seconds.** Do not set an aggressive timeout on the publish command. Use a 30-second timeout minimum.
- **HTML files with relative asset paths will break on publish.** If the file references local images or CSS (`./assets/`, `../styles/`), warn the user that those assets won't be available on the published page. Inline assets or use absolute URLs.
- **Publishing the same file twice creates a new document each time.** The CLI does not upsert -- it always creates. If the user wants to update an existing doc, they need `uselink update` (not covered by this skill). Mention this if the user says "update" or "republish".
