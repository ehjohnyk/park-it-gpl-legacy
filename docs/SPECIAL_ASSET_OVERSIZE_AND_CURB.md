# PARK-IT Special Asset, Oversize Transport & Non-Car Parking

## Purpose

PARK-IT must support assets that do not fit the normal passenger-car model.

The platform should distinguish:

1. **the thing being stored/parked** — vehicle, trailer, boat, aircraft, machine, cargo;
2. **the transport configuration used to move it** — towing vehicle, low-loader, abnormal-load convoy, aircraft tow, boat trailer;
3. **the place where it can stay** — yard, berth, hangar, apron stand, dry storage, staging area, depot;
4. **the route/access permissions needed to reach that place**.

Core abstraction:

```text
ASSET
  + ASSET ENVELOPE
  + TRANSPORT CONFIGURATION
  + SPACE/FACILITY CAPABILITIES
  + REGULATORY REQUIREMENTS
  + ACCESS / BOOKING / PAYMENT
```

PARK-IT may assist with regulatory checks, but a legal permit or authoritative clearance is valid only when issued/confirmed by the responsible authority/operator.

## 1. Asset classes

Initial asset classes:

- passenger vehicle
- van / light commercial vehicle
- bus / coach
- rigid truck
- tractor + semi-trailer
- trailer / caravan
- abnormal-load transport
- indivisible oversized cargo
- boat / yacht / personal watercraft
- boat trailer
- aircraft
- helicopter
- glider
- drone/UAS storage where relevant
- agricultural machine
- construction machine
- mobile crane
- excavator / loader / bulldozer
- forklift / industrial machine
- road-maintenance machine
- forestry machine
- special-purpose vehicle
- container / swap body / machinery module

The domain must be extensible; do not encode product logic as one enum-only switch.

## 2. Universal AssetEnvelope

An AssetEnvelope describes physical and operational compatibility.

Common fields:
- length
- width
- height
- mass / maximum permissible mass where relevant
- centre/clearance metadata where available
- wheel/axle configuration
- trailer/combination dimensions
- turning or manoeuvring requirement
- dangerous-goods/ADR class where applicable
- propulsion/fuel/battery characteristics
- indoor/outdoor eligibility
- leak/environmental risk classification
- tie-down/chocking requirement
- charging/power requirement
- security level
- operator-required classification

Specialised attributes can extend the envelope.

## 3. Oversize / abnormal road transport

An abnormal transport may exceed ordinary legal road dimensions or weights.

PARK-IT should model:
- tractor/towing vehicle
- trailer/low-loader
- indivisible load
- total combination envelope
- axle configuration
- total/maximum mass
- overhang
- escort/pilot requirement state
- permit reference
- permit authority
- authorised route/corridor
- allowed travel dates/times
- speed restrictions if supplied authoritatively
- bridge/tunnel/road constraints
- staging/holding areas
- required entry gate
- route validity/version

### Two-step decision

```text
FACILITY COMPATIBILITY
Can this space physically and operationally accept the transport?

ROUTE/PERMIT COMPATIBILITY
Can this exact transport legally and physically reach it under an active permit?
```

A compatible parking/staging yard must not be shown as "reachable" if route/permit requirements are unknown.

### Oversize parking / staging spaces

Support:
- heavy-haul staging yards
- permit waiting areas
- escort assembly points
- overnight abnormal-load parking
- port/industrial staging
- crane/machinery laydown yards
- border/customs staging
- secure high-value cargo yards

Facility attributes:
- gate width/height
- internal turning radius
- surface/pavement load rating
- space dimensions
- ground-bearing capability
- crane/forklift loading support
- escort access
- security
- lighting
- CCTV/ANPR
- power
- rest facilities
- hazardous-goods suitability
- operator approval workflow

## 4. Boats, yachts and marine storage

PARK-IT should support both **water berth** and **land/dry storage**.

### Marine asset profile

Potential fields:
- vessel length overall (LOA)
- beam
- draught
- air draught / mast height
- displacement/weight
- propulsion/fuel
- trailer dimensions/weight
- shore-power requirements
- fresh/salt-water suitability
- launch/recovery method
- crane/hoist requirement
- covered/indoor requirement

### Space/facility types

- marina berth
- visitor berth
- mooring
- dry stack
- boat yard
- winter storage
- trailer parking
- slipway/ramp
- crane/hoist slot
- service yard
- covered hall

Compatibility examples:

```text
Boat 9.2 m LOA / 3.1 m beam / 1.7 m draught
Berth A: compatible
Berth B: denied, max beam 2.8 m
Dry Hall C: compatible, crane booking required
```

The operator/port/marina remains authoritative for navigation, berth assignment, local safety and environmental rules.

## 5. Aircraft / aerodrome ground parking

Aircraft parking is not ordinary public parking. PARK-IT should operate only as an aerodrome/operator integration or authorised private-airfield/hangar marketplace workflow.

