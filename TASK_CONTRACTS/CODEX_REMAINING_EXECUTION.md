# CODEX MASTER EXECUTION TASK: Contracts 02, 03 & 04

> **Context:** AgriMart Agentic AI (`C:\Users\User\Desktop\VIBEATHON 2026\AgriMart-Market-Intelligence\backend`)  
> **Directive:** Implement remaining backend apps (`bookings` wiring, `transactions` ledger), run database migrations, seed 90-day historical time-series, and verify 100% test pass.

---

## TASK 1: Complete Contract 02 (`bookings` app registration & apps.py)

### 1.1 Create `backend/bookings/apps.py`
```python
from django.apps import AppConfig


class BookingsConfig(AppConfig):
    default_auto_field = "django.db.models.BigAutoField"
    name = "bookings"
```

### 1.2 Add `"bookings"` to `INSTALLED_APPS` in `backend/agrimart/settings.py`
Ensure `INSTALLED_APPS` includes `"bookings"`:
```python
INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    "corsheaders",
    "rest_framework",
    "common",
    "users",
    "mandi",
    "conversations",
    "bookings",
]
```

### 1.3 Add Bookings URL route to `backend/agrimart/urls.py`
```python
    path("api/v1/bookings/", include("bookings.urls")),
```

---

## TASK 2: Execute Contract 03 (`transactions` app)

### 2.1 Create `backend/transactions/models.py`
```python
import hashlib
import random
from decimal import Decimal
from django.conf import settings
from django.core.validators import MinValueValidator
from django.db import models
from common.models import TimeStampedModel, UUIDPrimaryKeyModel
from mandi.models import APMC, Commodity


class MarketTransaction(UUIDPrimaryKeyModel, TimeStampedModel):
    class LifecycleStatus(models.TextChoices):
        CONTRACT_ISSUED = "contract_issued", "Contract Note Issued"
        ESCROW_FUNDED = "escrow_funded", "Escrow Funded (100%)"
        GOODS_DISPATCHED = "goods_dispatched", "Goods Dispatched to Mandi"
        ASSAY_PASSED = "assay_passed", "APMC Quality Assay Passed"
        BUYER_ACCEPTED = "buyer_accepted", "Buyer Accepted Electronic Inward"
        FUNDS_RELEASED = "funds_released", "Funds Settled to Farmer Bank"

    transaction_id = models.CharField(max_length=40, unique=True, db_index=True)
    contract_note_number = models.CharField(max_length=40, unique=True, db_index=True)
    seller = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.PROTECT, related_name="sales_transactions")
    buyer = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.PROTECT, related_name="purchase_transactions")
    commodity = models.ForeignKey(Commodity, on_delete=models.PROTECT)
    apmc = models.ForeignKey(APMC, on_delete=models.PROTECT)
    quantity_quintals = models.DecimalField(max_digits=12, decimal_places=2, validators=[MinValueValidator(0.1)])
    agreed_rate = models.DecimalField(max_digits=12, decimal_places=2, validators=[MinValueValidator(0)])
    gross_amount = models.DecimalField(max_digits=14, decimal_places=2, validators=[MinValueValidator(0)])
    mandi_cess = models.DecimalField(max_digits=12, decimal_places=2, default=Decimal("0.00"))
    escrow_fee = models.DecimalField(max_digits=12, decimal_places=2, default=Decimal("0.00"))
    freight_deduction = models.DecimalField(max_digits=12, decimal_places=2, default=Decimal("0.00"))
    assay_deduction = models.DecimalField(max_digits=12, decimal_places=2, default=Decimal("0.00"))
    net_seller_payable = models.DecimalField(max_digits=14, decimal_places=2, validators=[MinValueValidator(0)])
    status = models.CharField(max_length=30, choices=LifecycleStatus.choices, default=LifecycleStatus.CONTRACT_ISSUED)
    escrow_deposit_hash = models.CharField(max_length=64, blank=True)
    contract_note_pdf_url = models.CharField(max_length=255, blank=True)

    class Meta:
        indexes = [
            models.Index(fields=["seller", "-created_at"]),
            models.Index(fields=["buyer", "-created_at"]),
            models.Index(fields=["status"]),
        ]

    def save(self, *args, **kwargs):
        if not self.transaction_id:
            rand_code = "".join(random.choices("0123456789ABCDEF", k=4))
            self.transaction_id = f"TXN-2026-{self.apmc.market_code[-3:]}-{rand_code}"
        if not self.contract_note_number:
            rand_cn = "".join(random.choices("0123456789ABCDEF", k=4))
            self.contract_note_number = f"CN-2026-{self.apmc.market_code[-3:]}-{rand_cn}"
        if not self.escrow_deposit_hash:
            raw = f"{self.transaction_id}:{self.seller.phone}:{self.gross_amount}"
            self.escrow_deposit_hash = hashlib.sha256(raw.encode("utf-8")).hexdigest()
        super().save(*args, **kwargs)

    def __str__(self) -> str:
        return f"{self.contract_note_number} | {self.commodity.name} ({self.status})"
```

