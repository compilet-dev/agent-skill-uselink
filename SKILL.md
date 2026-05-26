---
type: skill-pack
name: production-agent-skills
description: Production-tested agent skills for backend (Kotlin/Micronaut), frontend (React/Tailwind), design systems, content writing, and startup workflows. Extracted from real SaaS codebases — every anti-pattern comes from a real bug or production incident.
author: spartan-hieuvo
version: 1.0.0
includes:
  - skills/api-endpoint-creator/SKILL.md
  - skills/backend-api-design/SKILL.md
  - skills/kotlin-best-practices/SKILL.md
  - skills/database-patterns/SKILL.md
  - skills/database-table-creator/SKILL.md
  - skills/testing-strategies/SKILL.md
  - skills/security-checklist/SKILL.md
  - skills/design-intelligence/SKILL.md
  - skills/design-workflow/SKILL.md
  - skills/browser-qa/SKILL.md
  - skills/web-to-prd/SKILL.md
  - skills/article-writing/SKILL.md
  - skills/content-engine/SKILL.md
  - skills/investor-outreach/SKILL.md
  - skills/investor-materials/SKILL.md
---

> **This file is NOT a skill.** It is the entry point for the skill pack. Agents should read the individual skill files listed in `includes` above.

## Overview

This skill pack contains 15 production-tested skills organized into three categories:

### Backend (Kotlin/Micronaut)

Skills for building Kotlin/Micronaut backends with Exposed ORM, PostgreSQL, and Flyway migrations. Enforces layered architecture (Controller → Manager → Repository), RPC-style APIs, and strict coding standards.

### Frontend & Design

Skills for bootstrapping design systems, applying anti-AI-generic design guidelines, running browser QA with Playwright, and reverse-engineering web apps into PRDs.

### Content & Business

Skills for writing human-sounding content, adapting ideas across social platforms, drafting investor communications, and creating fundraising materials.

## Skill Modules

| Module | Path | Category |
|--------|------|----------|
| API Endpoint Creator | `skills/api-endpoint-creator/SKILL.md` | Backend |
| Backend API Design | `skills/backend-api-design/SKILL.md` | Backend |
| Kotlin Best Practices | `skills/kotlin-best-practices/SKILL.md` | Backend |
| Database Patterns | `skills/database-patterns/SKILL.md` | Backend |
| Database Table Creator | `skills/database-table-creator/SKILL.md` | Backend |
| Testing Strategies | `skills/testing-strategies/SKILL.md` | Backend |
| Security Checklist | `skills/security-checklist/SKILL.md` | Backend |
| Design Intelligence | `skills/design-intelligence/SKILL.md` | Design |
| Design Workflow | `skills/design-workflow/SKILL.md` | Design |
| Browser QA | `skills/browser-qa/SKILL.md` | Design |
| Web to PRD | `skills/web-to-prd/SKILL.md` | Design |
| Article Writing | `skills/article-writing/SKILL.md` | Content |
| Content Engine | `skills/content-engine/SKILL.md` | Content |
| Investor Outreach | `skills/investor-outreach/SKILL.md` | Business |
| Investor Materials | `skills/investor-materials/SKILL.md` | Business |

## How Agents Should Use This Skill Pack

1. **Read the relevant skill** before starting work. Each skill has a "When to Use" section with trigger conditions.
2. **Follow the rules and anti-patterns** listed in the skill. They come from real production incidents.
3. **Check the Gotchas section** at the bottom of each skill — these are the mistakes that Claude actually makes in practice.
4. **Use supporting files** (code-patterns.md, examples.md, checklists.md) for detailed templates and reference code.

## Typical Agent Workflows

### A. Build a new backend feature

1. Read `database-table-creator` → generate SQL migration + Kotlin Table/Entity/Repository
2. Read `api-endpoint-creator` → scaffold Controller → Manager → tests
3. Read `security-checklist` → audit for auth, input validation, IDOR
4. Read `testing-strategies` → add integration tests

### B. Design and build a UI feature

1. Read `design-intelligence` → generate design tokens
2. Read `design-workflow` → apply anti-AI-generic guidelines
3. Read `browser-qa` → run Playwright QA before PR

### C. Prepare fundraising materials

1. Read `investor-materials` → create pitch deck and one-pager
2. Read `investor-outreach` → draft personalized investor emails
3. Read `content-engine` → adapt pitch into launch content

## Repository Structure

```
production-agent-skills/
├── README.md                          # Installation and usage guide
├── SKILL.md                           # This file — skill pack manifest
├── marketplace.json                   # Machine-readable skill registry
├── LICENSE                            # MIT license
└── skills/
    ├── api-endpoint-creator/
    │   ├── SKILL.md
    │   ├── examples.md
    │   ├── testing-patterns.md
    │   └── error-handling-guide.md
    ├── backend-api-design/
    │   ├── SKILL.md
    │   └── code-patterns.md
    ├── kotlin-best-practices/
    │   ├── SKILL.md
    │   └── code-patterns.md
    ├── database-patterns/
    │   ├── SKILL.md
    │   └── code-templates.md
    ├── database-table-creator/
    │   ├── SKILL.md
    │   ├── examples.md
    │   ├── kotlin-templates.md
    │   └── validation-checklist.md
    ├── testing-strategies/
    │   ├── SKILL.md
    │   ├── examples.md
    │   └── integration-test-setup.md
    ├── security-checklist/
    │   ├── SKILL.md
    │   └── audit-reference.md
    ├── design-intelligence/
    │   ├── SKILL.md
    │   ├── palettes.md
    │   ├── tokens-reference.md
    │   └── typography.md
    ├── design-workflow/
    │   ├── SKILL.md
    │   └── checklists.md
    ├── browser-qa/
    │   ├── SKILL.md
    │   └── playwright-snippets.md
    ├── web-to-prd/
    │   └── SKILL.md
    ├── article-writing/
    │   ├── SKILL.md
    │   └── examples.md
    ├── content-engine/
    │   ├── SKILL.md
    │   └── examples.md
    ├── investor-outreach/
    │   ├── SKILL.md
    │   └── examples.md
    └── investor-materials/
        ├── SKILL.md
        └── example-outline.md
```
