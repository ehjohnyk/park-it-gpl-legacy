# Open Source / Standards Reuse Audit

Date: 2026-09-21

Purpose: identify projects and standards that can accelerate PARK-IT without blindly copying incompatible code.

## Principles

1. Prefer standards over vendor-specific coupling.
2. Prefer permissive licenses for reusable implementation components.
3. Treat AGPL/GPL dependencies carefully when commercial distribution/SaaS strategy matters.
4. Reuse architecture and protocol knowledge even when source-code reuse is not appropriate.
5. Every adopted dependency needs a license/security/maintenance review.
6. No external repository is trusted merely because it is popular.

## Strong candidates

### APDS — parking data interoperability
Repository: `parkingdata/spec`
License: MIT for the API specification.

Why useful:
- global parking data model/API direction
- external parking provider interoperability
- resource identifiers/versioning
- reduces risk of inventing a proprietary parking data schema

Recommendation:
- use APDS concepts/contracts at provider boundaries
- do not force the internal marketplace model to equal APDS exactly

### DATEX II — public parking and UVAR interoperability
Official model repository: `DATEX-II-EU/datexiimodel`
Primary authority: DATEX II / CEN specifications and recommended profiles.

Why useful:
- European reference model for traffic/travel data
- dedicated Parking and UVAR user domains
- permanent access restrictions can represent vehicle class, dimensions, weight, propulsion/fuel and emission-class restrictions
- suitable boundary for municipalities, navigation providers and national access points

Recommendation:
- implement DATEX II import/export as an adapter/profile, not the internal database schema
- track the current published profiles/version during implementation
- licensing/use terms for standards/model assets must be verified before copying generated/model artifacts

### UVAR Box / EU UVAR data work
Why useful:
- reference for digitising low/zero emission zones, limited traffic zones, parking regulations and congestion/access schemes
- reinforces the need for machine-readable zones, restrictions, exemptions and driver information

Recommendation:
- model PARK-IT Regulation/RegulationVersion so UVAR information can be imported/exported without losing provenance
- support foreign/non-resident vehicle scenarios

### ParkDots — competitive/product benchmark
Not an open-source reuse candidate.

Observed public capabilities include:
- municipal parking payments
- resident/visitor time credits/cards
- enforcement handheld workflows
- scan-car ANPR
- fixed ANPR/virtual gates
- occupancy sensors/camera monitoring
- navigation to free spaces
- municipal parking-space management and reporting

Recommendation:
- treat this as a minimum competitive benchmark for the CITY product
- differentiate with unified private marketplace + garages/workshops + smart access + general UVAR/vehicle-policy engine + assisted zoning


### Open Mobility Foundation Curb Data Specification (CDS)
Repository: `openmobilityfoundation/curb-data-specification`
Repository licence file: Creative Commons Attribution 4.0.

Why useful:
- static and dynamic curb/parking regulation exchange
- loading/pickup/dropoff use cases
- events and curb activity/metrics
- city/operator API patterns

Recommendation:
- use CDS as an import/export/interoperability reference for curb management
- keep PARK-IT's internal RegulationVersion/CurbSegment model richer and jurisdiction-neutral
- verify attribution/version/licensing obligations for any copied schema/material

### Abnormal transport / aviation standards
Not a single open-source component to embed.

Relevant authorities/standards to track:
- EU vehicle weights/dimensions framework
- national/regional abnormal-load permit processes
- EASA/ICAO aerodrome rules and operator procedures

Recommendation:
- encode provider/authority adapters and provenance rather than attempting to create one global legal rule table
- treat permit/operator confirmation as external authority state


### MapLibre GL JS
Repository: `maplibre/maplibre-gl-js`

Why useful:
- mature open-source vector map renderer
- avoids hard dependency on one commercial map frontend

Recommendation:
- strong candidate for web/PWA maps
- validate tile/geocoding provider terms separately

### Valhalla / OSRM
Repositories:
- `valhalla/valhalla`
- `Project-OSRM/osrm-backend`

Why useful:
- open routing engines over OpenStreetMap data
- route ETA / driving / walking calculations can stay provider-neutral

Recommendation:
- benchmark both for PARK-IT use cases
- keep RoutingProvider interface so hosted commercial routing remains possible

### PostgreSQL + PostGIS
Why useful:
- geospatial queries
- distance/radius filtering
- spatial indexing
- mature transactional data model

Recommendation:
- default V2 persistence choice unless audit finds a specific blocker

### Stripe Connect marketplace references
Reference repositories include Stripe marketplace/Connect samples.

Why useful:
- seller onboarding
- connected accounts
- application/platform fee
- payouts
- webhook patterns

Recommendation:
- use as payment-flow reference, not as our domain architecture
- isolate behind PaymentGateway
- never treat browser payment success as settlement truth

### CitrineOS / OCPP implementations
Repositories:
- `citrineos/citrineos-core`
- `steve-community/steve`
- `opencpo/opencpo`

Why useful:
- OCPP message handling
- charging station lifecycle
- RFID/auth concepts
- remote actions
- charger status/session concepts

Recommendation:
- CitrineOS is the strongest architectural reference to evaluate first
- decide whether to integrate an external CSMS or implement only the minimal adapter needed
- do not fork a full charging platform unless product requirements justify it

