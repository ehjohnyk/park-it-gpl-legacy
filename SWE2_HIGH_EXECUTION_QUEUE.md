# SWE-2 HIGH EXECUTION QUEUE

PROGRAM_END: 2026-10-15
PROJECT: PARK-IT

RULE: Audit the legacy application before modernization; preserve useful working behavior while replacing brittle/deprecated boundaries incrementally.

TARGET PRODUCT: mobility-space marketplace + municipal/public parking + UVAR/ZTL/LEZ + freight/special assets + curb management + booking + payments + smart physical access.

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
   - Heavy-vehicle compatible parking search and routing.
   - Special AssetEnvelope-compatible facility search.
   - Route/permit provenance for abnormal transport.
   - Deterministic explainable ranking.
   - Truthful stale/unknown availability.

4. **PARK-SWE2-004 — Municipal/Public Parking + UVAR**
   - Municipal authority/tenant model.
   - Zone/curb/entry geometry.
   - RegulationVersion + effective dates.
   - Tariffs.
   - Resident/visitor/business permits and credits.
   - Public parking sessions.
   - Vehicle eligibility incl. Euro class/propulsion/weight/dimensions.
   - Vans/HGVs/buses/trailers/articulated combinations.
   - Length/height/width/max-permissible-mass/axle/ADR/overnight restrictions.
   - Truck parking capacity/services/security + DATEX II SSTP boundary.
   - Dynamic curb/CDS + temporary rule model.
   - Boats/marina/dry storage, machinery yards and authorised aircraft ground-space foundations.
   - Roaming + Fleet/Logistics API contracts.
   - ZTL/LEZ/ZEZ/congestion/access-fee rules.
   - DATEX II/APDS boundaries.
   - GIS import + draft Zone Builder.
   - Human approve/version/publish.
   - Route compliance and explainable decisions.

5. **PARK-SWE2-005 — Municipal Enforcement + Occupancy**
   - Handheld/scan-car/fixed-ANPR contracts.
   - Sensor occupancy contracts.
   - Session/permit/exemption verification.
   - Compliance evaluation.
   - Suspected-violation evidence/backoffice.
   - No uncertain automatic legal penalty.

6. **PARK-SWE2-006 — Host Marketplace**
   - Host onboarding.
   - Space publish/edit.
   - Schedules/pricing.
   - Permitted/prohibited activities.
   - Host rules.
   - Equipment/amenities.
   - Photos.
   - Approval modes.

7. **PARK-SWE2-007 — Permit Orchestration**
   - PermitCase + AuthorityRulePack.
   - Jurisdiction/authority resolution.
   - Required applications/documents/consultations.
   - Authority connector interfaces + manual package fallback.
   - Administrative fee tracking/payment boundary.
   - Approved-route provenance.
   - Permit conditions.
   - Trip-day revalidation.
   - eID/e-signature/e-seal/e-delivery adapter boundary.
   - Document-AI extraction with provenance.
   - Toll/vignette/access-charge discovery and EETS-aware provider boundary.
   - eFTI future interoperability evaluation.
   - Fail closed on missing authoritative approval.

8. **PARK-SWE2-008 — Booking + Settlement**
   - Quote/reserve/confirm/check-in/check-out.
   - Cancellation/expiry.
   - PaymentGateway + MarketplaceGateway.
   - cards, Apple Pay, Google Pay sandbox checkout.
   - PayPal/SEPA/local method evaluation.
   - marketplace KYC/split/payout.
   - B2B/fleet billing.
   - Marketplace fee/payout/refund.
   - Sandbox reconciliation.
   - Webhook replay/idempotency.

9. **PARK-SWE2-009 — Smart Access Engine**
   - Access entitlement model.
   - QR and temporary PIN reference adapters.
   - Remote unlock adapter.
   - NFC/BLE interface.
   - Gate/smart-lock interface.
   - Revocation/expiry/replay tests.

10. **PARK-SWE2-010 — ANPR + Vehicle Access**
   - Plate binding.
   - Temporary allowlist grants.
   - Provider-neutral ANPR adapter.
   - Wrong-plate/wrong-gate/stale/replay tests.
   - Privacy retention.

11. **PARK-SWE2-011 — PARK-IT Access Box Foundation**
   - Threat model.
   - Device simulator.
   - Secure outbound transport.
   - Relay/sensor abstraction.
   - Signed/replay-safe command protocol.
   - Offline expiring entitlement cache.
   - No physical deployment without owner gate.

12. **PARK-SWE2-012 — EV / OCPP**
   - Charger model.
   - OCPP adapter.
   - Booking/charger reservation.
   - Session/energy/tariff records.
   - Simulator/sandbox evidence.

13. **PARK-SWE2-013 — Evidence + Trust + Disputes**
    - Accepted rules/version.
    - Check-in/out evidence.
    - Incident/dispute timeline.
    - Retention policy.
    - Role-based evidence access.

14. **PARK-SWE2-014 — Mobile/PWA Golden Path**
    - Customer-grade search/listing/booking/payment/access flow.
    - Visible E2E.
    - Responsive mobile browser evidence.

15. **PARK-SWE2-015 — Production/Staging Seal**
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
- Municipality-specific legal/VZN/regulation sources and production authority onboarding.
- Abnormal-load permit authority integrations and authoritative route/bridge/clearance data.
- Marina/port operator production integrations.
- Aerodrome/private-airfield operator production integrations and airside authorisation.
- Production enforcement/penalty integration.
- Production permit-authority/e-government credentials and agreements.
- Authority electronic-signature/qualified-signature requirements where applicable.
- National vehicle/emission registries or verification sources where access is restricted/contractual.
- Real access-control hardware installation.
- Physical relay/gate activation.
- Real ANPR camera deployment.
- EV charger credentials / production OCPP environment.
- Legal terms, insurance/liability allocation, consumer-law and GDPR production review.
- Public production deployment.

## COMPLETED

Record package, PR, implementation SHA, CI SHA, merge SHA and runtime evidence here.
