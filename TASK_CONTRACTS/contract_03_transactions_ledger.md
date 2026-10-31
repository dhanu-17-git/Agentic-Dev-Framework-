# Contract 03: Unified Transaction & 6-Step Escrow Ledger App

```json
{
  "task": "unified_transaction_escrow_ledger",
  "files": [
    "backend/transactions/models.py",
    "backend/transactions/serializers.py",
    "backend/transactions/views.py",
    "backend/transactions/urls.py",
    "backend/agrimart/settings.py",
    "backend/agrimart/urls.py"
  ],
  "changes": [
    "Create MarketTransaction model with 6-step lifecycle and escrow hash",
    "Implement contract note reference generator (CN-2026-XXXX)",
    "Expose GET /api/v1/transactions/ and GET /api/v1/transactions/<id>/",
    "Implement state transition endpoint POST /api/v1/transactions/<id>/action/",
    "Register 'transactions' in INSTALLED_APPS and wire /api/v1/transactions/ in agrimart/urls.py"
  ],
  "dependencies": ["users.CustomUser", "mandi.Commodity", "mandi.APMC"],
  "risk": "medium",
  "notes": "Ensures full contract note ledger and escrow audit trail for farmer & merchant roles."
}
```

## Implementation Specification for Codex

### 1. `backend/transactions/models.py`
- `MarketTransaction(UUIDPrimaryKeyModel, TimeStampedModel)`:
  - `class LifecycleStatus(models.TextChoices)`:
    1. `CONTRACT_ISSUED = "contract_issued", "Contract Note Issued"`
    2. `ESCROW_FUNDED = "escrow_funded", "Escrow Funded (100%)"`
    3. `GOODS_DISPATCHED = "goods_dispatched", "Goods Dispatched to Mandi"`
    4. `ASSAY_PASSED = "assay_passed", "APMC Quality Assay Passed"`
    5. `BUYER_ACCEPTED = "buyer_accepted", "Buyer Accepted Electronic Inward"`
    6. `FUNDS_RELEASED = "funds_released", "Funds Settled to Farmer Bank"`
  - Fields:
    - `transaction_id`: unique (e.g. `TXN-2026-LUD-7819`)
    - `contract_note_number`: unique (e.g. `CN-2026-LUD-1042`)
    - `seller`: FK `users.CustomUser` (Farmer)
    - `buyer`: FK `users.CustomUser` (Merchant)
    - `commodity`: FK `mandi.Commodity`
    - `apmc`: FK `mandi.APMC`
    - `quantity_quintals`: DecimalField(12, 2)
    - `agreed_rate`: DecimalField(12, 2)
    - `gross_amount`: DecimalField(14, 2)
    - `mandi_cess`: DecimalField(12, 2) (1.5%)
    - `escrow_fee`: DecimalField(12, 2) (0.5%)
    - `freight_deduction`: DecimalField(12, 2)
    - `assay_deduction`: DecimalField(12, 2)
    - `net_seller_payable`: DecimalField(14, 2)
    - `status`: choices=`LifecycleStatus.choices`, default=`CONTRACT_ISSUED`
    - `escrow_deposit_hash`: CharField(64)
    - `contract_note_pdf_url`: CharField(255, blank=True)

### 2. `backend/transactions/views.py`
- `TransactionListView(ListAPIView)`:
  - Returns transactions where `seller=request.user` OR `buyer=request.user`.
- `TransactionDetailView(RetrieveAPIView)`:
  - Returns complete 6-step lifecycle breakdown, deductions, and contract note specs.
- `TransactionActionView(APIView)`:
  - POST with action: `fund_escrow`, `dispatch_goods`, `pass_assay`, `accept_inward`, `release_funds`.
  - Transitions lifecycle state and logs timestamped verification audit record.

