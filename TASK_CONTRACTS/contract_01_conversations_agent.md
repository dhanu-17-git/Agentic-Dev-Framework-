# Contract 01: Conversations App & Agent Coordinator Orchestrator

```json
{
  "task": "conversations_and_agent_orchestrator",
  "files": [
    "backend/ai/tools/market_tools.py",
    "backend/ai/agents/coordinator.py",
    "backend/conversations/models.py",
    "backend/conversations/serializers.py",
    "backend/conversations/views.py",
    "backend/conversations/urls.py",
    "backend/agrimart/settings.py",
    "backend/agrimart/urls.py"
  ],
  "changes": [
    "Implement safe read-only tool wrappers in ai/tools/market_tools.py",
    "Implement AgentCoordinator with loop prevention (max 5 tool iterations)",
    "Create Conversation and Message models with JSON card_data payload",
    "Expose POST /api/v1/assistant/chat/ and GET /api/v1/assistant/conversations/",
    "Register 'conversations' in INSTALLED_APPS and include URLs in agrimart/urls.py"
  ],
  "dependencies": ["ai.llm.provider_factory", "analytics.*", "ai.forecasting.*"],
  "risk": "medium",
  "notes": "Ensures assistant chat queries return validated JSON card payloads for frontend dispatcher."
}
```

## Implementation Specification for Codex

### 1. `backend/ai/tools/market_tools.py`
Expose tool definitions and execution functions:
- `get_commodity_price(commodity_code: str)` -> returns current modal, min, max price from `DailyTicker`.
- `compare_mandis(commodity_code: str, quantity_quintals: float)` -> invokes `analytics.mandi_comparison.compare_regional_mandis()`.
- `get_market_liquidity(commodity_code: str, quantity_quintals: float)` -> invokes `analytics.liquidity_analysis.analyze_market_liquidity()`.
- `calculate_net_profit(commodity_code: str, quantity_quintals: float, moisture_pct: float)` -> invokes `analytics.profit_calculator.calculate_net_profit()`.
- `get_price_forecast(commodity_code: str, horizon_days: int)` -> invokes `ai.forecasting.predictor.generate_price_forecast()`.
- `find_verified_buyers(commodity_code: str, quantity_quintals: float)` -> invokes `analytics.buyer_matching.match_institutional_buyers()`.

### 2. `backend/ai/agents/coordinator.py`
- Class `AgentCoordinator`:
  - `run(user, user_message: str, conversation_id: str)`:
    1. Loads or initializes provider via `get_llm_provider()`.
    2. Sends user input + tool declarations.
    3. If tool call is requested, executes corresponding python function, appends tool response, loops max 5 times.
    4. Detects output card type and extracts card payload.
    5. Returns dict: `{"content": text, "card_type": ..., "card_data": {...}}`.

### 3. `backend/conversations/models.py`
- `Conversation`: `id` (UUID), `user` (FK `users.CustomUser`), `title`, `created_at`, `updated_at`.
- `Message`: `id` (UUID), `conversation` (FK `Conversation`), `role` (`user`, `assistant`), `content`, `card_type` (str, nullable), `card_data` (JSONField, default=dict), `created_at`.

### 4. `backend/conversations/views.py`
- `AssistantChatView` (`POST /api/v1/assistant/chat/`):
  - Receives `{"message": "...", "conversation_id": "..."}`.
  - Authenticates via JWT (`request.user`).
  - Calls `AgentCoordinator.run()`.
  - Persists messages and returns response JSON.

