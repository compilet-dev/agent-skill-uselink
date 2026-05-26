---
name: api-endpoint-creator
description: Create a complete RPC-style API endpoint (Controller → Manager → Repository → Tests) following project layered architecture. Use when adding a new endpoint, CRUD operations for a domain entity, or scaffolding the full stack from request DTO to integration test.
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
---

# API Endpoint Creator

Scaffolds a full RPC-style endpoint following the project's strict layered architecture. Code templates live in supporting files — this is the orchestration plan.

> See `examples.md` for full code (request/response DTOs, controller, manager, factory, repository).
> See `testing-patterns.md` for AbstractControllerTest setup and Retrofit client pattern.
> See `error-handling-guide.md` for Either<ClientException, T> patterns.

## When to Use

- Adding a new endpoint (e.g., `POST /workspace/create`)
- Wiring CRUD for a new domain entity end-to-end
- Setting up the full stack: Request DTO → Controller → Manager → Repository → Test

## Process

### 1. Decide URL + HTTP method

- **Mutation** → `@Post("/<noun>/create")`, `@Post("/<noun>/update")`, `@Post("/<noun>/delete")`. Never `@Put`/`@Delete`/`@Patch`.
- **Single read** → `@Get("/<noun>")` with `@QueryValue id: UUID` (singular noun)
- **List read** → `@Post("/<noun>/list")` with body `{ filter, sort, search, offset, limit }`. Never `@Get` with `page/size`.
- **No path params** — `?id=xxx`, never `/{id}`. See `## Anti-Pattern: Path parameters` below.

### 2. Create the Request/Response DTOs in `module-client`

Location: `app/module-client/src/main/kotlin/com/yourcompany/client/{request,response}/{domain}/`

- Response DTO has `companion object { fun from(entity) }` for entity → DTO conversion (not a separate mapper file)
- Request DTO uses `@field:NotBlank` for required Strings, `@field:NotNull` for required non-Strings
- List request implements `PageableRequest` (provides `offset: Long`, `limit: Int`)

> See `examples.md` § Request/Response Models for full templates.

### 3. Add the Controller

Location: `app/api-application/.../controller/`

Required annotations:
- `@ExecuteOn(TaskExecutors.IO)` — required for suspend functions
- `@Validated` — input validation
- `@Controller("/api/v1/...")` — endpoint path
- `@Secured(...)` — security rule (never `IS_ANONYMOUS` on user data)

The controller MUST be thin: parse input → delegate to manager → return response. No business logic, no repository injection.

> See `examples.md` § Controller for the full template.

### 4. Add the Manager + Manager Factory

Location: `app/module-{domain}/.../manager/`

- Manager interface returns `Either<ClientException, T>` — never throws
- Default impl injects `DatabaseContext` + repositories (no external API clients — those go in Services)
- Factory provides `@Singleton` bean

> See `examples.md` § Manager and `error-handling-guide.md` for Either patterns.

### 5. Add Repository methods (if missing)

Location: `app/module-repository/.../repository/`

- Reads use `transaction(db.replica)`, writes use `transaction(db.primary)`
- Every SELECT filters `deletedAt.isNull()` (soft delete)
- `.value` ONLY on `row[Table.id]` — other UUID columns map directly

> See `database-patterns` skill (load it) for repository templates.

### 6. Add multi-table writes in a transaction

If the manager touches 2+ tables, wrap in `transaction(db.primary) { ... }`. Single-table operations don't need an explicit transaction.

> See `error-handling-guide.md` § Multi-table operations.

### 7. Add the test

Location: `app/api-application/src/test/.../{domain}/`

- Extend `AbstractControllerTest` (never standalone)
- Use Retrofit clients from `module-client` — never raw `HttpRequest`
- Cover: happy path, auth (401), ownership (403/404), validation (400), not found (404)

> See `testing-patterns.md` for the full controller test template.

## Anti-Patterns

### Anti-Pattern: Path parameters
```kotlin
@Get("/employees/{id}")  // WRONG
@Get("/employee")        // CORRECT — ?id=xxx
```
We use query params project-wide (see `.claude/rules/backend-micronaut/API_DESIGN.md`). Reasons: explicit and self-documenting at call sites, easy to add optional params without breaking existing clients, and consistent with the FE conventions in `.claude/rules/frontend-react/FRONTEND.md` (snake_case wire format, no axios case-conversion middleware).

### Anti-Pattern: Inline data classes in controller files
```kotlin
// WRONG — data class at the bottom of MyController.kt
data class CreateMyRequest(val name: String)
```
All DTOs live in `module-client`. Inline data classes break the single-source-of-truth pattern.

### Anti-Pattern: Controller injecting Repository
```kotlin
class MyController(
  private val manager: MyManager,
  private val repository: MyRepository  // WRONG — only managers
)
```
Controllers inject Managers (and sometimes Detectors) only. Direct repository access bypasses business logic.

### Anti-Pattern: `@Get("/list")` with page/size
```kotlin
@Get("/employees")
suspend fun list(@QueryValue page: Int?, @QueryValue size: Int?)  // WRONG
```
List endpoints use `@Post("/list")` with `offset/limit` in the request body. See API_DESIGN.md.

### Anti-Pattern: Throwing exceptions in business logic
```kotlin
if (user == null) throw NotFoundException("user")  // WRONG
return ClientError.USER_NOT_FOUND.asException().left()  // CORRECT
```
Managers return `Either<ClientException, T>`. Controllers unwrap with `.throwOrValue()`.

## Checklist

- [ ] DTOs in `module-client/{request,response}/`, not inline
- [ ] Controller has `@ExecuteOn(TaskExecutors.IO)` + `@Secured` + `@Controller`
- [ ] Multi-word `@QueryValue` uses explicit snake_case name
- [ ] List endpoint uses `@Post("/list")` with offset/limit body
- [ ] Manager returns `Either<ClientException, T>` everywhere
- [ ] Multi-table writes wrapped in `transaction(db.primary) {}`
- [ ] Test extends `AbstractControllerTest`, uses Retrofit client
- [ ] Test covers: happy / auth / ownership / validation / not-found

## Output Receipt

End with this receipt:

```text
Changed:
- <path> — <endpoint/client/manager/repository/test change>
- <path> — <endpoint/client/manager/repository/test change>
Verified:
- <command or "not run">
Risk:
- <auth/ownership/migration concern or "low">
```

If the endpoint touches auth, ownership, payments, payouts, or destructive writes,
include the exact authorization rule and manager-level ownership check in `Risk`.

## Auto-Clarity

Use normal concise English, not compressed shorthand, when explaining:

- auth and ownership behavior
- payment, payout, subscription, or entitlement behavior
- data migrations or backfills required by the endpoint
- destructive writes, deletes, or irreversible state transitions
- any skipped test or manual verification gap

## Boundaries

- Do not invent domain rules. Ask or inspect existing managers first.
- Do not add anonymous access to user-owned data.
- Do not run migrations against shared or production databases.
- Do not bypass repository/manager layers to make the endpoint compile.
- Do not mark work complete without either running tests or stating exactly why not.
