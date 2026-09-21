# PARK-IT

**PARK-IT is a complete Mobility-Space Management Platform for people, fleets, cities, operators, vehicles and special mobile assets.**

The original project started as a web parking assistant that finds nearby parking and navigates the driver to an available space. The target product is now broader: one platform for discovering, booking, paying for and accessing physical spaces around mobility **and** for cities/operators to define public parking zones, permits, tariffs and vehicle-access regulations.

A PARK-IT listing can represent a normal parking space, private driveway, garage, long-term vehicle storage, EV charging space, detailing/DIY workshop bay, truck park, heavy-haul staging yard, boat berth/dry storage, machinery yard or authorised aircraft stand/hangar.

The product goal is simple:

> **Driver:** I have a vehicle and need a place or need to know whether I may enter a zone. PARK-IT finds a compliant option, explains the rule, handles parking/booking/payment and grants access when applicable.

> **City/operator:** I need to define zones, tariffs, permits, restrictions, occupancy and enforcement. PARK-IT turns them into versioned machine-readable policy and one driver experience.

## Complete Mobility-Space Management Platform

PARK-IT joins the full lifecycle that is usually fragmented across maps, city parking apps, permit portals, fleet systems, access-control products and marketplace apps:

```text
INTENT
 -> ASSET/VEHICLE PROFILE
 -> LEGAL/PHYSICAL ELIGIBILITY
 -> ROUTE
 -> PERMITS/APPROVALS
 -> SPACE DISCOVERY
 -> BOOKING
 -> PAYMENT
 -> PHYSICAL ACCESS
 -> OCCUPANCY/USE
 -> ENFORCEMENT/EVIDENCE
 -> SETTLEMENT
 -> ANALYTICS
```

The canonical complete scope is documented in [docs/MOBILITY_SPACE_PLATFORM_SCOPE.md](docs/MOBILITY_SPACE_PLATFORM_SCOPE.md).


## Product modes

### PARK
Find and reserve parking near a destination.

Examples:
- public parking
- commercial parking
- private driveway
- private parking bay
- hotel / office / company parking
- event parking
- monthly parking
- motorcycle / trailer / caravan parking
- van / bus / truck / articulated-combination parking

### CITY / MUNICIPAL
Operate public parking and urban vehicle access in the same driver platform.

Examples:
- paid parking zones
- resident / visitor / business permits
- ŤZP/disabled-person entitlements and reserved spaces
- BEV/PHEV/low-emission tariff benefits where the authority chooses
- delivery/supply, taxi, hotel, service and temporary special permits
- digital parking cards and time credits
- on-street/off-street municipal parking
- P+R
- tariff schedules and maximum stay
- ZTL / limited traffic zones
- low-emission / zero-emission zones
- pedestrian/residential access zones
- congestion/access fees
- loading/delivery windows
- vehicle restrictions by Euro emission class, fuel, category, length, width, height, weight, axle/combination class or trailer
- temporary/event restrictions
- ANPR enforcement, scan cars and fixed cameras
- occupancy sensors and live availability
- municipal backoffice, analytics and open-data feeds

### SPECIAL / ASSET
Find compatible storage, staging or authorised parking for assets beyond ordinary cars.

Examples:
- abnormal/oversize transport staging
- heavy-haul permit waiting areas
- secure laydown yards
- boats/yachts and boat trailers
- marina berths and dry storage
- work/construction/agricultural machinery
- mobile cranes
- industrial equipment
- private/GA aircraft hangars and authorised stands
- helicopter/tie-down spaces

### GARAGE
Rent private automotive space for a period of time.

Examples:
- secure garage
- covered parking
- seasonal vehicle storage
- classic-car storage
- detailing / cleaning space
- short-term garage rental
- EV charging garage

### WORKSHOP
Book a space where permitted automotive work can be performed.

Examples:
- DIY maintenance bay
- tyre-change bay
- detailing bay
- lift-equipped workshop
- workspace with tools
- compressor / water / electricity access
- mobile mechanic or detailer workspace

