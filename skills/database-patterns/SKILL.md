---
name: database-patterns
description: Database design patterns including schema design, migrations, soft deletes, and Exposed ORM. Use when creating tables, writing migrations, or implementing repositories.
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
---

# Database Patterns — Quick Reference

## Hard Rules

| Rule | Do | Don't |
|------|------|-------|
| Data types | TEXT | VARCHAR |
| Primary keys | UUID | SERIAL, BIGINT |
| Soft delete | deleted_at TIMESTAMP | DELETE FROM |
| Foreign keys | App-level validation | REFERENCES, ON DELETE CASCADE |
| Standard columns | id, created_at, updated_at, deleted_at | Skip any of these |

## Migration Template

SQL migration with table, trigger, and partial indexes for soft delete.

> See code-templates.md for the complete SQL template.

## Exposed Table Object

Extend `UUIDTable` and declare standard timestamp columns explicitly per table — there is no `SoftDeleteTable` base class in this codebase. Use `text()` not `varchar()`.

> See code-templates.md for the complete template, and `.claude/rules/database/SCHEMA.md` for why we declare timestamps inline.

## Entity Data Class

Implement `Entity<Instant>`, include all business fields + `createdAt`, `updatedAt`, `deletedAt`.

> See code-templates.md for the complete template.

## Repository Pattern

Interface + `Default*` implementation. Reads on `db.replica`, writes on `db.primary`. Soft delete via `deletedAt` update. `convert()` method maps `ResultRow` to entity.

> See code-templates.md for the full interface + implementation code.

## When Creating a New Table

Full checklist:
1. SQL migration file (next number in sequence)
2. Table object in `module-repository/table/`
3. Entity data class in `module-repository/entity/`
4. Enum/constants in `module-repository/constant/` (if needed)
5. Repository interface + implementation
6. Factory bean for repository
7. Repository tests

## Flyway Rules

- NEVER add a migration that fills a gap in deployed sequence
- NEVER rename an already-deployed migration file
- Migration numbers must be sequential from the latest
- Keep migrations simple and focused (one table per migration)

## Gotchas

- **Only `id` uses `.value` -- everything else is direct.** `row[Table.id].value` gives UUID, but `row[Table.projectId]` already returns UUID. Adding `.value` to non-id columns causes compile errors.
- **Always `gen_random_uuid()`, never `uuid_generate_v4()`.** Many managed Postgres providers (Railway, Render, etc.) don't ship the `uuid-ossp` extension; `uuid_generate_v4()` fails at migration time with `function uuid_generate_v4() does not exist`. `gen_random_uuid()` is built into PostgreSQL 13+ and always works. This applies to both column defaults AND `INSERT ... SELECT` in backfill migrations.
- **No `SoftDeleteTable` base class.** Every table declares `createdAt`, `updatedAt`, `deletedAt` itself on top of `UUIDTable`. Declare them explicitly per table for clarity.
- **Forgetting `WHERE deleted_at IS NULL` on indexes wastes space.** Every index on a soft-delete table should be partial. Full indexes include dead records nobody queries.
- **Text columns that hold JSON should still use `text()` in Exposed.** JSONB in Postgres, `text()` in Kotlin, serialize/deserialize in the entity layer. **And** when writing to a JSONB column, wrap scalars with `objectMapper.writeValueAsString(...)` — a bare UUID/string fails with `invalid input syntax for type json`.
- **Idempotent seeds: never `ON CONFLICT (col) WHERE deleted_at IS NULL`.** Partial indexes silently let the conflict target miss a soft-deleted row. Use the UPDATE-then-INSERT-WHERE-NOT-EXISTS pattern in `SCHEMA.md`.
- **Once a migration is on `dev`/`master`, the file body is frozen.** Editing it (even a comment) flips the Flyway checksum and crash-loops the next deploy. Add a new migration instead. See `MIGRATIONS.md > The Other Recurring Pattern: Checksum Mismatch`.
