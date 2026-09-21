# PARK-IT Municipal / Public Parking / UVAR Platform

## Purpose

PARK-IT must support not only private marketplace inventory but also **authoritative public parking and urban vehicle access regulation**.

A municipality, district or parking operator should be able to define, publish, price, monitor and enforce public parking and access rules in the same platform drivers use for private spaces, garages and workshops.

The target is one driver experience:

```text
ROUTE -> CHECK VEHICLE ELIGIBILITY -> WARN / REROUTE -> ENTER ZONE
      -> PARK -> PAY / USE PERMIT -> MONITOR SESSION -> LEAVE
```

and one municipal control plane:

```text
MAP/GIS -> ZONES -> RULES -> TARIFFS -> PERMITS -> ENFORCEMENT
        -> OCCUPANCY -> ANALYTICS -> OPEN DATA / DATEX II / APDS
```

## 1. Municipal tenancy

A municipality can operate one or more administrative areas.

Hierarchy example:

```text
Municipality
  -> District
    -> Parking Zone
      -> Sector
        -> Curb Segment / Street Section
          -> Individual Space (optional)
```

The model must support:
- polygon zones
- line/curb segments
- point restrictions
- entry/exit gates
- directionality
- overlapping regulations with deterministic priority
- effective-from/effective-to dates
- scheduled/dynamic rules
- versioned publication

Every authoritative rule should retain:
- issuing authority
- legal/source reference
- version
- publication status
- effective period
- geometry version
- operator who approved/published it

## 2. Public parking policy

A city can configure:
- paid / unpaid zones
- tariff bands
- per-minute, hourly, daily or capped pricing
- maximum stay
- free first N minutes
- grace periods
- night/weekend rules
- event tariffs
- occupancy-sensitive tariffs where legally allowed
- resident-only periods
- loading/unloading windows
- disabled parking
- EV-specific rules
- motorcycle rules
- bus/truck rules
- P+R facilities
- reserved municipal/service spaces
- temporary closures
- construction/event restrictions

The same zone may have different policy by time and vehicle/user class.

## 3. Digital permits and parking cards

Support permit classes such as:
- resident
- visitor
- bonus/time-credit
- business
- employee
- municipal service
- emergency
- disabled
- EV/low-emission
- delivery
- taxi
- hotel/guest
- temporary event
- contractor
- school/service access

A permit may be bound to:
- person
- household
- company
- vehicle plate
- fleet
- zone
- time window
- credit balance
- usage quota

Permit eligibility and issuance must be configurable per authority and fully auditable.

## 4. ZTL / LEZ / ZEZ / UVAR policy engine

PARK-IT should have a general Urban Vehicle Access Regulation engine rather than one hard-coded "low-emission zone" feature.

Supported policy dimensions should include:
- vehicle category/class
- passenger/commercial use
- fuel/propulsion type
- Euro emission class
- zero-emission status
- CO2/emission attribute where authoritative data exists
- gross weight
- axle/weight class
- vehicle height/width/length
- trailer
- hazardous-goods classification
- country/registration context
- resident status
- disability entitlement
- emergency/public-service entitlement
- delivery purpose/permit
- permit/sticker/registration
- day/time/season
- event state
- congestion state
- air-quality trigger
- payment/toll state

Decision outputs:

```text
ALLOW
DENY
ALLOW_WITH_FEE
REQUIRE_PERMIT
REQUIRE_REGISTRATION
REQUIRE_PAYMENT
EXEMPT
WARNING
UNKNOWN / CANNOT_VERIFY
```

Every decision must provide a machine-readable reason and a driver-facing explanation.

Example:

```text
ZONE: Historic Centre ZTL
Vehicle: diesel M1, Euro 4
Time: Monday 09:15

Decision: DENY
Reason: EURO_CLASS_BELOW_MINIMUM
Rule: ZTL-2027-04 v3
Alternative: P+R North, 1.8 km
```

## 5. Route-aware regulation

Routing should evaluate restrictions before directing a driver into a regulated zone.

Examples:
- vehicle cannot enter -> route around zone
- entry requires fee -> show fee before route starts
- permit required -> offer registration/permit workflow
- destination lies inside restricted zone -> route to compliant public/private parking at boundary
- temporary restriction active -> reroute immediately
- foreign vehicle classification uncertain -> warn and request required data

This turns PARK-IT into a compliance-aware navigation layer, not only a parking map.

## 6. Assisted / automatic zoning

Municipal setup should not require drawing every street manually.

Create a **Zone Builder** that can generate draft zoning from permitted data sources:

Inputs may include:
- municipal GIS
- GeoJSON / Shapefile / GPKG
- WFS/WMS or other authorised GIS services
- OpenStreetMap road graph and attributes
- cadastral/parcel data where licensing permits
- existing parking-space inventory
- traffic-sign datasets
- entry/exit points
- existing zone codes
- parking meter locations
- sensor/ANPR telemetry
- imagery/map-provider APIs only where their terms permit the intended derivation

Workflow:

```text
IMPORT DATA
 -> NORMALISE ROAD / CURB / POLYGON GEOMETRY
 -> DETECT CANDIDATE ZONE BOUNDARIES
 -> PROPOSE CURB SEGMENTS / SPACES
 -> PROPOSE RULE/TARIFF ASSIGNMENTS
 -> CONFLICT + GAP DETECTION
 -> HUMAN GIS REVIEW
 -> LEGAL/POLICY APPROVAL
 -> VERSIONED PUBLISH
```