The host explicitly declares allowed and prohibited activities for every space.

## Universal SPACE model

The core domain object is not just a parking spot. It is a **SPACE** with capabilities, rules, availability, access methods and optional equipment/services.

Example:

```text
SPACE #9281

Type: GARAGE
Parking .............. YES
Long-term storage .... YES
Detailing ............ YES
Car washing .......... YES
DIY repair ........... YES
Oil change ........... NO
Welding .............. NO
Painting ............. NO

Water ................ YES
230 V ................ YES
400 V ................ NO
Drain ................. YES
Heating ............... YES
Ventilation ........... YES
Compressor ............ YES
Vacuum ................ YES
Lift .................. NO

Max vehicle:
Length 5.4 m
Width  2.2 m
Height 2.1 m
Weight 3,000 kg
```

Capabilities must be machine-readable so search, ranking, policy, pricing and access automation can work consistently.

## Public parking, zoning and UVAR

PARK-IT must support authoritative municipal/public rules alongside marketplace listings.

A public zone can define:
- geometry: polygon, curb/road segment, entry point or individual spaces
- tariff and charging schedule
- maximum stay and grace rules
- resident/visitor/business permits
- time credits and exemptions
- allowed/prohibited vehicle classes
- Euro emission class / propulsion restrictions
- vehicle weight/dimension restrictions
- ZTL/LEZ/ZEZ/congestion rules
- loading/delivery periods
- temporary/event restrictions
- enforcement method
- occupancy/availability sources

The policy engine should answer not only **"where can I park?"** but also **"may this specific vehicle legally enter and park there at this time, and what must it pay or obtain?"**

Example:

```text
Destination: Historic Centre
Vehicle: M1 diesel, Euro 4
Time: Monday 09:15

Entry: DENIED by ZTL-2027-04 v3
Reason: Euro class below minimum
Alternative: P+R North
Parking: 2.00 EUR + public transport connection
```

### Entitlements, discounts and special permits

Municipal pricing and access must be rule-driven rather than hard-coded.

A city can define an **entitlement** for a person, vehicle, organisation or permit class and attach benefits/conditions such as:
- free parking
- percentage or fixed-price discount
- lower tariff band
- free first N minutes/hours
- higher/lower daily cap
- longer maximum stay
- access to otherwise restricted zones
- use of reserved spaces
- allowed entry only during configured time windows
- limited monthly/day credits
- exemption from a specific fee or restriction

Typical configurable classes include:
- BEV
- PHEV
- other low/zero-emission classes
- persons with disabilities / ŤZP entitlement
- resident
- visitor
- business/operator
- delivery/supply vehicles
- taxi
- emergency/public service
- municipal fleet
- maintenance/contractor
- hotel/guest
- medical/home-care service
- school/service access
- temporary/event permit

Example:

```text
Zone A base tariff ........ 2.00 EUR/h
BEV ........................ 50% discount
PHEV ....................... 25% discount
ŤZP permit ................. free + reserved-space eligibility
Resident permit ............ 0.50 EUR/h, max 24 h
Supply permit .............. entry 06:00-10:00, 60 min parking
Emergency/service .......... exempt
```

These are examples of **configurable policy**, not assumed legal entitlements. Each municipality publishes its own authoritative rules, eligibility evidence, scope and effective dates.


### Assisted zone builder

Cities should be able to import municipal GIS/open data and have PARK-IT generate a **draft** zoning model from road/curb geometry, existing parking inventory, signs, zone codes and other licensed map data.

Sources can include:
- GeoJSON / Shapefile / GPKG
- WFS/WMS or municipal GIS
- OpenStreetMap/open data
- cadastral or road datasets where licensed
- existing parking inventory
- traffic-sign datasets
- sensor/ANPR telemetry
- commercial map APIs only within their terms

AI may propose polygons, curb segments, tariff assignments and detect gaps/conflicts. A human municipal operator must approve/version/publish legally effective zones and restrictions.

