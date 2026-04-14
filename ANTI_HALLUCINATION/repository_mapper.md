# Repository Mapper

> **System:** Anti-Hallucination  
> **Priority:** CRITICAL — Must run before ANY implementation task

## Purpose
Generate a verified structural map of the project before any code changes.

## Mandatory Steps

### Step 1: Full Tree Scan
Scan the complete project directory tree. Record every file and directory.

### Step 2: Identify Entrypoints
Locate primary entrypoints: `app.py`, `main.py`, `index.js`, `manage.py`, or equivalent.

### Step 3: Map System Layers
Identify and categorize:
- **Routes** — HTTP request handlers
- **Services** — Business logic modules
- **Models** — Database schema and data access
- **Templates/Views** — Frontend rendering
- **Config** — Settings and environment

### Step 4: Detect Database Layer
Identify ORM, raw SQL, or database abstraction in use.

### Step 5: Map Dependencies
List internal module dependencies and external library usage.

## Required Output Format

```
REPOSITORY MAP
==============
Project: [name]
Entrypoint: [file]

Routes:
  - [file] → [description]

Services:
  - [file] → [description]

Models:
  - [file] → [description]

Templates:
  - [directory/file] → [description]

Config:
  - [file] → [description]

External Dependencies:
  - [library] → [version]
```

## Failure Modes

| Condition | Action |
|-----------|--------|
| Cannot identify entrypoint | **HALT.** Request human clarification. |
| Conflicting architecture patterns | **FLAG.** Report conflict before proceeding. |
| Repository too large to fully scan | Scan top 3 levels, report depth limit. |

## Rules
- **NEVER** skip this step before implementation
- **NEVER** generate code during mapping
- **NEVER** modify any files during mapping
- **NEVER** assume a file exists — verify it
- Output must be stored in `PROJECT_MEMORY/architecture_memory.md`