### 2.2 Create `backend/transactions/serializers.py`
```python
from rest_framework import serializers
from transactions.models import MarketTransaction


class MarketTransactionSerializer(serializers.ModelSerializer):
    seller_name = serializers.CharField(source="seller.full_name", read_only=True)
    seller_phone = serializers.CharField(source="seller.phone", read_only=True)
    buyer_name = serializers.CharField(source="buyer.full_name", read_only=True)
    commodity_name = serializers.CharField(source="commodity.name", read_only=True)
    commodity_code = serializers.CharField(source="commodity.code", read_only=True)
    apmc_name = serializers.CharField(source="apmc.name", read_only=True)

    class Meta:
        model = MarketTransaction
        fields = [
            "id",
            "transaction_id",
            "contract_note_number",
            "seller_name",
            "seller_phone",
            "buyer_name",
            "commodity_name",
            "commodity_code",
            "apmc_name",
            "quantity_quintals",
            "agreed_rate",
            "gross_amount",
            "mandi_cess",
            "escrow_fee",
            "freight_deduction",
            "assay_deduction",
            "net_seller_payable",
            "status",
            "escrow_deposit_hash",
            "contract_note_pdf_url",
            "created_at",
            "updated_at",
        ]


class TransactionTransitionInputSerializer(serializers.Serializer):
    action = serializers.ChoiceField(
        choices=[
            "fund_escrow",
            "dispatch_goods",
            "pass_assay",
            "accept_inward",
            "release_funds",
        ]
    )
```

### 2.3 Create `backend/transactions/views.py`
```python
from rest_framework import status
from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response
from rest_framework.views import APIView

from transactions.models import MarketTransaction
from transactions.serializers import MarketTransactionSerializer, TransactionTransitionInputSerializer


class TransactionListView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request):
        user = request.user
        txns = MarketTransaction.objects.filter(
            models.Q(seller=user) | models.Q(buyer=user)
        ).select_related("seller", "buyer", "commodity", "apmc").order_by("-created_at")
        return Response(MarketTransactionSerializer(txns, many=True).data)


class TransactionDetailView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request, transaction_id):
        user = request.user
        txn = MarketTransaction.objects.filter(
            models.Q(id=transaction_id) | models.Q(transaction_id=transaction_id) | models.Q(contract_note_number=transaction_id)
        ).filter(models.Q(seller=user) | models.Q(buyer=user)).first()

        if not txn:
            return Response({"detail": "Transaction not found."}, status=status.HTTP_404_NOT_FOUND)

        return Response(MarketTransactionSerializer(txn).data)


class TransactionTransitionView(APIView):
    permission_classes = [IsAuthenticated]

    TRANSITIONS = {
        "fund_escrow": MarketTransaction.LifecycleStatus.ESCROW_FUNDED,
        "dispatch_goods": MarketTransaction.LifecycleStatus.GOODS_DISPATCHED,
        "pass_assay": MarketTransaction.LifecycleStatus.ASSAY_PASSED,
        "accept_inward": MarketTransaction.LifecycleStatus.BUYER_ACCEPTED,
        "release_funds": MarketTransaction.LifecycleStatus.FUNDS_RELEASED,
    }

    def post(self, request, transaction_id):
        serializer = TransactionTransitionInputSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        action = serializer.validated_data["action"]

        user = request.user
        txn = MarketTransaction.objects.filter(
            models.Q(id=transaction_id) | models.Q(transaction_id=transaction_id)
        ).filter(models.Q(seller=user) | models.Q(buyer=user)).first()

        if not txn:
            return Response({"detail": "Transaction not found."}, status=status.HTTP_404_NOT_FOUND)

        new_status = self.TRANSITIONS[action]
        txn.status = new_status
        txn.save(update_fields=["status", "updated_at"])

        return Response(MarketTransactionSerializer(txn).data)
```