### Parallax Parking Management System
Repository: `parking-platform-org/parking-management-system`
License stated in README: MIT.

Why useful:
- clean example of separating ANPR/OCR into a provider/microservice boundary
- vehicle registration / plate normalisation concepts
- hardware-integration boundary

Recommendation:
- study contracts and testing patterns
- do not copy ML assets/models without verifying their provenance and licenses
- PARK-IT should keep ANPR optional/provider-neutral

### Home Assistant / ESPHome ecosystem
Repositories:
- `home-assistant/core`
- `esphome/esphome`

Why useful:
- broad real-world smart-gate/relay/device ecosystem
- useful integration target for early prototypes
- demonstrates local-first device control patterns

Recommendation:
- PARK-IT Access Box should have its own secure protocol
- Home Assistant/MQTT can be an optional adapter, not the security authority

### Permit workflow benchmarks — VEMAGS / RDW-DWO
These are not open-source components to copy; they are workflow/product references.

Germany:
- VEMAGS electronically supports heavy/oversize transport application, authority consultation, statements, conditions and issuance.

Netherlands:
- RDW/DWO supports exceptional-transport applications, route proposals and consultation with road managers.

Slovakia:
- abnormal/overweight transport is handled as special road use under national road law, with competence/consents depending on route and authority.

Recommendation:
- PARK-IT should build PermitAuthorityAdapter + AuthorityRulePack abstractions.
- Country integrations must rely on current authoritative processes/agreements rather than scraping portals without permission.
- Where no API exists, generate structured application packages and keep a human/operator-assisted submission path.

### Payment platform references
Strong current candidates to evaluate:
- Adyen for Platforms
- Stripe Connect
- PayPal/Braintree platform/checkout capabilities

Required evaluation:
- Apple Pay / Google Pay
- cards
- PayPal
- SEPA/open banking/local methods
- marketplace seller onboarding/KYC
- split payments
- preauthorisation/capture
- refunds/chargebacks
- payouts
- country support
- pricing
- PSD2/SCA
- reconciliation/webhooks

Recommendation:
- choose one primary PSP for V2 sandbox, keep PaymentGateway/MarketplaceGateway interfaces.
- do not implement our own custody/KYC/payout rails.


## Caution / do not directly embed without licensing decision

### OpenALPR
Repository: `openalpr/openalpr`
License: AGPL-3.0.

Useful as historical ANPR reference, but direct reuse can have strong copyleft implications.

Recommendation:
- do not make it the default embedded PARK-IT component
- prefer an ANPR provider interface and separately reviewed implementations

### Cal.com
Core is AGPLv3/open-core with commercial portions.

Useful as a scheduling UX/architecture reference, but PARK-IT booking logic is specialised enough that importing the application would create unnecessary coupling and licensing complexity.

Recommendation:
- study concepts only unless legal/product review explicitly approves reuse

## Parking repositories found in broad GitHub search

Many repositories named "parking reservation system" are student/demo projects with small scope, weak operational evidence or missing security models.

Recommendation:
- do not adopt them wholesale
- mine only isolated ideas after license/security review
- our booking/access/payment requirements exceed typical parking demos

## What PARK-IT should build itself

These are core differentiation and should remain first-class domain code:

- universal SPACE/capability model
- host rules + platform rules
- booking state machine
- marketplace pricing/quoting
- access entitlement engine
- credential lifecycle
- ANPR entitlement binding
- multi-provider smart access abstraction
- evidence/dispute timeline
- parking/garage/workshop unified search
- reputation model
- service/equipment add-ons
- policy-aware ranking
- municipal RegulationVersion model
- UVAR/LEZ/ZTL policy evaluation
- assisted zone builder + topology validation
- public permit/session/enforcement normalization
- special AssetEnvelope compatibility
- oversize staging + permit/route workflow
- marine/aircraft/machinery space compatibility
- dynamic curb + temporary-rule model
- parking roaming + fleet/logistics API

## Provider interfaces to define early

- MapRenderer / map configuration
- Geocoder
- RoutingProvider
- ParkingDataProvider
- MunicipalGISProvider
- PublicRegulationProvider
- CurbDataProvider
- OversizePermitProvider
- Marina/PortProvider
- AerodromeGroundSpaceProvider
- PermitAuthorityAdapter
- AuthorityRuleProvider
- PaymentGateway
- MarketplaceGateway
- DATEXIIAdapter
- PermitProvider
- EnforcementProvider
- OccupancySensorProvider
- PaymentGateway
- IdentityVerificationProvider
- MessagingProvider
- ANPRProvider
- AccessControlProvider
- SmartLockProvider
- EV/OCPPProvider
- ObjectStorageProvider
- NotificationProvider

## License gate

The current PARK-IT repository itself is GPLv3.

Before commercial V2 code is substantially mixed into the legacy tree:
1. inventory all historical contributors and copyright ownership
2. inventory copied/vendor code
3. decide intended product licensing model
4. decide whether V2 remains GPL, is dual-licensed where legally possible, or is developed in a cleanly separated codebase/package structure
5. record the owner/legal decision

Do not silently replace the existing license.
