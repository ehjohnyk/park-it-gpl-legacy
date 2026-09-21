# Devin Execution Brief — PARK-IT V2

## Mission

Transform the legacy PARK-IT parking prototype into a production-shaped automotive space marketplace, municipal/public parking and UVAR platform with smart access.

Do not treat this brief as a one-slice coding request.

Follow:
1. `AGENTS.md`
2. `MASTER_AUTONOMOUS_COMPLETION_PROTOCOL.md`
3. `SWE2_HIGH_ACCELERATION_PROTOCOL.md`
4. `SWE2_HIGH_EXECUTION_QUEUE.md`
5. `docs/PARK_IT_PRODUCT_ARCHITECTURE.md`
6. `docs/OPEN_SOURCE_REUSE_AUDIT.md`
7. `docs/MUNICIPAL_PUBLIC_PARKING_UVAR.md`

## Mandatory first action

Perform a repository-wide audit and save an executable master plan in the repository before substantial implementation.

Audit:
- current runtime/installability
- dependency age/vulnerabilities
- source ownership/license
- secrets/API keys
- legacy Mongo assumptions
- Google API assumptions
- tests
- CI/server CI
- public assets
- dead code
- node_modules tracked in git
- TODO/FIXME/HACK
- deployment assumptions

Do not delete legacy behaviour until its useful semantics are captured by tests/specification.

## Target product tracks

### P0 — Recovery / baseline
- reproducible clean install
- current supported runtime
- dependency/security scan
- remove tracked generated/dependency artefacts where safe
- environment/config hygiene
- baseline behavioural tests
- exact-SHA server CI

### P1 — Domain foundation
Implement typed domain model for:
- User / Host / Driver
- Vehicle
- Space / Zone
- Capability / Equipment
- Rules
- Availability
- Quote
- Booking

Acceptance:
- migrations
- validation
- state-machine tests
- race/double-booking tests
- no provider-specific objects leaking into domain

### P2 — Geo/search/routing
- PostGIS
- radius/destination search
- capability filtering
- deterministic ranking baseline
- MapLibre integration
- routing adapter with Valhalla/OSRM evaluation
- stale/unknown availability semantics

### P3 — Municipal/Public Parking + UVAR Foundation
- municipality/authority tenancy
- RegulatoryZone / CurbSegment / RegulationVersion
- authoritative/draft separation
- tariff plans
- permit types and digital permits
- resident/visitor/time-credit model
- public parking sessions
- vehicle policy profile
- Euro class / propulsion / size / weight / time based access rules
- ZTL / LEZ / ZEZ / congestion/access-fee decisions
- deterministic reason codes
- DATEX II/APDS adapter research
- GIS import (GeoJSON first)
- draft zone builder + topology conflict detection
- human approve/version/publish workflow
- route compliance evaluation

Acceptance:
- effective-dated/versioned policy tests
- overlapping-zone priority tests
- vehicle eligibility matrix tests
- no draft regulation can affect driver/enforcement decisions
- authoritative publish is audited
- provider/map-derived data licensing documented

### P4 — Municipal Enforcement + Occupancy
- public payment/permit verification
- enforcement observation model
- handheld/scan-car/fixed-ANPR adapter contracts
- occupancy sensor contract
- COMPLIANT / SUSPECTED_VIOLATION / UNKNOWN
- evidence package
- backoffice review queue
- no automatic legal penalty from uncertain observations

### P5 — Marketplace host flow
- create/edit/publish space
- photos
- schedules
- pricing
- permitted/prohibited activities
- custom host rules
- instant/manual approval
- vehicle dimension limits

### P6 — Booking
- quote
- reserve
- booking state machine
- cancellation/expiry
- check-in/check-out
- idempotency
- concurrency
- audit timeline

### P7 — Payments/payouts
- PaymentGateway abstraction
- provider sandbox integration
- connected-host onboarding
- platform fee
- host payout
- refund/cancel flows
- webhook replay/idempotency
- ledger reconciliation

No real-money production activation without owner approval.

### P8 — Smart Access
Build Access Policy / Credential Engine.