### 2.4 Create `backend/transactions/urls.py`
```python
from django.urls import path
from transactions.views import TransactionDetailView, TransactionListView, TransactionTransitionView

urlpatterns = [
    path("", TransactionListView.as_view(), name="transaction-list"),
    path("<str:transaction_id>/", TransactionDetailView.as_view(), name="transaction-detail"),
    path("<str:transaction_id>/transition/", TransactionTransitionView.as_view(), name="transaction-transition"),
]
```

### 2.5 Add `"transactions"` to `INSTALLED_APPS` and URL conf
- In `backend/agrimart/settings.py`, add `"transactions"` to `INSTALLED_APPS`.
- In `backend/agrimart/urls.py`, add:
```python
    path("api/v1/transactions/", include("transactions.urls")),
```

---

## TASK 3: Execute Contract 04 (Migrations, Seeding & Test Suite)

### 3.1 Run Migrations
```powershell
& "C:\Users\User\Desktop\MINI PROJECT\backend\.venv\Scripts\python.exe" manage.py makemigrations conversations bookings transactions
& "C:\Users\User\Desktop\MINI PROJECT\backend\.venv\Scripts\python.exe" manage.py migrate
```

### 3.2 Run Historical Ticker Seeder
Run a Python one-liner to populate 90 days of daily tickers:
```powershell
& "C:\Users\User\Desktop\MINI PROJECT\backend\.venv\Scripts\python.exe" -c "import django, os; os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'agrimart.settings'); django.setup(); from ai.forecasting.seed_historical_tickers import seed_historical_ticker_series; count = seed_historical_ticker_series(90); print(f'Seeded {count} daily tickers.')"
```

