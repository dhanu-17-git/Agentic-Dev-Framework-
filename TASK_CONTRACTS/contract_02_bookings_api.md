# Contract 02: Bookings REST APIs & URL Wiring

```json
{
  "task": "bookings_rest_api_wiring",
  "files": [
    "backend/bookings/serializers.py",
    "backend/bookings/views.py",
    "backend/bookings/urls.py",
    "backend/agrimart/settings.py",
    "backend/agrimart/urls.py"
  ],
  "changes": [
    "Create DeliverySlotSerializer and SlotBookingSerializer",
    "Implement SlotAvailabilityView (GET /api/v1/bookings/availability/)",
    "Implement CreateSlotBookingView (POST /api/v1/bookings/)",
    "Implement MyBookingsView (GET /api/v1/bookings/my-bookings/)",
    "Register 'bookings' in INSTALLED_APPS and wire /api/v1/bookings/ in agrimart/urls.py"
  ],
  "dependencies": ["bookings.models", "bookings.services"],
  "risk": "low",
  "notes": "Wraps create_slot_booking_atomic with DRF validation and permissions."
}
```

## Implementation Specification for Codex

### 1. `backend/bookings/serializers.py`
- `DeliverySlotSerializer`: ModelSerializer for `DeliverySlot`, exposing `id`, `apmc`, `date`, `start_time`, `end_time`, `gate_number`, `max_capacity_quintals`, `booked_capacity_quintals`, `remaining_capacity`, `status`.
- `SlotBookingSerializer`: ModelSerializer for `SlotBooking`, exposing `id`, `booking_reference`, `slot_detail`, `commodity_name`, `estimated_quantity_quintals`, `vehicle_number`, `driver_phone`, `status`, `qr_code_token`, `created_at`.
- `CreateSlotBookingInputSerializer`: Serializer with `slot_id` (UUID), `commodity_code` (str), `quantity_quintals` (Decimal > 0), `vehicle_number` (str), `driver_phone` (str, optional), `idempotency_key` (UUID, optional).

### 2. `backend/bookings/views.py`
- `SlotAvailabilityView(APIView)`:
  - Query parameters: `apmc` (market code or UUID), `date` (YYYY-MM-DD, default today).
  - Seeds default slots if none exist via `seed_default_apmc_slots()`.
  - Returns serialized slot availability.
- `CreateSlotBookingView(APIView)`:
  - Permission: `IsAuthenticated`.
  - Validates input serializer.
  - Calls `create_slot_booking_atomic()`.
  - Returns 201 Created with `SlotBookingSerializer`.
- `MyBookingsView(APIView)`:
  - Permission: `IsAuthenticated`.
  - Filters `SlotBooking.objects.filter(farmer=request.user).order_by('-created_at')`.

### 3. `backend/bookings/urls.py`
- `path("availability/", SlotAvailabilityView.as_view(), name="slot-availability")`
- `path("", CreateSlotBookingView.as_view(), name="create-booking")`
- `path("my-bookings/", MyBookingsView.as_view(), name="my-bookings")`

