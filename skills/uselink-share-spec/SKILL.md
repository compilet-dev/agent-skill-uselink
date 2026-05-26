---
name: uselink-share-spec
description: "Publish a planning spec or plan to uselink so stakeholders can read it without repo access. Use when the user says 'share spec', 'publish the plan', 'send this spec to PM', or wants a shareable link for a .planning artifact."
allowed-tools:
  - Bash
  - Read
  - Glob
---

# Share Spec via Uselink

Publish specs and plans from `.planning/` to uselink so PMs, stakeholders, and business people can read them without cloning the repo.

## When to Use

- User wants to share a spec or plan with someone outside the repo
- User says "share the versioning spec" or "publish the analytics plan"
- User wants a link to send a stakeholder for review

## Prerequisites

Check CLI installed and configured before anything else:

```bash
which uselink || echo "MISSING"
test -f ~/.uselink/config.json && echo "OK" || echo "NO_CONFIG"
```

If `uselink` is missing, tell the user to install it. If config is missing, tell them to run `uselink login`.

## Workflow

### 1. Find the artifact

Scan both directories:

```bash
ls .planning/specs/*.md .planning/plans/*.md 2>/dev/null
```

If the user gave a name (e.g. "versioning"), fuzzy-match against filenames. If multiple files match, list them and ask which one. If zero match, ask for the path.

### 2. Clean the title

Strip prefixes and convert to human-readable Title Case:

| Filename | Title |
|----------|-------|
| `done_04_publishing.md` | Publishing |
| `10_versioning.md` | Versioning |
| `billing-hardening.md` | Billing Hardening |
| `spec-11-polish-v2.md` | Polish V2 |

Rules: drop `done_` prefix, drop leading number + underscore (`04_`), drop `spec-NN-` prefix, convert kebab/snake to spaces, Title Case the result.

### 3. Publish

```bash
uselink publish <file> --title "<clean title>"
```

Capture the URL from stdout.

### 4. Return the URL

Report the published URL to the user. If the file was a `done_*` spec, mention it was a completed spec.

## Gotchas

- **`done_` prefix means the spec is implemented, not that it shouldn't be shared.** Stakeholders often want to review completed specs for reference or onboarding. Publish it normally but mention the spec is marked as done.
- **The CLI config file path is `~/.uselink/config.json`, not `~/.config/uselink/`.** If the user says "I already logged in" but the config check fails, they may have installed a different version or the config landed in the wrong directory. Check both paths before giving up.
- **Glob for `.md` only -- `.planning/` also contains `.html` files (like the PRD).** The uselink CLI handles HTML too, but the title-cleaning logic assumes markdown naming conventions. If the user asks to share an HTML file, skip the title cleaning and use the filename as-is.
- **Some plans have no number prefix (e.g. `public-profile.md`, `billing-hardening.md`).** The title cleaner must handle both numbered (`10_versioning.md`) and unnumbered (`billing-hardening.md`) filenames without mangling either.