AI can assist with:
- detecting missing gaps between zone polygons
- matching street names/codes
- suggesting curb segmentation
- identifying inconsistent tariffs
- detecting overlapping/conflicting rules
- reading structured source documents into draft policy
- proposing changes when the road network changes

**AI-generated geometry or policy is never authoritative by itself.** A city/operator must approve a published regulation.

Google Maps or another commercial map can be a visual/reference/provider input only within applicable licensing terms. PARK-IT should prefer provider-neutral municipal GIS + OpenStreetMap/open data for reusable zoning logic.

## 7. Map editor

Municipal operators need a map-first editor:

- draw polygon
- split/merge zone
- draw curb segment
- snap to road/curb geometry
- assign tariff/rule
- bulk edit streets
- import GIS layers
- validate topology
- preview driver view
- compare old/new version
- schedule future activation
- rollback to previous version

Map edits must have audit/version history.

## 8. Public parking payment

Driver flow:
1. locate automatically or select zone
2. select vehicle
3. system evaluates permit/discount
4. show tariff and maximum stay
5. select duration
6. pay or consume credit
7. create digital parking session
8. allow extension if policy permits
9. notify before expiry
10. close/expire session

Payment methods should be provider-neutral and may include:
- card
- Apple Pay / Google Pay through payment provider
- municipal wallet/credit
- resident/visitor credit
- third-party mobility provider
- external parking/payment integration

## 9. Occupancy and availability

Sources can include:
- in-ground/NB-IoT sensors
- camera occupancy
- ANPR entry/exit counting
- gate counters
- payment/session inference
- operator feeds
- manual status
- forecast model

The system must distinguish:

```text
MEASURED
INFERRED
PREDICTED
STALE
UNKNOWN
```

A predicted free space must never be presented as sensor-confirmed.

## 10. Enforcement

Municipal enforcement can integrate:
- handheld enforcement app
- scan car / mobile ANPR
- fixed ANPR cameras
- virtual gates
- occupancy sensors
- parking-session database
- permits/exemptions
- zone/rule engine

Pipeline:

```text
OBSERVATION
 -> NORMALISE PLATE / LOCATION / TIME
 -> FIND ACTIVE RULE VERSION
 -> FIND PAYMENT / PERMIT / EXEMPTION
 -> POLICY EVALUATION
 -> COMPLIANT | SUSPECTED_VIOLATION | UNKNOWN
 -> EVIDENCE PACKAGE
 -> MUNICIPAL BACKOFFICE REVIEW
```

PARK-IT should generate **violation candidates**, not silently convert uncertain machine observations into legal penalties. Final enforcement workflow depends on jurisdiction and authority.

## 11. Municipal backoffice

Required views:
- live occupancy map
- zone editor
- tariff/rule editor
- permit administration
- parking sessions
- payments/reconciliation
- enforcement queue
- ANPR/sensor health
- device inventory
- disputes/appeals integration
- revenue
- occupancy
- turnover
- average stay
- violation trends
- resident/visitor credit usage
- access-regulation statistics
- open-data exports

## 12. Digital twin and simulation

Before publishing a new policy, the city should be able to simulate it.

Examples:
- change tariff from 1.00 to 1.50 EUR/h
- reduce maximum stay
- create resident-only sector
- prohibit Euro 4 diesel vehicles
- activate event ZTL
- convert street to loading zone
- add P+R alternative

Simulation can estimate, subject to data quality:
- affected vehicles
- historical session impact
- occupancy impact
- revenue impact
- likely diversion
- capacity pressure on neighbouring zones

Simulation is decision support, not a guarantee.

## 13. Driver "Can I go there?" mode

A vehicle profile can contain verified or user-declared attributes needed for access decisions.

A driver entering a destination should be able to ask:

> Can my car legally enter and park there at 10:00 tomorrow?

PARK-IT returns:
- entry eligibility
- restrictions
- required permit/registration
- expected toll/parking price
- compliant route
- compliant parking alternatives

The system should support foreign vehicles and clearly distinguish verified from self-declared vehicle attributes.

## 14. Interoperability

Municipal/public integrations should be designed around European/open standards where practical:

- **DATEX II Parking**
- **DATEX II UVAR / machine-interpretable traffic regulation profiles as they mature**
- **APDS**
- national access point (NAP) feeds where applicable
- OpenStreetMap/OpenData for non-authoritative base geometry
- provider-neutral payment, ANPR and sensor APIs

Internal PARK-IT domain models should remain richer than any single external standard; standards belong at import/export boundaries.

## 15. Relationship to marketplace inventory

Public and private supply coexist in one search result but keep different governance.

```text
PUBLIC / MUNICIPAL
  Authority owns regulation and tariff
  PARK-IT executes/integrates published policy

PRIVATE / MARKETPLACE
  Host owns listing/rules within platform constraints
  PARK-IT handles booking/payment/access

COMMERCIAL OPERATOR
  External operator can provide tariff, availability, booking and access APIs
```

Driver ranking can compare all eligible options:
- municipal on-street
- municipal garage
- P+R
- commercial garage
- private driveway
- private garage
- EV bay

## 16. Non-negotiable controls

- authoritative zones/rules are versioned
- draft and published state are separate
- effective dates are explicit
- every access/parking decision can explain which rule version applied
- map/provider data licensing is respected
- automatic zoning requires approval before publication
- enforcement evidence has retention/access rules
- uncertain plate/vehicle classification cannot become an automatic legal conclusion
- provider outage must not silently produce "allowed" or "paid" state