Asset profile may include:
- aircraft registration
- type/model
- wingspan
- length
- tail height
- MTOW/weight class
- rotor diameter for helicopters
- fuel/energy type
- towing requirements
- tie-down/chocking requirements
- hangar compatibility
- ground-power requirement
- security/access class

Space types:
- apron stand
- remote stand
- general-aviation stand
- helicopter stand
- hangar bay
- private hangar
- tie-down area
- maintenance bay

Compatibility should evaluate:
- stand/hangar dimensions
- door clearance
- wing/tail/rotor clearance
- pavement/stand limitations where authoritative data exists
- access/tow path
- operator approval
- security zone
- ground-handling requirements
- temporary closures/restrictions

EASA/ICAO aerodrome procedures and the aerodrome operator remain authoritative. PARK-IT must not self-authorise airside access or aircraft stand use.

## 6. Work machines and industrial equipment

Examples:
- excavator
- wheel loader
- bulldozer
- mobile crane
- paver
- roller
- agricultural tractor/harvester
- forestry machine
- telehandler
- road-maintenance machine

Storage needs may include:
- secure yard
- hardstanding
- high ground-bearing capacity
- low-loader access
- oversized gate
- wash area
- fuel restrictions
- battery/EV-machine charging
- 230/400 V
- hydraulic/oil spill containment
- covered storage
- maintenance bay
- overnight security

The system should distinguish:
- machine can legally travel on-road itself;
- machine must be transported;
- machine needs a special permit or escort;
- storage is compatible but route is not yet validated.

## 7. Curb and dynamic street-space management

PARK-IT must model the curb/roadside as a time-dependent resource.

Example:

```text
CURB SEGMENT #821

06:00-10:00  DELIVERY
10:00-17:00  PAID PARKING
17:00-19:00  RESIDENT ONLY
19:00-02:00  TAXI / PICKUP-DROPOFF
EVENT MODE   NO STOPPING
```

A curb rule can specify:
- user/vehicle classes
- asset dimensions/mass
- purpose: parking/loading/passenger pickup/service
- time/day/season
- tariff
- maximum stay
- permit
- reservation
- temporary closure
- event/emergency/snow mode

Evaluate Open Mobility Foundation CDS as an interoperability boundary.

## 8. Temporary and event rules

Authorities/operators need future and emergency overrides:
- street cleaning
- snow removal
- roadworks
- markets/fairs
- concerts/sports
- filming
- moving/relocation
- construction crane operations
- abnormal-load passage
- emergency/security closures
- temporary bus/taxi/loading zones

Temporary rules must:
- have explicit start/end
- cite authority/source
- override according to deterministic precedence
- be previewable before activation
- expire automatically
- retain audit/version history
- notify affected bookings/permits where possible

## 9. Parking roaming / one vehicle-account experience

PARK-IT should support provider-neutral roaming across cities/operators.

A user keeps:
- verified vehicles/assets
- plate/registration
- dimensions/classification
- permits/entitlements
- payment method
- organisational/fleet membership

At a new city/operator:
1. identify applicable zone/operator;
2. evaluate local rules and recognised entitlements;
3. obtain authoritative price;
4. create local parking/access session through adapter;
5. reconcile payment/session status.

Do not assume a permit valid in one authority is valid in another.

## 10. Fleet / logistics API

Provide B2B APIs for fleets and logistics operators.

Inputs may include:
- fleet vehicles/assets
- stops/destinations
- time windows
- cargo/asset envelopes
- permit references
- desired services/security
- parking/rest/staging needs

Outputs:
- legal access decision
- required permits/registrations
- loading/curb availability
- compatible parking/staging candidates
- reservations
- route restrictions
- truck/oversize/machinery constraints
- audit/reason codes

Potential users:
- parcel delivery
- field service
- municipal fleets
- bus/coach operators
- heavy haul
- construction
- agriculture
- equipment rental
- marinas
- airports/private airfields

## 11. Safety / regulation boundary

PARK-IT must distinguish:
- `VERIFIED_AUTHORITATIVE`
- `OPERATOR_CONFIRMED`
- `USER_DECLARED`
- `INFERRED`
- `UNKNOWN`

A regulatory or compatibility decision should preserve source/provenance.

For abnormal transport, aviation and other high-risk use cases:
- no inferred route/permit may be labelled legally approved;
- no aircraft stand is activated without aerodrome/operator authority;
- no dangerous-goods compatibility is assumed;
- no bridge/pavement/ground capacity is invented;
- unknown critical data fails closed or requires operator approval.

## 12. Search examples

```text
"Secure overnight staging for 28 m abnormal transport, 80 t, gate at least 6 m."
"Dry storage for 9 m boat with 3 m beam and trailer."
"Private hangar for Cessna 172 for three nights, operator approval required."
"Secure yard for 24 t excavator with low-loader access."
"Parking for mobile crane, 3.2 m wide, 4.1 m high."
```

Search must explain why candidates are compatible or rejected.