### Municipal enforcement

PARK-IT can integrate:
- handheld enforcement
- scan car/mobile ANPR
- fixed ANPR/virtual gates
- sensors
- active parking sessions
- permits and exemptions

Machine observations create a compliance result or **suspected violation evidence package** for the competent authority; legal enforcement remains jurisdiction-specific.

See [docs/MUNICIPAL_PUBLIC_PARKING_UVAR.md](docs/MUNICIPAL_PUBLIC_PARKING_UVAR.md).


## Heavy vehicle / freight parking

PARK-IT must treat vans, buses, trucks and articulated combinations as first-class vehicles, not oversized passenger cars.

A city, road operator or private truck-stop operator can publish restrictions such as:
- no vans/commercial vehicles above a configured length
- no vehicles above a configured maximum permissible mass
- no HGV parking in residential zones
- no trailers or articulated combinations
- height/width/axle-load restrictions
- bus-only / truck-only / delivery-only areas
- daytime loading allowed but overnight parking prohibited
- time-window access for commercial vehicles
- ADR/dangerous-goods restrictions
- refrigerated-vehicle restrictions or designated areas

The driver experience must answer both:
1. **Where am I legally allowed to stop/park this exact vehicle combination?**
2. **Where is there actually suitable capacity?**

Example:

```text
Vehicle:
tractor + semi-trailer
combination length: 16.5 m
max permissible mass: 40 t
height: 4.0 m
ADR: NO

Destination area:
City centre

Result:
Municipal Zone A ........ DENIED (vehicles > 3.5 t)
Industrial Zone HGV .... ALLOWED, 8 spaces
Secure Truck Park East . ALLOWED, 23 spaces, bookable
P+R ..................... DENIED (max length 6.0 m)
```

### Truck parking facilities

A truck-parking facility can describe:
- HGV capacity and live occupancy
- reservable/non-reservable spaces
- maximum vehicle/combination dimensions
- weight/axle constraints
- trailer parking
- bus/coach eligibility
- ADR eligibility
- refrigerated-vehicle support and reefer power
- security/certification level
- gated/ANPR access
- toilets/showers
- food/drink
- Wi-Fi
- fuel
- workshop/repair
- laundry
- EV/HGV charging
- overnight stay
- pricing and cancellation rules

PARK-IT should support European safe-and-secure truck-parking data and DATEX II exchange at provider boundaries.

For professional drivers, a future route planner can take an externally supplied/driver-entered rest deadline or remaining-driving-time constraint and find a compliant reachable parking option. PARK-IT must not claim certified tachograph/legal-hours compliance unless that module is separately validated for the applicable law.


## Special assets, oversize transport and non-car parking

PARK-IT should separate **asset compatibility** from **route/legal access compatibility**.

A space may physically fit an asset but still be unreachable without a permit, escort, authorised route or operator approval.

Core model:

```text
ASSET
 + ASSET ENVELOPE
 + TRANSPORT CONFIGURATION
 + SPACE/FACILITY CAPABILITIES
 + REGULATORY REQUIREMENTS
 + BOOKING / ACCESS / PAYMENT
```

Supported use cases include:
- abnormal-load and heavy-haul staging
- oversized indivisible cargo
- boats/yachts, trailers, marina berths and dry storage
- excavators, cranes, agricultural/construction machinery
- secure equipment yards
- private/GA aircraft hangars and operator-authorised apron/stand parking

Examples:

```text
28 m abnormal transport / 80 t
-> compatible staging yard
-> gate/turning/ground-load check
-> route/permit status
-> escort/time-window requirements

9.2 m boat / 3.1 m beam
-> berth or dry storage
-> draught/beam/hoist compatibility

24 t excavator
-> hardstanding yard
-> low-loader access
-> spill/security requirements

Cessna 172
-> hangar/stand compatibility
-> aerodrome/operator approval required
```

