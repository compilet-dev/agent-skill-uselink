---
name: security-checklist
description: Security audit for Micronaut/Kotlin backend — auth, authz, input validation, IDOR, secrets, response sanitization. Use when implementing auth on a new endpoint, validating user inputs, reviewing a PR for security issues, or auditing for OWASP risks.
allowed-tools:
  - Read
  - Glob
  - Grep
---

# Security Checklist

Run a security audit against Micronaut/Kotlin backend code. The list below names the specific failure modes Claude trips on in this codebase — each one comes from a real near-miss or rule violation we've seen.

> See `audit-reference.md` for code examples and full pass/fail patterns.

## When to Use

- Adding `@Secured` to a new controller or endpoint
- Receiving user input on a new request DTO
- Reviewing a PR for security issues before merge
- Auditing a service for IDOR, injection, or auth-bypass risks

## Audit Process

Walk every staged controller and manager in the diff. For each, check the anti-patterns below in order. **Stop and flag** as soon as one matches — don't bundle findings.

1. Run `git diff --staged --name-only | grep -E '(Controller|Manager)\.kt$'` to find files in scope
2. For each, check against `## Anti-Patterns` below
3. For each finding, output: file:line, anti-pattern name, fix

## Anti-Patterns

### Anti-Pattern: Bare `@QueryValue` for multi-word param

```kotlin
// WRONG — frontend sends ?project_id=xxx, Micronaut binds to ?projectId=xxx, param is null
@QueryValue projectId: UUID

// CORRECT — explicit snake_case name matches what frontend sends
@QueryValue("project_id") projectId: UUID
```

Jackson's snake_case config affects JSON body, NOT query params. If a multi-word `@QueryValue` is missing the explicit name, the param silently binds to `null` (or worse, an attacker-supplied value if they guess the camelCase name). Every multi-word `@QueryValue` MUST have `@QueryValue("snake_case_name")`.

### Anti-Pattern: `@Secured(IS_AUTHENTICATED)` without ownership check

```kotlin
// WRONG — any authenticated user can read any project
@Get("/project")
suspend fun get(@QueryValue id: UUID, auth: Authentication): ProjectResponse {
  return projectManager.byId(id).throwOrValue()
}

// CORRECT — manager verifies the user owns this resource
@Get("/project")
suspend fun get(@QueryValue id: UUID, auth: Authentication): ProjectResponse {
  return projectManager.byIdForUser(id, auth.userId()).throwOrValue()
}
```

`@Secured(IS_AUTHENTICATED)` only checks the JWT is valid. It does NOT check the user owns the resource. Every endpoint that returns user-owned data must verify ownership in the manager — IDOR risk otherwise.

### Anti-Pattern: Logging request body on auth endpoints

```kotlin
// WRONG — leaks passwords and tokens to logs
logger.info("Login attempt: $request")

// CORRECT — log non-sensitive fields only
logger.info("Login attempt for email=${request.email}")
```

Auth endpoints (`/login`, `/register`, `/reset-password`, `/refresh-token`) handle plaintext credentials. Never log the full request, never log the response (contains tokens), never log headers (`Authorization` contains bearer tokens). Inspect every `logger.info/debug` call near auth code.

### Anti-Pattern: Returning entities directly from controllers

```kotlin
// WRONG — leaks deletedAt, internal flags, audit columns
return userRepository.byId(id)

// CORRECT — Response DTO controls what's exposed
return UserResponse.from(userRepository.byId(id))
```

Entities have all DB columns including `deletedAt`, internal status flags, and timestamps. Response DTOs in `module-client` are the security boundary. Every controller return type must be a `*Response` DTO, never an Entity.

### Anti-Pattern: Missing `deletedAt.isNull()` filter

```kotlin
// WRONG — returns soft-deleted records, including ones the user explicitly deleted
.where { ProjectsTable.id eq id }

// CORRECT — every SELECT on a soft-deletable table must filter
.where { ProjectsTable.id eq id and ProjectsTable.deletedAt.isNull() }
```

Soft-deleted records are user-initiated deletions. Returning them is a privacy violation (e.g., a user deleted their account but their data is still accessible). Every SELECT must include `deletedAt.isNull()`.

### Anti-Pattern: String concatenation in SQL

```kotlin
// WRONG — SQL injection
exec("SELECT * FROM users WHERE email = '$email'")

// CORRECT — Exposed ORM auto-parameterizes
UsersTable.selectAll().where { UsersTable.email eq email }
```

We use Exposed ORM project-wide. Any `exec()` or raw `connection.prepareStatement()` is a code smell. If you find one, escalate.

## Rules (quick reference)

- Every controller endpoint has `@Secured(...)` — never `@Secured(IS_ANONYMOUS)` for non-public endpoints
- Admin endpoints use `OAuthSecurityRule.ADMIN`
- Resource ownership verified in manager, not just in controller
- `@Valid` on every controller param, Jakarta validation annotations on all required DTO fields
- No raw SQL with string concatenation (Exposed ORM only)
- Response DTOs only — never return entities
- Auth endpoints don't log request body, response, or headers
- Rate limiting on `/login`, `/register`, `/reset-password`
- `.env` and secrets never committed (`.gitignore` enforced)

## Output Format

For each finding:

```
[file]:[line]   [Anti-Pattern Name]
What's wrong:   [one line]
Fix:            [code change]
```

End with summary: `N findings — X critical, Y major, Z minor`. Critical = auth bypass / data leak / injection. Major = IDOR / missing ownership check. Minor = style / defense-in-depth.

## Output Receipt

End with this receipt after the findings summary:

```text
Checked:
- <path> — <controller/manager/repository/request DTO>
- <path> — <controller/manager/repository/request DTO>
Risk:
- <remaining unreviewed area or "low">
```

If a file could not be reviewed because context was missing, list it under
`Risk` instead of silently skipping it.

## Auto-Clarity

Security findings need enough detail for the author to act safely. Do not use
compressed shorthand when explaining:

- auth bypass, IDOR, or privilege escalation
- token, password, or secret exposure
- SQL injection or unsafe raw SQL
- payment, payout, subscription, or entitlement access
- user data deletion, export, or privacy behavior

For critical findings, include attacker path, affected data, and concrete fix.

## Boundaries

- Audit only. Do not edit code in this skill.
- Do not approve a PR or mark security work complete.
- Do not report hypothetical issues without a code path or missing guard.
- Do not down-rank auth bypass, data leak, injection, or secret exposure.
- Do not omit uncertainty; use a `question` finding when intent is unclear.