Adapters:
- QR
- temporary PIN
- remote unlock reference adapter
- ANPR provider interface
- NFC/BLE credential interface
- smart lock/gate provider interface

Acceptance:
- time-bounded credentials
- scoped zones
- revocation
- cancellation expiry
- replay prevention
- access audit events
- provider outage behaviour
- no permanent shared PIN design

### P9 — ANPR
- vehicle/plate binding
- temporary plate entitlement
- local/provider adapter proof
- confidence/freshness
- privacy/retention
- negative tests: wrong plate, stale booking, wrong gate, replay

Do not make one ANPR engine mandatory.

### P10 — PARK-IT Access Box foundation
Create only after threat model.

Requirements:
- outbound-only secure channel
- per-device identity
- signed/replay-safe commands
- relay + sensor abstraction
- local expiring entitlement cache
- offline policy
- health telemetry
- test/simulator first

Physical hardware activation is an external/owner gate.

### P11 — EV
- charger model
- OCPP/EV adapter boundary
- charger status/session
- booking/charger binding
- energy metering/price domain
- sandbox/simulator evidence

### P12 — Evidence/disputes/trust
- accepted-rule version
- check-in/out photos
- incident/dispute
- retention classes
- immutable hashes/metadata where appropriate
- role-based evidence access

### P13 — Mobile/PWA + Municipal Golden Paths

Marketplace visible E2E:
1. search destination/use case
2. select space
3. quote
4. booking
5. payment sandbox
6. credential appears
7. simulated gate/ANPR entry
8. active booking
9. checkout
10. settlement/review

Municipal driver E2E:
1. choose destination and vehicle
2. route crosses a regulated zone
3. RegulationVersion evaluates vehicle eligibility
4. show allow/deny/fee/permit reason
5. when denied, reroute to a compliant boundary/P+R parking option
6. when allowed, start a public parking session
7. apply permit/credit/discount
8. pay in sandbox
9. simulated enforcement observation verifies session/permit
10. session expires/closes with audit evidence

Municipal operator E2E:
1. import GeoJSON test zone
2. Zone Builder creates draft segments/rules
3. topology/conflict validation
4. authorised operator reviews and publishes version
5. future effective date activates deterministically
6. driver and enforcement decisions reference the exact published version
7. rollback/version-history evidence works

### P14 — Operations/release
- observability
- security headers
- rate limits
- backup/restore
- privacy controls
- admin/risk tools
- provider outage behaviour
- dependency/license report
- production/staging config
- exact-SHA CI

## Architecture constraints

- modular monolith first
- PostgreSQL/PostGIS preferred
- provider adapters at every volatile external boundary
- no fake "live" availability
- no hard-coded map/payment/access vendor in domain
- public regulations are effective-dated/versioned and authority-owned
- AI/GIS auto-zoning only creates drafts; an authorised human publishes authoritative policy
- physical access defaults fail-closed
- every external callback is replayable/untrusted
- all critical writes idempotent
- auditable state transitions
- privacy retention explicit
- secrets never committed

## Open-source evaluation

Evaluate, do not blindly import:
- parkingdata/spec (APDS)
- DATEX-II-EU/datexiimodel + current official DATEX II Parking/UVAR specifications
- maplibre/maplibre-gl-js
- valhalla/valhalla
- Project-OSRM/osrm-backend
- citrineos/citrineos-core
- parking-platform-org/parking-management-system
- Stripe marketplace samples

Record for each:
- exact repo/ref
- license
- intended reuse
- copied code yes/no
- security implications
- maintenance status
- owner gate if needed

## Licensing owner gate

Existing repository: GPLv3.

Do not change `LICENSE` without an explicit owner/legal decision.
Before mixing major V2 commercial code into legacy code, produce a copyright/dependency inventory and recommend clean options.

## Completion standard

A track is not complete because code compiles.

Required where applicable:
- unit tests
- integration tests
- negative paths
- hostile review
- runtime evidence
- visible E2E
- exact-SHA self-hosted/server CI
- docs/state reconciliation

Continue autonomously through dependency-ready machine-solvable tracks until only explicit external/owner/legal gates remain.
