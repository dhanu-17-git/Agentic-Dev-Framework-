# Change Scope Guard

> **System:** Anti-Hallucination  
> **Priority:** CRITICAL — Enforced on every code change

## Purpose
Prevent AI from making unnecessarily large changes to the codebase.

## Rules

### Minimal Patch Policy
- Edit ONLY the specific functions or blocks that need to change
- Never rewrite an entire file when only a few lines need modification
- Preserve existing code formatting and style
- Preserve existing comments unless they are incorrect

### Size Limits

| Change Type | Max Lines | Approval Required |
|-------------|:---------:|:-----------------:|
| Bug fix | 20 | No |
| Small feature addition | 50 | No |
| Module modification | 100 | Architect review |
| New file creation | 200 | Architect approval |
| Full file rewrite | Any | **Explicit human approval** |

### Before Making Changes
1. Count the number of lines you plan to change
2. If >50 lines in a single file → justify why a smaller change is not possible
3. If rewriting entire file → **STOP** and request explicit approval
4. If creating a new file → verify architect has approved the addition

## Prohibited Actions
- Rewriting files that only need small fixes
- Deleting and recreating files instead of patching
- Reformatting code that is not part of the task
- Adding features not requested in the current task
- Changing function signatures without reviewing all callers

## Failure Modes

| Condition | Action |
|-----------|--------|
| Change exceeds 50 lines without justification | **HALT.** Provide justification or reduce scope. |
| Full file rewrite attempted | **HALT.** Return: `SCOPE_VIOLATION: Full rewrite requires approval.` |
| Unrequested feature added | **REJECT.** Remove addition, implement only what was asked. |
| Existing code reformatted unnecessarily | **REJECT.** Revert formatting changes. |
