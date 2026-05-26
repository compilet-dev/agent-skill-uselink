---
name: kotlin-best-practices
description: Kotlin coding standards — null safety, Either error handling, Exposed ORM, coroutines, transactions. Use when writing Kotlin code for Micronaut backends, reviewing Kotlin PRs, or when ktlint flags an issue.
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
---

# Kotlin Best Practices

Codebase-specific Kotlin standards. The anti-patterns below are the ones Claude actually trips on in this repo — each comes from a real ktlint failure or PR review.

> See `code-patterns.md` for full code templates (manager skeleton, repository pattern, table definition, etc.).

## When to Use

- Writing or reviewing Kotlin code in a Micronaut/Exposed backend
- ktlint failed and you need to know which rule and how to fix
- Adding a new manager, repository, or controller

## Anti-Patterns

### Anti-Pattern: `!!` (force unwrap) anywhere

```kotlin
// WRONG — ktlint fails the build, runtime NPE risk
val email = decodedToken.email!!

// CORRECT — Either + elvis
val email = decodedToken.email ?: return AuthError.INVALID_CREDENTIALS.asException().left()

// CORRECT — null check enables smart cast
if (user == null) return AuthError.AUTHENTICATION_FAILED.asException().left()
generateTokensAndResponse(user)  // user is non-null here
```

`!!` is banned project-wide and ktlint enforces it. There are NO exceptions. Use `?.`, `?:`, or null-check + smart cast.

### Anti-Pattern: `runCatching` in suspend functions

```kotlin
// WRONG — swallows CancellationException, breaks coroutine cancellation silently
val result = runCatching { suspendCall() }.getOrDefault(fallback)

// CORRECT — rethrow CancellationException explicitly
val result = runCatching { suspendCall() }
  .onFailure { if (it is CancellationException) throw it }
  .onFailure { logger.warn("Operation failed", it) }
  .getOrDefault(fallback)

// BEST — use Either, no exceptions at all
suspend fun doWork(): Either<ClientException, Result> { ... }
```

`runCatching` catches all `Throwable` including `CancellationException`. In coroutine code that means cancellation requests get silently absorbed and the coroutine keeps running. In non-suspend code, `runCatching` is fine. In suspend code, prefer `Either`.

### Anti-Pattern: `Provider<T>` for circular dependency

```kotlin
// WRONG — Provider<T> hides a cycle that should be fixed
class DefaultProjectManager(
  private val workspaceManagerProvider: Provider<WorkspaceManager>,  // smell
)

// CORRECT — extract shared logic to a third interface at a lower layer
class DefaultProjectManager(
  private val workspaceManager: WorkspaceManager,                // direct
  private val projectThumbnailResolver: ProjectThumbnailResolver  // new, no cycle
)
```

If you reach for `Provider<T>` to lazy-resolve a circular dep, stop. Extract the shared functionality into its own interface and inject directly.

### Anti-Pattern: `.value` on non-`id` UUID columns

```kotlin
// WRONG — only Table.id returns EntityID<UUID>; uuid() columns return UUID directly
fun toEntity(row: ResultRow) = Entity(
  id = row[Table.id].value,
  projectId = row[Table.projectId].value,  // compile error: "Unresolved reference 'value'"
  userId = row[Table.userId].value,        // same error
)

// CORRECT
fun toEntity(row: ResultRow) = Entity(
  id = row[Table.id].value,           // .value ONLY for id
  projectId = row[Table.projectId],   // direct UUID
  userId = row[Table.userId],         // direct UUID
)
```

Rule: only `row[Table.id]` needs `.value`. Every other `uuid()` column maps directly.

### Anti-Pattern: `.where {}` called twice

```kotlin
// WRONG — second .where REPLACES the first; deletedAt filter is lost
Table.selectAll()
  .where { Table.deletedAt.isNull() }
  .where { Table.status eq "active" }   // overwrites the deletedAt filter

// CORRECT
Table.selectAll()
  .where { Table.deletedAt.isNull() }
  .andWhere { Table.status eq "active" }
```

Calling `.where {}` a second time silently replaces the first. Always use `.andWhere {}` to add conditions.

### Anti-Pattern: Hardcoded strings instead of enum.value

```kotlin
// WRONG — silent break when enum value renames
ifLeft = { "critical" }
if (status == "at_risk") { ... }

// CORRECT
ifLeft = { HealthStatus.CRITICAL.value }
if (status == HealthStatus.AT_RISK.value) { ... }
```

If an enum exists, use `EnumName.VALUE.value` everywhere — return values, comparisons, defaults. Hardcoded strings break silently when the enum changes; `enum.value` fails the compile.

### Anti-Pattern: `@Suppress` to silence ktlint

```kotlin
// WRONG — workaround
@Suppress("UNUSED_PARAMETER")
private fun createCommit(employeeId: UUID, authorUsername: String)

// CORRECT — fix the root cause, remove the unused param
private fun createCommit(authorUsername: String)
```

`@Suppress` is a workaround, not a fix. Remove the unused param and update call sites. Same for `@Suppress("DEPRECATION")` — use the non-deprecated replacement instead.

### Anti-Pattern: Catch-and-swallow exceptions

```kotlin
// WRONG — bug disappears silently
try {
  expensiveOp()
} catch (e: Exception) {
  fallbackValue
}

// CORRECT — log first, then fallback
runCatching { expensiveOp() }
  .onFailure { logger.warn("Operation failed, using fallback", it) }
  .getOrDefault(fallbackValue)
```

If you catch an exception and don't log it, you've hidden a future bug. Always log before falling back.

## Quick Reference

| Rule | Why |
|---|---|
| Reads use `transaction(db.replica)` | Read replica for performance |
| Writes use `transaction(db.primary)` | Primary for consistency |
| Multi-table writes in one transaction | Atomic rollback on partial failure |
| `text()` not `varchar()` | Project standard — Exposed reads as String |
| `UUIDTable` not `Table` | `id` is provided; declare `createdAt`/`updatedAt`/`deletedAt` per table (no `SoftDeleteTable` base) |
| `companion object { fun from(entity) }` inside Response DTO | One mapping pattern, no separate mapper files |
| Constructor injection only | Field injection breaks Micronaut DI |

## Lint commands

```bash
./gradlew ktlintCheck      # check
./gradlew ktlintFormat     # auto-fix what's fixable
```
