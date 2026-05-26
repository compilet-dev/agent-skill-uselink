# API Design — Code Patterns

> This file is referenced by SKILL.md. Read it when implementing actual endpoints.

## Controller Template

```kotlin
@ExecuteOn(TaskExecutors.IO)    // REQUIRED for suspend
@Validated
@Controller("/api/v1/admin")
@Secured(OAuthSecurityRule.ADMIN)
class EmployeeController(
  private val employeeManager: EmployeeManager  // Managers ONLY
) {
  @Get("/employee")
  suspend fun getEmployee(@QueryValue id: UUID): EmployeeResponse {
    return employeeManager.findById(id).throwOrValue()
  }

  // List endpoints are @Post("/list") with offset/limit in the body — NEVER
  // @Get with page/size. See API_DESIGN.md > List Endpoints.
  @Post("/employees/list")
  suspend fun listEmployees(
    @Valid @Body request: EmployeeListRequest
  ): EmployeeListResponse {
    return employeeManager.list(
      offset = request.offset,
      limit = request.limit.coerceAtMost(100),
      status = request.status
    ).throwOrValue()
  }
}
```

## Request Models (paginated list)

In `module-client/request/{domain}/`:

```kotlin
@Serdeable
@Introspected
data class EmployeeListRequest(
  val status: String? = null,
  override val offset: Long = 0,
  override val limit: Int = 20
) : PageableRequest
```

Note: do NOT put `@field:Min` on a non-nullable body field with a Kotlin default — Micronaut-serde writes the JVM primitive default (0) when the JSON key is missing and the validator then rejects it. Clamp in the manager. See API_DESIGN.md.

## Response Models

All in `module-client/response/{domain}/`:

```kotlin
data class EmployeeResponse(
  val id: UUID,
  val name: String,
  val email: String,
  val status: String,
  val createdAt: Instant
) {
  companion object {
    fun from(entity: EmployeeEntity) = EmployeeResponse(
      id = entity.id,
      name = entity.name,
      email = entity.email,
      status = entity.status,
      createdAt = entity.createdAt
    )
  }
}

data class EmployeeListResponse(
  val items: List<EmployeeResponse>,
  val total: Long,
  val offset: Long,
  val limit: Int,
  val hasMore: Boolean
)
```

## Pagination Pattern

```kotlin
override suspend fun list(
  offset: Long,
  limit: Int,
  status: String?
): Either<ClientException, EmployeeListResponse> {
  val (items, total) = transaction(db.replica) {
    val query = EmployeesTable
      .selectAll()
      .where { EmployeesTable.deletedAt.isNull() }

    if (status != null) {
      query.andWhere { EmployeesTable.status eq status }
    }

    val total = query.count()
    val items = query
      .orderBy(EmployeesTable.createdAt, SortOrder.DESC)
      .limit(limit, offset)
      .map { convert(it) }

    items to total
  }

  return EmployeeListResponse(
    items = items.map { EmployeeResponse.from(it) },
    total = total,
    offset = offset,
    limit = limit,
    hasMore = (offset + items.size) < total
  ).right()
}
```

## Error Pattern

```kotlin
// Not found
val entity = repository.byId(id)
  ?: return ClientError.NOT_FOUND.asException().left()

// Already exists
val existing = repository.byEmail(email)
if (existing != null) {
  return ClientError.ALREADY_EXISTS.asException().left()
}

// Validation
if (request.name.isBlank()) {
  return ClientError.INVALID_INPUT.asException("Name is required").left()
}
```

## Factory Bean

```kotlin
@Factory
class EmployeeManagerFactory {
  @Singleton
  fun provideEmployeeManager(
    employeeRepository: EmployeeRepository,
    db: DatabaseContext
  ): EmployeeManager {
    return DefaultEmployeeManager(employeeRepository, db)
  }
}
```
