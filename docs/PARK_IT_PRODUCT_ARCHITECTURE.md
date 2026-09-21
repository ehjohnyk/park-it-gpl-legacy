# PARK-IT Product Architecture V2

## 1. Product thesis

PARK-IT evolves from a parking finder into a unified platform for private automotive-space marketplace inventory, public/municipal parking, urban vehicle access regulations, smart access and related services.

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
- Municipality
- MunicipalAuthority
- RegulatoryZone
- CurbSegment
- Regulation
- RegulationVersion
- TariffPlan
- PermitType
- Permit
- EntitlementPolicy
- EntitlementGrant
- TariffBenefit
- ReservedSpaceClass
- VerificationEvidence
- CreditAccount
- PublicParkingSession
- VehiclePolicyProfile
- AccessRegulationDecision
- EnforcementObservation
- ViolationCandidate
- SensorDevice
- OccupancyObservation
- VehicleEnvelope
- VehicleCombination
- HeavyVehicleRestriction
- TruckParkingFacility
- TruckParkingCapacity
- FacilityService
- SecurityClassification
- Asset
- AssetEnvelope
- AssetTransportConfiguration
- OversizeMovementPlan
- PermitReference
- StagingFacility
- MarineFacility
- Berth
- AircraftGroundSpace
- MachineryStorageProfile
- CurbSegmentPolicy
- TemporaryRule
- RoamingProviderMapping
- FleetAccount

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

Four rule scopes:

1. Platform safety/privacy policy
2. Public-authority regulation
3. Host/property policy
4. Booking-specific policy

Evaluation output must be deterministic:
- ALLOW
- DENY
- REQUIRE_APPROVAL
- REQUIRE_DEPOSIT
- REQUIRE_EVIDENCE
- REQUIRE_IDENTITY_LEVEL

Never let host rules weaken mandatory platform controls or public-authority regulations. Public regulations must be versioned, effective-dated and attributable to an authority.

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

## 7A. Municipal/public policy engine

Public regulation is authoritative policy, not a normal HostRule.

Rule scopes:
1. platform safety/privacy controls
2. public authority regulation
3. private host/property rules
4. booking-specific terms

A public RegulationVersion must bind:
- authority
- legal/source reference
- geometry/version
- effectiveFrom/effectiveTo
- vehicle/user applicability
- schedule
- tariff/permit requirement
- exemptions
- publication state

Vehicle-aware predicates may include:
- vehicle category
- Euro emission class
- propulsion/fuel
- zero-emission status
- weight/dimensions
- residence/permit
- disability/emergency/service status
- time/day/season
- event/air-quality/congestion trigger

Decision outputs:
- ALLOW
- DENY
- ALLOW_WITH_FEE
- REQUIRE_PERMIT
- REQUIRE_REGISTRATION
- REQUIRE_PAYMENT
- EXEMPT
- WARNING
- UNKNOWN

Every decision must return the exact RegulationVersion and reason codes used.

## 7B. Public zoning / GIS

Use PostGIS for authoritative/draft geometries:
- area polygon
- curb/road line
- point restriction/entry
- gate
- individual space

Provide import pipelines for:
- GeoJSON
- Shapefile/GPKG
- municipal WFS/WMS where appropriate
- OpenStreetMap/open road graph
- other licensed map/GIS sources

Automatic/AI zoning is a proposal system only:

```text
SOURCE DATA
-> NORMALISE
-> PROPOSE BOUNDARIES/SEGMENTS
-> DETECT GAPS/OVERLAPS
-> ASSIGN DRAFT RULES
-> HUMAN REVIEW
-> VERSIONED PUBLISH
```

No inferred map restriction becomes authoritative without an authorised publish action.

## 7C. Public parking sessions and permits

Support:
- resident/visitor/business permits
- time-credit accounts
- discounts/exemptions
- zone ticket/session purchase
- max-stay/grace rules
- event/dynamic tariffs
- P+R
- extension where policy permits

The authoritative parking session must be verifiable by enforcement adapters.

## 7C. Entitlement / benefit engine

Municipal special treatment must use a reusable entitlement engine.

Subjects:
- person/account
- vehicle
- fleet/organisation
- permit

Predicates:
- zone/space
- vehicle propulsion (BEV/PHEV/etc.)
- emission class
- disability/accessible-parking entitlement
- residency
- permit class
- organisation role
- time/effective period
- credits/quota

