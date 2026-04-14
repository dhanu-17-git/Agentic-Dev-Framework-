# File Verification Guard

> **System:** Anti-Hallucination  
> **Priority:** CRITICAL — Must run before ANY file edit

## Purpose
Prevent AI from editing, referencing, or importing files that do not exist.

## Rules

### Before Editing Any File
1. Verify the file exists in the repository map
2. Verify the file path is correct (no typos, no assumed paths)
3. Verify the file belongs to the correct system layer
4. If file does not exist → **DO NOT CREATE IT** unless architect has approved

### Before Importing Any Module
1. Verify the module exists in the codebase
2. Verify the import path matches actual file structure
3. Never import from assumed or invented paths

### Before Referencing Any Function
1. Verify the function exists in the target file
2. Verify the function signature matches expected parameters
3. Never call functions that have not been confirmed to exist

## Failure Modes

| Condition | Action |
|-----------|--------|
| File does not exist | **HALT.** Return: `FILE_NOT_FOUND: [path]. Architecture update required.` |
| Import path invalid | **HALT.** Return: `INVALID_IMPORT: [path]. Verify module structure.` |
| Function not found | **HALT.** Return: `FUNCTION_NOT_FOUND: [name] in [file]. Verify API.` |
| Path contains typo | **HALT.** Return: `PATH_MISMATCH: Expected [x], found [y].` |

## Validation Checklist
- [ ] File exists on disk
- [ ] File is in the correct directory for its layer
- [ ] File has been read and contents are understood
- [ ] No phantom references to non-existent code