For aviation, abnormal transport and other regulated/high-risk cases, PARK-IT may assist with compatibility and workflow but cannot self-authorise permits, airside access or regulated movements.

See [docs/SPECIAL_ASSET_OVERSIZE_AND_CURB.md](docs/SPECIAL_ASSET_OVERSIZE_AND_CURB.md).


## Host marketplace

Any eligible owner or operator can publish unused automotive space.

A host can configure:
- location and exact access instructions
- type of space
- dimensions and vehicle limits
- photos
- opening calendar
- minimum / maximum booking duration
- instant booking or manual approval
- hourly / daily / monthly pricing
- deposits where applicable
- equipment and amenities
- EV charging
- permitted activities
- prohibited activities
- custom house rules
- check-in / check-out requirements
- accepted access methods
- cancellation policy
- overstay policy
- security requirements

Potential hosts:
- private individuals
- apartment buildings
- offices
- hotels
- dealerships
- repair shops
- detailing businesses
- shopping centres
- logistics sites
- municipalities
- parking operators

## Booking lifecycle

Target state:

```text
DISCOVER
  -> QUOTE
  -> RESERVE
  -> PAYMENT AUTHORISATION
  -> ACCESS CREDENTIAL ISSUED
  -> CHECK-IN
  -> ACTIVE USAGE
  -> CHECK-OUT
  -> SETTLEMENT
  -> REVIEW / DISPUTE WINDOW
```

The system must be idempotent and auditable. No booking should be considered active merely because a payment UI succeeded.

## Smart Access Engine

A confirmed booking can create a temporary physical-access entitlement.

Example:

```text
Booking: ABC123
Vehicle: NR-123XY
Space: Garage 4
Access zone: Gate A + Garage 4
Valid: 2026-09-22 13:45 -> 18:15
```

Supported access adapters should include:

- ANPR / license-plate allowlist
- NFC mobile credential
- BLE mobile credential
- QR credential
- temporary PIN
- in-app remote unlock
- smart lock
- smart gate / barrier
- relay controller
- host remote approval
- physical key / lockbox fallback

Credentials must be time-bounded, revocable and scoped to the minimum resource necessary.

### License plate / ANPR flow

```text
Reservation confirmed
  -> selected vehicle/plate bound to booking
  -> temporary plate entitlement created
  -> vehicle arrives
  -> ANPR provider reads plate
  -> Access Engine verifies entitlement
  -> gate opens
  -> event appended to audit trail
  -> entitlement expires after booking + configured grace period
```

ANPR must be implemented behind a provider interface. PARK-IT must not hard-wire one recognition vendor or one camera brand.

### PARK-IT Access Box

A future hardware product may connect existing gates/garage doors to PARK-IT.

Potential capabilities:
- ESP32-class controller
- Ethernet / Wi-Fi / optional LTE
- secure relay outputs
- door / gate sensors
- encrypted local credential cache
- MQTT / HTTPS connectivity
- optional NFC reader
- optional keypad
- optional ANPR camera integration
- offline-safe policy for already-issued credentials
- signed firmware updates
- tamper / health telemetry

The cloud must never require a permanently open inbound port to the device.

## Live space state

A space can expose operational state independently from booking state.

Examples:
- AVAILABLE
- RESERVED
- OCCUPIED
- ACCESS_OFFLINE
- GATE_OPEN
- GATE_CLOSED
- CHARGER_AVAILABLE
- CHARGING
- OUT_OF_SERVICE

Freshness must be explicit. Stale telemetry may not be presented as live truth.

## Digital identity, documents and e-government

Permit and authority workflows should support:
- eID/e-government identity adapters
- electronic signature / qualified electronic signature where required
- electronic seals for organisations
- trusted timestamp
- registered electronic delivery
- authority document inbox/outbox
- multilingual forms/documents

Document AI can assist by extracting structured data from:
- vehicle registration/technical documents
- axle-load tables
- cargo drawings
- insurance certificates
- ADR documents
- route surveys
- permits
- invoices/receipts