Actions:
- free parking
- percentage/fixed discount
- tariff override
- max-stay override
- zone-entry permission
- reserved-space eligibility
- fee/restriction exemption
- time-window access
- credit grant/consumption

Pricing output must preserve:
```text
base tariff
+/- adjustments
+ matched entitlement/rule IDs
= final tariff
```

Accessible-space eligibility and pricing are separate decisions.

Examples such as BEV/PHEV discounts, ŤZP benefits or supply permits are municipality-configured policy; the core must not presume a universal legal entitlement.


## 7D. Heavy vehicle / freight model

Do not represent a truck only by plate/category.

Create a VehicleEnvelope / VehicleCombination model covering:
- category/use
- rigid/articulated
- dimensions
- maximum permissible mass
- actual mass only when verified/required
- axle information
- trailer
- combination length
- ADR
- refrigerated/reefer characteristics

Regulation predicates can operate on these attributes.

TruckParkingFacility must expose capacity, compatibility and services so search can reject physically or legally invalid candidates before ranking.

DATEX II Safe and Secure Truck Parking should be evaluated as an import/export boundary.

Freight ranking signals:
- legal route compatibility
- vehicle-envelope compatibility
- HGV slot availability/freshness
- reservation availability
- detour/ETA
- security classification
- driver facilities
- ADR/reefer requirements
- price


## 7E. Special Asset / oversize compatibility

Generalise the object being stored from Vehicle to Asset while keeping Vehicle as a specialised first-class profile.

AssetEnvelope contains common dimensions/mass/security/environmental needs plus specialised extensions.

Compatibility is two-dimensional:

```text
SPACE_COMPATIBLE
ROUTE_OR_OPERATOR_AUTHORISED
```

Examples:
- abnormal road transport requires staging-space compatibility plus permit/route status;
- boat requires berth/dry-storage compatibility;
- work machine may require low-loader access and ground-bearing capacity;
- aircraft requires hangar/stand compatibility plus aerodrome/operator authority.

Unknown critical compatibility data returns `REQUIRE_OPERATOR_APPROVAL` or `UNKNOWN`, not ALLOW.

### Oversize movement

OversizeMovementPlan may reference:
- transport combination envelope
- indivisible cargo envelope
- authority permit
- authorised corridor
- validity window
- escort state
- staging points
- source/provenance

PARK-IT does not generate an authoritative abnormal-load permit unless directly integrated with the competent authority.

### Marine

Marine compatibility can include LOA, beam, draught, air draught, displacement, trailer, shore power, hoist/slipway and covered-storage requirements.

### Aviation ground space

AircraftGroundSpace can represent operator-authorised hangar, tie-down, GA stand, helicopter stand or maintenance bay.

Compatibility may use wingspan, length, tail/rotor clearance, weight class, towing, security zone and ground-handling requirements. Aerodrome/operator authority is mandatory for controlled airside use.

### Machinery

Machinery storage can include hardstanding/ground-bearing capability, low-loader access, gate envelope, spill containment, security, charging/power and maintenance capabilities.

## 7F. Dynamic curb / temporary rules

CurbSegmentPolicy is effective-dated and can change use by schedule or event:
- loading
- parking
- taxi/pickup
- resident
- disabled/accessibility
- bus
- micromobility
- no stopping
- temporary closure

TemporaryRule must have source, authority, start/end, precedence and automatic expiry.

Evaluate Open Mobility Foundation CDS as an interoperability boundary.

## 7G. Roaming and fleet integration

Roaming adapters map PARK-IT sessions/payments to external city/operator systems without assuming cross-authority permit equivalence.

FleetAccount/API supports bulk vehicles/assets, time windows, permit references, access decisions, loading/curb rules, compatible staging/parking and reservations.


## 7H. Enforcement

Normalize enforcement observations from:
- handheld app
- scan car
- fixed ANPR
- gate/entry camera
- occupancy sensor

Evaluation:

```text
Observation
-> location + active RegulationVersion
-> plate/vehicle normalisation
-> active session/permit/exemption
-> policy decision
-> COMPLIANT | SUSPECTED_VIOLATION | UNKNOWN
```

PARK-IT stores the evidence/audit trail and produces a violation candidate. Jurisdiction-specific legal penalty issuance remains outside the generic core unless explicitly integrated and authorised.

## 7I. Standards

Public interoperability boundaries should evaluate:
- DATEX II Parking
- DATEX II UVAR / machine-interpretable traffic regulation profiles
- APDS
- National Access Point feeds

Internal models remain provider/standard-neutral.


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
