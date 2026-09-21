# PARK-IT Product Architecture V2

## 1. Product thesis

PARK-IT evolves from a parking finder into a marketplace for automotive spaces, access and related services.

Core abstraction:

```text
SPACE + CAPABILITIES + RULES + AVAILABILITY + ACCESS + BOOKING + PAYMENT + EVIDENCE
```

The same booking engine must support:
- 30-minute private parking
- overnight EV charging
- weekend garage rental
- monthly vehicle storage
- hourly detailing space
- DIY workshop bay
- professional bay + optional service provider

## 2. Domain model

Minimum entities:

- User
- Organisation
- HostProfile
- DriverProfile
- Vehicle
- VehicleCredential
- Space
- SpaceZone
- Capability
- Equipment
- HostRule
- PlatformRule
- AvailabilityRule
- AvailabilityException
- PriceRule
- Quote
- Booking
- BookingParticipant
- Payment
- Payout
- Refund
- Deposit/Hold
- AccessPolicy
- AccessCredential
- AccessDevice
- AccessEvent
- ANPREvent
- Charger
- ChargingSession
- EvidenceArtifact
- Incident
- Dispute
- Review
- Notification
- AuditEvent

Use immutable identifiers and explicit state transitions.

## 3. Booking state machine

Candidate state model:

```text
DRAFT
-> QUOTED
-> PENDING_APPROVAL | RESERVED
-> PAYMENT_PENDING
-> CONFIRMED
-> ACCESS_READY
-> ACTIVE
-> CHECKOUT_PENDING
-> COMPLETED

Terminal/alternate:
CANCELLED
EXPIRED
REJECTED
PAYMENT_FAILED
NO_SHOW
DISPUTED
REFUNDED
PARTIALLY_REFUNDED
```

Requirements:
- idempotency key on every externally retried mutation
- optimistic concurrency or transactional locking for slot allocation
- no double booking
- payment webhooks treated as untrusted/replayable input
- credential issuance only after the required booking/payment conditions are true
- credential revocation on cancellation/expiry
- append-only audit events for security-significant transitions

## 4. Space and capability model

Do not create separate incompatible schemas for ParkingSpot, Garage, Workshop and Storage.

Use one Space aggregate plus typed capabilities.

Example capabilities:
- PARKING
- COVERED
- SECURE_STORAGE
- EV_CHARGING
- DETAILING
- CAR_WASH
- DIY_REPAIR
- TYRE_WORK
- LIFT
- TOOLS
- WATER
- DRAIN
- POWER_230V
- POWER_400V
- COMPRESSED_AIR
- HEATING
- VENTILATION

Capabilities can have parameters, limits and evidence.

Example:
```json
{
  "code": "LIFT",
  "attributes": {
    "type": "two_post",
    "maxKg": 3500
  }
}
```

## 5. Rules engine

Three rule scopes:

1. Platform policy
2. Host/property policy
3. Booking-specific policy

Evaluation output must be deterministic:
- ALLOW
- DENY
- REQUIRE_APPROVAL
- REQUIRE_DEPOSIT
- REQUIRE_EVIDENCE
- REQUIRE_IDENTITY_LEVEL

Never let host rules weaken mandatory platform controls.

## 6. Availability

Availability is not a boolean.

Model:
- calendar schedule
- exceptions
- booking occupancy
- provider-reported availability
- sensor-reported occupancy
- freshness timestamp
- confidence
- source

Truth model example:
```text
AVAILABLE_CONFIRMED
AVAILABLE_PREDICTED
RESERVED
OCCUPIED
UNKNOWN
STALE
OUT_OF_SERVICE
```

## 7. Search and ranking

Use PostGIS for spatial filtering.

Pipeline:
1. intent parsing / structured filters
2. geo candidate generation
3. capability/vehicle/policy filtering
4. temporal availability check
5. route/walk-time enrichment
6. price quote
7. ranking
8. explainability payload

Ranking should support a weighted deterministic baseline before ML.

Candidate signals:
- route ETA
- walk ETA
- price
- availability confidence
- host quality
- access automation
- cancellation flexibility
- security
- charger compatibility

## 8. Access architecture

Core principle:

**A booking produces a scoped, revocable entitlement. Provider adapters turn that entitlement into physical access.**

Access types:
- ANPR plate allowlist
- NFC
- BLE
- QR
- PIN
- wallet credential
- remote unlock
- lockbox/key instructions

Never store one permanent shared PIN as a booking credential.

Each credential should contain or resolve to:
- booking ID
- subject/user/device
- permitted zone
- validity start/end
- nonce/version
- revocation state
- provider mapping

## 9. ANPR

Provider interface:

```ts
interface AnprProvider {
  createTemporaryPlateGrant(input): Promise<ProviderGrant>;
  revokePlateGrant(input): Promise<void>;
  verifyPlateEvent(input): Promise<NormalizedPlateEvent>;
}
```

Support cloud, local edge and camera-native implementations.

Normalize:
- plate
- country/region when known
- confidence
- camera/gate
- capturedAt
- image/evidence reference if permitted
- provider event id

Never make raw camera image retention mandatory.

## 10. PARK-IT Access Box

Separate edge project / package, not tightly coupled to the web app.

Security requirements:
- unique device identity
- mutual authentication
- signed commands
- anti-replay sequence/nonce
- local monotonic time strategy
- encrypted credential cache
- fail-safe relay configuration
- watchdog
- signed OTA
- health heartbeat
- tamper signal where hardware supports it
- offline policy is explicit, not accidental

Suggested transport:
- outbound MQTT over TLS or HTTPS long-poll/WebSocket
- no public inbound port

## 11. Payments

Create a PaymentGateway abstraction.

Core domain owns:
- amount
- currency
- booking
- fee
- refund intent
- payout intent
- ledger references

Provider owns:
- payment intent IDs
- connected account IDs
- webhooks
- settlement mechanics

Do not use Stripe objects as the database's domain model.

## 12. EV

Create an EvGateway abstraction.

Capabilities:
- charger discovery/registration
- status
- reservation mapping
- remote start/stop
- meter values
- transaction/session
- tariffs
- identity mapping

Reference OCPP/OCPI standards.

## 13. Evidence and disputes

Evidence artifacts are immutable records with:
- owner/subject
- booking
- type
- capturedAt
- retention class
- privacy classification
- checksum
- storage locator
- access policy

Do not retain evidence indefinitely by default.

## 14. Privacy

Data classes:
- public listing
- account
- payment metadata
- precise location
- vehicle/plate
- access credential
- access telemetry
- image/video evidence
- incident/dispute

Each class requires:
- purpose
- legal basis / contract mapping
- retention
- authorised roles
- export/delete handling
- logging/redaction rules

## 15. Target technical shape

Recommended initial modern stack:
- TypeScript
- Node.js current LTS
- Next.js or equivalent web/PWA shell
- PostgreSQL
- PostGIS
- Redis only where justified
- object storage for photos/evidence
- job queue
- provider adapter packages
- Playwright for browser E2E
- OpenAPI
- Docker dev/runtime
- self-hosted exact-SHA CI

Prefer a modular monolith first.

## 16. Non-functional gates

Before production:
- no machine-solvable P0/P1/P2
- multi-tenant/resource auth tests
- double-booking race tests
- payment replay tests
- access replay tests
- booking/access expiry tests
- offline access tests
- provider outage tests
- GDPR retention tests
- secret scanning
- dependency/license scan
- backup/restore evidence
- visible mobile golden path
- exact-SHA server CI green