Extracted values are marked with provenance/confidence and require verification before they become authoritative inputs. Missing values are never invented.


## Permit Orchestrator

PARK-IT should not stop at "special permit required".

For regulated movements such as abnormal/oversize transport it should build a `PermitCase`:

```text
transport/load data
-> candidate routes
-> jurisdictions
-> competent authorities
-> required applications/documents
-> consultations/engineering/escort requirements
-> administrative fees
-> submission/status tracking
-> authority conditions
-> approved route/corridor
-> trip-day revalidation
```

Where an authority offers an API/portal integration, PARK-IT can submit and track electronically. Where no machine integration exists, it can generate the correct application package and guide/coordinate the manual submission.

A normal navigation route is never treated as an approved abnormal-load route.

Country-specific systems already demonstrate parts of this model; PARK-IT's differentiation is provider/jurisdiction orchestration above them.

See [docs/PERMIT_ORCHESTRATION_AND_PAYMENTS.md](docs/PERMIT_ORCHESTRATION_AND_PAYMENTS.md).


## Dynamic curb, temporary rules, roaming and fleet API

PARK-IT should also treat street/curb space as a time-dependent resource.

Example:

```text
CURB SEGMENT #821
06:00-10:00 DELIVERY
10:00-17:00 PAID PARKING
17:00-19:00 RESIDENT ONLY
19:00-02:00 TAXI / PICKUP
EVENT MODE  NO STOPPING
```

Authorities can publish temporary rules for cleaning, snow, roadworks, markets, sport/events, emergency closures or abnormal-load passage. Rules are effective-dated, versioned, automatically expire and may trigger rerouting/booking notifications.

A future roaming layer lets one PARK-IT account use multiple cities/operators through provider adapters while preserving each authority's own rules and permit validity.

A B2B Fleet/Logistics API should expose access decisions, loading/curb rules, compatible parking/staging, permits, reservations and reason codes for delivery fleets, buses, heavy haul, construction and service fleets.


## Search and ranking

PARK-IT should search by user intent, not only by address.

Examples:
- "Parking near the hospital tomorrow from 08:00 to 15:00."
- "Secure parking within 10 minutes walking distance, max 15 EUR."
- "Garage in Nitra on Saturday with water and 230 V for detailing."
- "Workshop bay with a lift for two hours."
- "Monthly dry storage for a classic car."
- "Parking with EV charging overnight."
- "Find secure parking for a 16.5 m / 40 t truck within 45 minutes, with shower and bookable space."

Ranking inputs may include:
- availability
- estimated arrival time
- driving distance
- walking distance to destination
- price
- expected total cost
- confidence/freshness of availability
- vehicle dimensions
- capability match
- access method
- EV compatibility
- host rating
- security features
- cancellation rules

AI may translate natural-language intent into filters, but deterministic policy/ranking rules must remain inspectable and testable.

## Toll, vignette and road-charge orchestration

Route cost/compliance should also include:
- motorway/vignette products
- truck tolls
- congestion/access charges
- tunnel/bridge/ferry charges where integrated
- EETS-compatible road charging interfaces where applicable
- authority/permit fees

PARK-IT should show which costs are mandatory, which are optional services and which are already covered by a fleet/EETS account.

The route engine should be able to optimise for:
- fastest
- cheapest legal
- lowest toll
- lowest emission/access cost
- permit-compatible
- HGV/oversize-compatible
- preferred operator/fleet policy


## Payments

PARK-IT uses a provider-neutral Payment Orchestrator.

Target payment methods, subject to country/PSP availability:
- Visa / Mastercard / major cards
- Apple Pay
- Google Pay
- PayPal
- SEPA / bank transfer / open-banking options
- local payment methods
- B2B monthly invoicing
- municipal credits
- fleet payment integrations

Payment flows include:
- public parking sessions
- booking preauthorisation/capture
- marketplace split payments
- host/operator payouts
- deposits/holds
- refunds/partial refunds
- EV charging
- permit/authority fees
- service-provider fees
- subscriptions/recurring billing
- reconciliation

