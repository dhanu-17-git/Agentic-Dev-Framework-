# Decisions Log

> Do not load full memory unless explicitly requested.

- [2026-10-31] Extracted standalone `AgriMart-Market-Intelligence` from monolithic codebase; excised store/cart/checkout while preserving 100% auth and APMC market intelligence.
- [2026-10-31] Adopted strict separation between LLM reasoning and deterministic calculation: LLM never calculates math or hallucinates prices; Python engines calculate exact cess, freight, moisture deductions, and statistical time-series forecasts.
- [2026-10-31] Implemented Rule-Based zero-dependency LLM provider fallback to guarantee 100% test pass rate and offline operability without relying on external cloud LLM credits.
- [2026-10-31] Implemented pessimistic row-locking (`select_for_update`) for APMC delivery slot reservations to prevent harvest concurrency race conditions.
- [2026-10-31] Partitioned remaining tasks into 4 discrete, compact Task Contracts for Codex to maintain strict context limits and prevent token saturation.
