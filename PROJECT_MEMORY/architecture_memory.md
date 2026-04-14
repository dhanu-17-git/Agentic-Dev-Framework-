# Architecture Memory

> **System:** Project Memory  
> **Updated by:** Architect agent after each feature implementation

## Purpose
Store persistent architecture decisions so AI never contradicts established patterns.

## Current Architecture

### System Layers

| Layer | Directory | Responsibility |
|-------|-----------|---------------|
| Routes | `routes/` | HTTP request handling, input validation, response formatting |
| Services | `services/` | Business logic, data transformation, orchestration |
| Models | `models/` | Database schema, queries, data access |
| Templates | `templates/` | UI rendering, display only |
| Config | `config/` | Environment settings, constants |

### Data Flow
```
[Client Request] → Route → Service → Model → [Database]
                                                  ↓
[Client Response] ← Route ← Service ← Model ← [Result]
```

### Architecture Decisions

| Decision | Rationale | Date |
|----------|-----------|------|
| *Example: Use SQLite* | *Lightweight, no server setup needed* | *YYYY-MM-DD* |
| | | |

### Anti-Patterns (NEVER DO)
- Database queries in route handlers
- Business logic in templates
- Direct file I/O in routes
- Circular imports between layers
- Hardcoded credentials anywhere

---

**Update this file whenever architecture changes are made.**