Government/authority fees, operator/host revenue and PARK-IT commission must stay separately identifiable.

PARK-IT should use licensed PSP marketplace/platform capabilities for KYC/payouts and avoid custodying client funds itself where possible.


## Payments and payouts

Marketplace settlement should support:
- buyer payment
- platform fee
- host payout
- refunds
- partial refunds
- deposits / holds where supported
- disputes
- cancellation fees
- overstay charges
- EV energy charges
- equipment/service add-ons
- tax/VAT metadata

Payment processing must be behind a provider abstraction. Stripe Connect is a strong initial reference for connected-account onboarding and marketplace payouts, but core booking state may not depend on Stripe-specific semantics.

## EV charging

A SPACE may expose one or more EV chargers.

Target support:
- OCPP adapter
- charger availability
- reservation binding
- remote start/stop where authorised
- energy/session telemetry
- tariff calculation
- RFID / account credential mapping
- combined parking + energy settlement

OCPP/OCPI integrations should be isolated behind adapters.

## Equipment and services

A booking can optionally include equipment:

- lift
- jack
- tyre machine
- wheel balancer
- vacuum
- extractor
- compressor
- diagnostic tool
- pressure washer
- charger
- tools

A future SERVICE marketplace can attach labour to a space booking.

Example:
- space only
- space + equipment
- space + mobile detailer
- space + mobile mechanic

This creates one transaction graph:

```text
SPACE -> EQUIPMENT -> SERVICE -> BOOKING -> PAYMENT -> ACCESS -> REPUTATION
```

## Rules and responsibility model

The platform needs two policy layers.

### Platform rules
Non-optional minimum safety, legal, privacy and prohibited-use rules.

### Host rules
Additional terms defined by the host for the specific property.

Examples:
- no grinder after 20:00
- no oil changes
- maximum two people
- no smoking
- clean floor after use
- photo check-out required
- 100 EUR deposit
- engine may not run indoors
- no painting / welding

A host cannot use custom rules to override platform-level prohibitions.

Exact liability allocation, insurance, consumer-law obligations and host/guest contractual wording are legal-design gates and must be reviewed for each launch jurisdiction.

## Evidence, trust and disputes

Higher-risk bookings should support an evidence chain:
- verified identity state
- verified vehicle / plate
- booking timestamps
- accepted rules/version
- payment state
- credential issuance/revocation
- gate/open events
- check-in photos
- check-out photos
- optional sensor events
- optional ANPR events
- incident report
- dispute timeline

Example event stream:

```text
13:57 VEHICLE_ARRIVED       NR123XY
13:57 ACCESS_GRANTED        ANPR / Gate A
13:57 GATE_OPENED
13:59 GATE_CLOSED
17:42 ACCESS_GRANTED        ANPR / Gate A
17:43 VEHICLE_EXITED
17:44 BOOKING_COMPLETED
17:45 ACCESS_REVOKED
```

Privacy-sensitive evidence must have explicit purpose, access control and retention.

## Privacy and security

PARK-IT can process location, identity, vehicle plates, access logs, payment metadata and potentially camera-derived events. The architecture must therefore use privacy-by-design:

- collect minimum necessary data
- configurable retention
- encrypt secrets and sensitive data
- tenant/resource-level authorization
- immutable security/audit events where justified
- signed and expiring credentials
- replay protection
- idempotent webhooks
- rate limits
- device authentication
- no default public camera streams
- explicit access to evidence
- provider isolation
- fail closed for physical access decisions unless an explicitly defined offline entitlement permits access

GDPR/DPA implementation is a release gate, not an afterthought.

## Architecture direction

Recommended target architecture:

