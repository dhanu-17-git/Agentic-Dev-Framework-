# AI Tool Kit — Production-Grade Agentic Development Framework

> **Created & Maintained by:** Dhanush M  
> **Version:** 3.1 — Elite Grade  
> **License:** MIT License © Dhanush M, 2026

---

**AI Tool Kit** is a state-of-the-art, anti-hallucination prompt framework for AI-assisted software engineering. Designed to turn any LLM (Claude 3.5 Sonnet, GPT-4o, Gemini) or AI IDE (Cursor, Windsurf, Copilot) into a deterministic, senior-level software architect. 

Instead of dealing with AI hallucinations, lost context, and generic code, this toolkit forces the AI to use **Project Memory**, **Deterministic Task Contracts**, and a strict **9-Stage Development Pipeline**.

## 🚀 Usage

```
1. Drop ai-tool-kit/ into any project root
2. Tell the AI: "Read ai-tool-kit/AI_ENTRYPOINT.md"
3. Ask for what you want in plain English
4. The AI auto-detects task type and loads only the files it needs
```

The entrypoint supports 5 modes — from a 2-file quick fix to a 12-file full pipeline — so context window usage is always minimal.

## 🔄 Pipeline

```
brainstorming
    ↓
repository_mapper
    ↓
architect → produces feature_contract
    ↓
planner → produces implementation_plan
    ↓
coder → produces patch_contract
    ↓
tester
    ↓
lint_and_validate
    ↓
reviewer
    ↓
security
```

## 📁 Structure — 82 Files / 14 Directories

```
ai-tool-kit/
├── AI_ENTRYPOINT.md                    ← Smart bootloader (start here)
├── SYSTEM_PROMPT.md                    ← Full execution rules
├── README.md
│
├── PIPELINE/                           (2 files)
│   ├── agent_pipeline.md              ← Stage execution order
│   └── agent_handoff_rules.md         ← Contract handoff protocol
│
├── TASK_CONTRACTS/                     (3 files)
│   ├── feature_contract.md            ← architect output
│   ├── implementation_plan.md         ← planner output
│   └── patch_contract.md             ← coder output
│
├── AGENTS/                             (4 files)
│   ├── architect.md
│   ├── builder.md
│   ├── reviewer.md
│   └── debugger.md
│
├── TEAM_AGENTS/                        (6 files)
│   ├── planner.md
│   ├── coder.md
│   ├── tester.md
│   ├── reviewer.md
│   ├── security.md
│   └── documenter.md
│
├── ANTI_HALLUCINATION/                 (4 files)
│   ├── repository_mapper.md
│   ├── file_verification.md
│   ├── layer_protection.md
│   └── change_scope_guard.md
│
├── PROJECT_MEMORY/                     (4 files)
│   ├── architecture_memory.md
│   ├── feature_memory.md
│   ├── tech_stack_memory.md
│   └── decision_log.md
│
├── REPO_INTELLIGENCE/                  (8 files)
│   ├── repo_summary.md
│   ├── repo_overview.md
│   ├── module_index.md
│   ├── api_map.md
│   ├── database_map.md
│   ├── service_dependency_map.md
│   ├── route_structure.md
│   └── db_relationships.md
│
├── SKILLS/                             (19 files)
│   ├── brainstorming.md
│   ├── tdd_workflow.md
│   ├── lint_and_validate.md
│   ├── git_workflow.md
│   ├── repo_reader.md
│   ├── architecture_planner.md
│   ├── patch_editor.md
│   ├── self_review.md
│   ├── secure_coding.md
│   ├── test_generator.md
│   ├── dependency_eval.md
│   ├── context_loader.md
│   ├── error_recovery.md
│   ├── anti_hallucination_guard.md
│   ├── frontend_design.md             ← UI aesthetics
│   ├── database_operations.md         ← Data safety rules
│   ├── silent_failure_hunter.md       ← Elite analyzer
│   ├── type_design_analyzer.md        ← Elite analyzer
│   └── code_simplifier.md             ← Elite analyzer
│
├── CONTEXT/                            (8 files)
│   ├── anti_hallucination_rules.md
│   ├── coding_rules.md
│   ├── load_rules.md
│   ├── ignore_rules.md
│   ├── file_priority.md
│   ├── naming_conventions.md
│   ├── memory_rules.md
│   └── intelligence_rules.md
│
├── WORKFLOWS/                          (7 files)
│   ├── project_bootstrap.md           ← Zero-to-one starter
│   ├── feature_development.md
│   ├── debugging.md
│   ├── refactoring.md
│   ├── testing.md
│   ├── code_review.md
│   └── ai_safe_feature_flow.md
│
├── TEAM_WORKFLOWS/                     (2 files)
│   ├── full_feature_pipeline.md
│   └── hotfix_pipeline.md
│
├── DOC_TEMPLATES/                      (5 files)
│   ├── architecture_template.md
│   ├── module_summary_template.md
│   ├── dev_rules_template.md
│   ├── feature_spec_template.md
│   └── api_documentation_template.md
│
└── IMPORTS/                            (5 files)
    └── antigravity-awesome-skills/
        ├── codebase_map.md
        ├── incremental_dev.md
        ├── debug_investigator.md
        ├── dependency_eval.md
        └── prompt_optimizer.md
```

## 🔒 Safeguards

| System | What It Prevents |
|--------|-----------------|
| Anti-Hallucination | Invented files, broken layers, large rewrites |
| Task Contracts | Free-form reasoning, skipped planning, untracked changes |
| Project Memory | Contradicted decisions, duplicated features |
| Pipeline Gates | Skipped stages, missing handoffs, orphaned contracts |
| Lint & Validate | Unchecked code quality |

## ⚡ Context-Aware Loading

The AI doesn't load all 82 files. The entrypoint auto-detects task type:

| Task | Files Loaded | Context Cost |
|------|-------------|--------------|
| Quick Fix | ~3 files | ~800 tokens |
| Debug | ~4 files | ~1,500 tokens |
| Code Review | ~4 files | ~1,500 tokens |
| Frontend Build | ~8 files | ~5,000 tokens |
| Project Bootstrap | ~10 files | ~12,000 tokens |
| Full Feature | ~15 files | ~18,000 tokens |

---

**Built with ❤️ by Dhanush M**