### 3.3 Create Comprehensive Pytest Suite in `backend/tests/test_agentic_system.py`
```python
import pytest
from decimal import Decimal
from rest_framework.test import APIClient
from users.models import CustomUser
from mandi.models import APMC, Commodity, DailyTicker
from bookings.models import DeliverySlot, SlotBooking
from bookings.services import create_slot_booking_atomic
from transactions.models import MarketTransaction
from analytics.profit_calculator import calculate_net_profit
from analytics.mandi_comparison import compare_regional_mandis
from analytics.liquidity_analysis import analyze_market_liquidity
from ai.forecasting.predictor import generate_price_forecast
from ai.agents.coordinator import AgentCoordinator


@pytest.fixture
def auth_user(db):
    user = CustomUser.objects.create(
        phone="9876543210",
        full_name="Ramesh Kumar (Farmer)",
        role=CustomUser.Role.FARMER,
        is_verified=True,
    )
    user.set_password("SecurePass1234!")
    user.save()
    return user


@pytest.mark.django_db
def test_profit_calculator_deterministic_deductions():
    profit = calculate_net_profit(
        quantity_quintals=100.0,
        rate_per_quintal=2400.0,
        distance_km=40.0,
        moisture_pct=11.0,
    )
    assert profit.gross_value == 240000.0
    assert profit.mandi_cess == 3600.0  # 1.5%
    assert profit.escrow_fee == 1200.0  # 0.5%
    assert profit.loading_unloading_cost == 1500.0  # 15/qtl
    assert profit.freight_cost == 720.0  # 0.18 * 40 * 100
    assert profit.net_proceeds == 232980.0
    assert profit.effective_price_per_quintal == 2329.8


@pytest.mark.django_db
def test_mandi_comparison_returns_sorted_proceeds():
    res = compare_regional_mandis("WHEAT_SHARBAT", quantity_quintals=100.0)
    assert res["commodity"] is not None
    assert len(res["mandis"]) > 0
    # Must be sorted descending by estimatedNetProceeds
    proceeds = [m["estimatedNetProceeds"] for m in res["mandis"]]
    assert proceeds == sorted(proceeds, reverse=True)


@pytest.mark.django_db
def test_market_liquidity_depth_and_spread():
    liq = analyze_market_liquidity("WHEAT_SHARBAT", target_quantity_quintals=100.0)
    assert len(liq["bids"]) == 5
    assert len(liq["asks"]) == 5
    assert liq["best_bid"] < liq["best_ask"]
    assert liq["spread_abs"] > 0
    assert liq["total_bid_vol"] > 0


@pytest.mark.django_db
def test_forecasting_generates_confidence_intervals():
    fc = generate_price_forecast("WHEAT_SHARBAT", horizon_days=14)
    assert len(fc["forecastPoints"]) == 14
    assert fc["predictedPrice"] > 0
    assert fc["confidenceLower"] <= fc["predictedPrice"] <= fc["confidenceUpper"]
    assert fc["mape"] >= 0


@pytest.mark.django_db
def test_slot_booking_atomic_concurrency(auth_user):
    apmc = APMC.objects.first()
    import datetime
    slot = DeliverySlot.objects.create(
        apmc=apmc,
        date=datetime.date.today(),
        start_time=datetime.time(8, 0),
        end_time=datetime.time(10, 0),
        max_capacity_quintals=Decimal("100.00"),
        booked_capacity_quintals=Decimal("0.00"),
    )
    commodity = Commodity.objects.first()

    # Successful booking
    b1 = create_slot_booking_atomic(
        farmer_user=auth_user,
        slot_id=str(slot.id),
        commodity_code=commodity.code,
        quantity_quintals=60.0,
        vehicle_number="PB-10-XY-1234",
    )
    assert b1.booking_reference.startswith("SLOT-2026-")

    # Refresh slot
    slot.refresh_from_db()
    assert slot.booked_capacity_quintals == Decimal("60.00")

    # Attempt overbooking (requires 50, only 40 left)
    from rest_framework.exceptions import ValidationError
    with pytest.raises(ValidationError):
        create_slot_booking_atomic(
            farmer_user=auth_user,
            slot_id=str(slot.id),
            commodity_code=commodity.code,
            quantity_quintals=50.0,
            vehicle_number="PB-10-XY-5678",
        )


@pytest.mark.django_db
def test_assistant_chat_endpoint_execution(auth_user):
    client = APIClient()
    client.force_authenticate(user=auth_user)

    response = client.post(
        "/api/v1/assistant/chat/",
        {"message": "Compare mandis for 100 quintals of wheat"},
        format="json",
    )
    assert response.status_code == 201
    data = response.json()
    assert "conversation" in data
    assert "message" in data
    assert data["message"]["card_type"] == "comparison"
    assert "mandis" in data["message"]["card_data"]
```

### 3.4 Run Pytest
```powershell
& "C:\Users\User\Desktop\MINI PROJECT\backend\.venv\Scripts\pytest.exe"
```
Ensure all tests pass with 100% green.

