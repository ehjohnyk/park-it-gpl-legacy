# SWE-2 HIGH EXECUTION QUEUE

PROGRAM_END: 2026-10-15
PROJECT: PARK-IT

RULE: Audit the legacy application before modernization; preserve useful working behavior while replacing brittle/deprecated boundaries incrementally.

TARGET PRODUCT: automotive space marketplace + booking + payments + smart physical access.

## ACTIVE

1. **PARK-SWE2-V2-000 — Product/Architecture Reset**
   - Product definition, open-source reuse audit and Devin execution brief.
   - Status: documentation prepared on `docs/park-it-mobility-space-marketplace-v2`.

## READY

1. **PARK-SWE2-001 — Whole-App Recovery + License/Dependency Baseline**
   - Reproducible install/test/start.
   - Legacy behaviour inventory.
   - Dependency/security scan.
   - Source ownership/license inventory.
   - Secrets/config hygiene.
   - Exact-SHA server CI baseline.

2. **PARK-SWE2-002 — V2 Domain Foundation**
   - Universal SPACE model.
   - Capabilities/equipment/rules.
   - Vehicle model.
   - Availability.
   - Quote/booking state machine.
   - PostgreSQL/PostGIS migrations.
   - Double-booking/concurrency tests.

3. **PARK-SWE2-003 — Geo + Search + Routing Engine**
   - APDS-aware provider boundary.
   - MapLibre frontend.
   - Routing adapter; evaluate Valhalla/OSRM.
   - Destination + intent search.
   - Deterministic explainable ranking.
   - Truthful stale/unknown availability.

4. **PARK-SWE2-004 — Host Marketplace**
   - Host onboarding.
   - Space publish/edit.
   - Schedules/pricing.
   - Permitted/prohibited activities.
   - Host rules.
   - Equipment/amenities.
   - Photos.
   - Approval modes.

5. **PARK-SWE2-005 — Booking + Settlement**
   - Quote/reserve/confirm/check-in/check-out.
   - Cancellation/expiry.
   - PaymentGateway.
   - Marketplace fee/payout/refund.
   - Sandbox reconciliation.
   - Webhook replay/idempotency.

6. **PARK-SWE2-006 — Smart Access Engine**
   - Access entitlement model.
   - QR and temporary PIN reference adapters.
   - Remote unlock adapter.
   - NFC/BLE interface.
   - Gate/smart-lock interface.
   - Revocation/expiry/replay tests.

7. **PARK-SWE2-007 — ANPR + Vehicle Access**
   - Plate binding.
   - Temporary allowlist grants.
   - Provider-neutral ANPR adapter.
   - Wrong-plate/wrong-gate/stale/replay tests.
   - Privacy retention.

8. **PARK-SWE2-008 — PARK-IT Access Box Foundation**
   - Threat model.
   - Device simulator.
   - Secure outbound transport.
   - Relay/sensor abstraction.
   - Signed/replay-safe command protocol.
   - Offline expiring entitlement cache.
   - No physical deployment without owner gate.

9. **PARK-SWE2-009 — EV / OCPP**
   - Charger model.
   - OCPP adapter.
   - Booking/charger reservation.
   - Session/energy/tariff records.
   - Simulator/sandbox evidence.

10. **PARK-SWE2-010 — Evidence + Trust + Disputes**
    - Accepted rules/version.
    - Check-in/out evidence.
    - Incident/dispute timeline.
    - Retention policy.
    - Role-based evidence access.

11. **PARK-SWE2-011 — Mobile/PWA Golden Path**
    - Customer-grade search/listing/booking/payment/access flow.
    - Visible E2E.
    - Responsive mobile browser evidence.

12. **PARK-SWE2-012 — Production/Staging Seal**
    - Environment config.
    - API-key hygiene.
    - Observability.
    - backup/restore.
    - security/privacy hostile audit.
    - dependency/license evidence.
    - exact-SHA server CI.
    - deployment only after owner-authorized gates.

## BLOCKED / OWNER OR EXTERNAL GATES

- Final commercial licensing model for V2 vs existing GPLv3 repository.
- Real payment production activation.
- Real map/geocoding/provider keys where paid/contractual.
- Authoritative external parking feeds.
- Real access-control hardware installation.
- Physical relay/gate activation.
- Real ANPR camera deployment.
- EV charger credentials / production OCPP environment.
- Legal terms, insurance/liability allocation, consumer-law and GDPR production review.
- Public production deployment.

## COMPLETED

Record package, PR, implementation SHA, CI SHA, merge SHA and runtime evidence here.
