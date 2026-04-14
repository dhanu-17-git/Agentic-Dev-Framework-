# AI Tool Kit — Entrypoint

> **Created by:** Dhanush M  
> **Version:** 3.1  
> **Purpose:** Single bootloader for AI-assisted development. Read this file FIRST.

This is a structured AI engineering toolkit with anti-hallucination safeguards, task contracts, and project memory. It contains 75+ files, but **you never load all of them.** You load only what the current task requires.

---

## Core Rules (Always Active — No Exceptions)

- **NEVER** invent file paths — verify they exist before editing
- **NEVER** rewrite entire files — use minimal, targeted patch edits
- **NEVER** hallucinate architecture, dependencies, or module names
- **ALWAYS** respect existing project structure and naming conventions
- **ALWAYS** ask for clarification before making destructive changes
- **ALWAYS** stop and report if a step cannot be completed safely

---

## Step 1: Detect the Task Type

Read the user's request and classify it into ONE of these modes:

| Mode | When to Use | Context Cost |
|------|-------------|--------------|
| 🔧 **Quick Fix** | Bug fix, typo, small edit, one-file change | Light (~3 files) |
| 🐛 **Debug** | Investigating errors, tracing issues, diagnosing failures | Light (~4 files) |
| 👀 **Code Review** | Reviewing code quality, security, conventions | Light (~4 files) |
| 🎨 **Frontend Build** | Building UI, pages, components, design work | Medium (~8 files) |
| 🏗️ **Feature Build** | New feature, multi-file change, architecture decisions | Full (~12 files) |
| 🚀 **Project Bootstrap**| Starting from an empty folder, scaffolding a new app | Full (~10 files) |

---

## Step 2: Load Only What the Mode Requires

### 🔧 Quick Fix Mode
```
Load → CONTEXT/coding_rules.md
Load → ANTI_HALLUCINATION/repository_mapper.md
Then → Fix the issue with minimal edits
```

### 🚀 Project Bootstrap (Zero-to-One) Mode
```
Load → SYSTEM_PROMPT.md (Phase 1-3)
Load → CONTEXT/coding_rules.md
Load → WORKFLOWS/project_bootstrap.md     ← The core bootstrap engine
Load → PROJECT_MEMORY/tech_stack_memory.md
Load → PROJECT_MEMORY/architecture_memory.md
Load → PROJECT_MEMORY/decision_log.md
Then → Scaffold, initialize memory, transition to feature build
```

### 🐛 Debug Mode
```
Load → CONTEXT/coding_rules.md
Load → ANTI_HALLUCINATION/repository_mapper.md
Load → WORKFLOWS/debugging.md
Load → AGENTS/debugger.md
Then → Investigate, trace, and resolve
```

### 👀 Code Review Mode
```
Load → CONTEXT/coding_rules.md
Load → ANTI_HALLUCINATION/repository_mapper.md
Load → WORKFLOWS/code_review.md
Load → AGENTS/reviewer.md
Then → Review, report findings, suggest fixes
```

### 🎨 Frontend Build Mode
```
Load → SYSTEM_PROMPT.md (Phase 1–5)
Load → CONTEXT/coding_rules.md
Load → ANTI_HALLUCINATION/repository_mapper.md
Load → SKILLS/brainstorming.md
Load → SKILLS/frontend_design.md          ← Design thinking before coding
Load → AGENTS/builder.md
Load → SKILLS/lint_and_validate.md
Load → SKILLS/self_review.md
Then → Design direction first, then implement
```

### 🏗️ Feature Build Mode (Full Pipeline)
```
Load → SYSTEM_PROMPT.md                   ← Full execution rules

Phase 1: Context
  → CONTEXT/anti_hallucination_rules.md
  → CONTEXT/coding_rules.md
  → CONTEXT/file_priority.md
  → ANTI_HALLUCINATION/repository_mapper.md

Phase 2: Intelligence
  → REPO_INTELLIGENCE/repo_summary.md

Phase 3: Memory
  → PROJECT_MEMORY/architecture_memory.md
  → PROJECT_MEMORY/feature_memory.md
  → PROJECT_MEMORY/tech_stack_memory.md
  → PROJECT_MEMORY/decision_log.md

Phase 4: Plan
  → SKILLS/brainstorming.md               ← Clarify requirements
  → AGENTS/architect.md                   ← Design architecture
  → PIPELINE/agent_pipeline.md            ← Execution order
  → PIPELINE/agent_handoff_rules.md       ← Contract handoff rules
  → Produce TASK_CONTRACTS/feature_contract.md
  → Produce TASK_CONTRACTS/implementation_plan.md

Phase 5: Build
  → AGENTS/builder.md
  → If frontend → SKILLS/frontend_design.md
  → Produce TASK_CONTRACTS/patch_contract.md per file
  → SKILLS/git_workflow.md

Phase 6: Validate
  → WORKFLOWS/testing.md
  → SKILLS/tdd_workflow.md
  → SKILLS/lint_and_validate.md

Phase 7: Review
  → AGENTS/reviewer.md
  → TEAM_AGENTS/security.md
  → SKILLS/silent_failure_hunter.md
  → SKILLS/type_design_analyzer.md
  → SKILLS/code_simplifier.md
```

---

## Step 3: Execute and Protect

### Development Pipeline (Feature Build only)
```
brainstorming → repository_mapper → architect → planner → coder → tester → lint_and_validate → reviewer → security
```

### Fail-Safe Conditions
The AI must **stop execution** if:
- Repository structure cannot be mapped
- Required files are missing
- A change would rewrite an entire file unnecessarily
- A change violates architecture layers
- A workflow step cannot be completed safely

**Report the issue instead of continuing.**

---

## Toolkit Map (Reference Only — Do Not Load All)

```
ai-tool-kit/
├── AI_ENTRYPOINT.md          ← YOU ARE HERE — start point
├── SYSTEM_PROMPT.md          ← Full execution rules (Feature Build only)
├── SKILLS/                   ← How-to guides (loaded per task)
├── AGENTS/                   ← Role personas (loaded per task)
├── TEAM_AGENTS/              ← Specialist roles (loaded per task)
├── WORKFLOWS/                ← Step-by-step playbooks (loaded per task)
├── ANTI_HALLUCINATION/       ← Safety guards (core files always loaded)
├── CONTEXT/                  ← Ground rules (core files always loaded)
├── PROJECT_MEMORY/           ← Persistent state (Feature Build only)
├── REPO_INTELLIGENCE/        ← Project maps (Feature Build only)
├── TASK_CONTRACTS/           ← Phase outputs (Feature Build only)
├── DOC_TEMPLATES/            ← Doc generation templates
├── IMPORTS/                  ← External imported skills
└── TEAM_WORKFLOWS/           ← Multi-step pipelines
```
