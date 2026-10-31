# Contract 04: Migrations, Historical Seeding & E2E Pytest Verification

```json
{
  "task": "migrations_seeding_and_e2e_verification",
  "files": [
    "backend/tests/test_ai_agent.py",
    "backend/tests/test_analytics_engines.py",
    "backend/tests/test_bookings_concurrency.py",
    "backend/tests/test_transactions_lifecycle.py"
  ],
  "changes": [
    "Run makemigrations and migrate for conversations, bookings, and transactions apps",
    "Execute seed_historical_ticker_series() to populate 90 days of daily tickers",
    "Add comprehensive pytest suite covering all analytics, forecasting, and booking concurrency",
    "Verify 100% test pass rate on SQLite test runner",
    "Validate frontend build (npm run build) and synchronize final files to VIBEATHON repository"
  ],
  "dependencies": ["pytest", "pytest-django", "conversations.*", "bookings.*", "transactions.*"],
  "risk": "low",
  "notes": "Guarantees full test harness coverage and demonstration readiness."
}
```

## Implementation Specification for Codex

### 1. Migrations Execution
```powershell
& "C:\Users\User\Desktop\MINI PROJECT\backend\.venv\Scripts\python.exe" manage.py makemigrations conversations bookings transactions
& "C:\Users\User\Desktop\MINI PROJECT\backend\.venv\Scripts\python.exe" manage.py migrate
```

### 2. Historical Price Seeding Command / Function
Invoke `ai.forecasting.seed_historical_tickers.seed_historical_ticker_series(90)`:
Populates 90 chronological daily price observations for all 10 commodities across all 6 APMCs.

### 3. Pytest Verification Suite
- `test_analytics_engines.py`:
  - Asserts exact net proceeds for wheat 100 Qtl (Gross - 1.5% cess - 0.5% escrow - Freight - Loading).
  - Asserts multi-mandi ranking produces valid sorted descending net proceeds.
  - Asserts 5-level market depth produces valid spread and non-negative slippage.
  - Asserts Holt-Winters forecasting produces 95% confidence intervals and valid MAPE metric.
- `test_bookings_concurrency.py`:
  - Asserts atomic slot reservation with `select_for_update()`.
  - Asserts booking rejection when requested quantity exceeds `remaining_capacity`.
  - Asserts idempotency key returns identical booking reference without deducting double capacity.
- `test_transactions_lifecycle.py`:
  - Asserts 6-step lifecycle transitions from `CONTRACT_ISSUED` to `FUNDS_RELEASED`.
  - Asserts escrow SHA-256 hash generation.
- `test_ai_agent.py`:
  - Asserts `/api/v1/assistant/chat/` handles mandi comparison, liquidity depth, price forecast, and slot booking prompts returning appropriate cards.

