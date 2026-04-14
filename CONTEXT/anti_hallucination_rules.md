# Anti-Hallucination Rules

> **System:** Context  
> **Priority:** CRITICAL — Load before any implementation task

## Master Rules

### Rule 1: Map Before Code
AI must run `ANTI_HALLUCINATION/repository_mapper.md` before writing any code.
No exceptions.

### Rule 2: Verify Before Edit
AI must verify every file exists before editing.
Follow `ANTI_HALLUCINATION/file_verification.md`.

### Rule 3: Respect Layers
AI must not violate architecture layer boundaries.
Follow `ANTI_HALLUCINATION/layer_protection.md`.

### Rule 4: Minimize Changes
AI must make the smallest possible change.
Follow `ANTI_HALLUCINATION/change_scope_guard.md`.

### Rule 5: No Invention
AI must NEVER:
- Invent files that do not exist
- Invent functions that have not been verified
- Invent architecture patterns not present in the codebase
- Invent database tables or columns not in the schema
- Assume any path, import, or API without verification

### Rule 6: Halt on Uncertainty
If AI is unsure about any of the following, it must HALT and ask:
- Whether a file exists
- Whether a function exists
- Whether a design pattern is appropriate
- Whether a change is within scope

## Loading Order
1. `ANTI_HALLUCINATION/repository_mapper.md`
2. `ANTI_HALLUCINATION/file_verification.md`
3. `ANTI_HALLUCINATION/layer_protection.md`
4. `ANTI_HALLUCINATION/change_scope_guard.md`
