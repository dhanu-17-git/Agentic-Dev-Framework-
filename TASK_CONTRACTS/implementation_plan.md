# AgriMart Agentic AI — Codex Execution Implementation Plan

Master execution roadmap dividing the remaining implementation into 4 isolated, context-bounded contracts following the Agentic Dev Framework schema.

## Contract Index

| Contract ID | Module Name | Primary Objective | Target Files | Risk | Status |
|---|---|---|---|---|---|
| **CONTRACT-01** | `conversations_and_agent_orchestrator` | Tool registration, AgentCoordinator with loop guards, chat history models & API | `backend/ai/tools/market_tools.py`, `backend/ai/agents/coordinator.py`, `backend/conversations/*` | Medium | Ready for Execution |
| **CONTRACT-02** | `bookings_rest_api_wiring` | DRF serializers, slot availability endpoint, atomic booking endpoint with idempotency | `backend/bookings/serializers.py`, `backend/bookings/views.py`, `backend/bookings/urls.py` | Low | Ready for Execution |
| **CONTRACT-03** | `unified_transaction_escrow_ledger` | 6-step lifecycle tracker, contract note generator (CN-2026-XXXX), escrow hash audit | `backend/transactions/*` | Medium | Ready for Execution |
| **CONTRACT-04** | `migrations_seeding_and_e2e_verification` | Database migrations, 90-day ticker seeding, pytest suite (analytics, bookings concurrency, lifecycle) | `backend/tests/*` | Low | Ready for Execution |

## Execution Protocol for Codex
1. Execute **one contract per session** to preserve token context.
2. Read the corresponding `contract_0X_*.md` contract file.
3. Apply changes strictly within the specified target files.
4. Run `pytest` to verify regressions.
5. Update `Agentic-Dev-Framework-/PROJECT_MEMORY/active_context.md`.
