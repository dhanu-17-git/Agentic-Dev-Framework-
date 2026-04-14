# Feature Contract

> **Produced by:** Architect  
> **Consumed by:** Planner  
> **Purpose:** Define a feature completely before implementation begins

---

## Feature Name
[Short descriptive name]

## Problem
[Describe the problem this feature solves]

## Scope
[What the feature MUST do — specific, measurable outcomes]

## Out of Scope
[What this feature will NOT do — explicit boundaries]

## Files Affected

| File | Action | Description |
|------|--------|-------------|
| `routes/example.py` | Modify | Add new endpoint |
| `services/example_service.py` | Create | Business logic |
| `templates/example.html` | Create | UI template |

## Data Requirements
[New fields, tables, or models required]

## API Changes

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /example | Fetch data |
| POST | /example | Submit data |

## Risks
[Possible edge cases, failure scenarios, or breaking changes]

## Acceptance Criteria
- [ ] [Condition 1 that must be satisfied]
- [ ] [Condition 2 that must be satisfied]
- [ ] [Tests pass]
- [ ] [No layer violations]

---

**This contract must be approved before proceeding to implementation planning.**
