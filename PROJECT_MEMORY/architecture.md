# Architecture Memory

> Load ONLY: relevant architecture section.

- **Project:** AgriMart Agentic AI (`AgriMart-Market-Intelligence`)
- **Primary Interface:** Conversational AI Copilot (`AssistantPage.jsx` default post-login)
- **Secondary Interface:** Agricultural Commodity Market Terminal (`MarketTerminalPage.jsx` wrapping `AgriStockExchangeView`)
- **Backend Architecture:**
  - Django 5 + Django REST Framework + FastAPI Realtime
  - Database: SQLite (`market_intelligence.db.sqlite3`) with Argon2/PBKDF2 auth
  - LLM Orchestration: Provider factory (Rule-Based zero-dep fallback + Gemini + OpenAI-compatible) with deterministic Python analytics tools
  - Analytics Core: Pure formulaic profit calculation (1.5% APMC cess, 0.5% escrow, freight, moisture deduction), multi-mandi ranking by net in-hand proceeds, 5-level market depth/liquidity, Holt-Winters exponential trend forecasting (95% CI, MAPE/RMSE)
  - Procurement & Transactions: Atomic slot booking with row-level locks (`select_for_update`) and 6-step cryptographic escrow ledger
