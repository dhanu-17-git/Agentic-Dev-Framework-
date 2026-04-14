# Layer Protection

> **System:** Anti-Hallucination  
> **Priority:** CRITICAL — Enforced on every code change

## Purpose
Prevent AI from violating architecture layer boundaries.

## Layer Definitions

| Layer | Responsibility | Allowed Dependencies |
|-------|---------------|---------------------|
| **Routes** | HTTP handling, request validation, response formatting | Services only |
| **Services** | Business logic, orchestration, data transformation | Models, external APIs |
| **Models** | Database schema, queries, data access | Database driver only |
| **Templates** | UI rendering, display logic | None (receives data) |
| **Config** | Environment, settings, constants | None |

## Violation Rules

### Routes MUST NOT
- Contain business logic
- Execute database queries directly
- Import model classes for direct manipulation
- Handle file system operations

### Services MUST NOT
- Import HTTP request/response objects
- Return HTML or render templates
- Access request headers or cookies directly

### Models MUST NOT
- Contain business logic
- Import route or service modules
- Handle HTTP concepts

### Templates MUST NOT
- Contain backend logic
- Execute database queries
- Import Python/JS modules

## Failure Modes

| Violation | Action |
|-----------|--------|
| Business logic in route | **REJECT.** Move logic to service layer. |
| DB query in route | **REJECT.** Create service method. |
| HTTP context in service | **REJECT.** Pass data as parameters. |
| Logic in template | **REJECT.** Move to service, pass result. |
| Circular dependency | **REJECT.** Refactor to break cycle. |

## Enforcement
Before committing any code change, verify:
- [ ] Each modified file respects its layer boundaries
- [ ] No cross-layer imports were introduced
- [ ] Data flows downward: Route → Service → Model