```text
Web / PWA / Mobile
      |
API Gateway / BFF
      |
+---------------------------------------------------+
| Identity & Trust                                  |
| Space / Listing                                   |
| Availability / Calendar                           |
| Search / Geo / Ranking                            |
| Booking / Pricing                                 |
| Payments / Payouts                                |
| Access Policy / Credential Engine                 |
| Device / Gate / ANPR Adapters                     |
| EV / OCPP Adapter                                 |
| Evidence / Disputes                               |
| Reviews / Reputation                              |
| Notifications                                     |
| Admin / Risk / Operations                         |
+---------------------------------------------------+
      |
PostgreSQL + PostGIS / Queue / Object Storage
      |
External provider adapters
```

Start as a modular monolith unless operational evidence justifies service separation.

## Open standards / reusable foundations

Prefer standards and permissively licensed components over proprietary lock-in.

Strong candidates:
- **APDS** for parking-data interoperability
- **DATEX II Parking + UVAR** for European public parking/access-regulation interoperability
- **Open Mobility Foundation CDS** for curb/loading/parking regulation interoperability
- **OpenStreetMap** data
- **MapLibre GL JS** for map rendering
- **Valhalla or OSRM** for routing
- **PostgreSQL + PostGIS** for geospatial persistence
- **Stripe Connect** patterns for marketplace settlement
- **OCPP / OCPI** for charging interoperability
- provider-neutral ANPR adapter
- provider-neutral access-control adapter
- MQTT/HTTPS device transport for PARK-IT Access Box

See [docs/OPEN_SOURCE_REUSE_AUDIT.md](docs/OPEN_SOURCE_REUSE_AUDIT.md).

## Legacy application

The repository currently contains a legacy Node.js parking-assistance prototype based on Google Maps/Places and MongoDB-era assumptions.

Legacy behavior includes:
- geolocation
- nearby parking discovery
- nearest parking navigation
- parking availability polling
- map markers and place details

This legacy application is historical evidence and a migration source, not the target architecture.

Modernisation must preserve useful behaviour while replacing brittle/deprecated boundaries incrementally.

## Delivery program

The current execution program is defined in:
- [MASTER_AUTONOMOUS_COMPLETION_PROTOCOL.md](MASTER_AUTONOMOUS_COMPLETION_PROTOCOL.md)
- [SWE2_HIGH_ACCELERATION_PROTOCOL.md](SWE2_HIGH_ACCELERATION_PROTOCOL.md)
- [SWE2_HIGH_EXECUTION_QUEUE.md](SWE2_HIGH_EXECUTION_QUEUE.md)
- [docs/PARK_IT_PRODUCT_ARCHITECTURE.md](docs/PARK_IT_PRODUCT_ARCHITECTURE.md)
- [docs/MUNICIPAL_PUBLIC_PARKING_UVAR.md](docs/MUNICIPAL_PUBLIC_PARKING_UVAR.md)
- [docs/SPECIAL_ASSET_OVERSIZE_AND_CURB.md](docs/SPECIAL_ASSET_OVERSIZE_AND_CURB.md)
- [docs/MOBILITY_SPACE_PLATFORM_SCOPE.md](docs/MOBILITY_SPACE_PLATFORM_SCOPE.md)
- [docs/PERMIT_ORCHESTRATION_AND_PAYMENTS.md](docs/PERMIT_ORCHESTRATION_AND_PAYMENTS.md)
- [docs/OPEN_SOURCE_REUSE_AUDIT.md](docs/OPEN_SOURCE_REUSE_AUDIT.md)
- [docs/DEVIN_EXECUTION_BRIEF.md](docs/DEVIN_EXECUTION_BRIEF.md)

## Current status

The V2 marketplace/access architecture documented above is a **target state**, not a claim that these features are implemented today.

The next implementation step is a whole-repository audit and a dependency-aware executable master plan before code migration begins.

## License

The repository currently contains a GPLv3 license inherited from the existing project.

**Important:** the long-term commercial licensing model must be resolved before substantial reuse or commercial productisation. Do not silently replace or relicense third-party or contributed code. The modernisation plan must inventory copyright ownership and dependency licenses before a licensing decision is made.
