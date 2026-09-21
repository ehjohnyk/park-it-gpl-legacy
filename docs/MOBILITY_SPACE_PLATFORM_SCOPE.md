# PARK-IT Complete Mobility-Space Management Scope

## Product definition

PARK-IT is not only a parking application.

It is a **Mobility-Space Management Platform** that connects:

- physical space
- vehicles and other mobile assets
- public regulation
- route/access eligibility
- permits and approvals
- booking
- payment
- physical access
- occupancy
- enforcement
- operators
- cities
- fleets
- service providers
- analytics/digital twin

Canonical thesis:

> **Any mobility asset + any destination/use case -> determine where it may legally go, where it can physically fit, what approval/payment is required, reserve the correct space, grant access, and keep the whole decision auditable.**

## Core product pillars

### 1. Public parking and municipal mobility
- paid/unpaid zones
- tariffs
- maximum stay
- resident/visitor/business permits
- ŤZP/accessible parking entitlements
- BEV/PHEV/low-emission benefits
- ZTL/LEZ/ZEZ/UVAR
- temporary/event rules
- congestion/access fees
- P+R
- occupancy
- enforcement
- municipal backoffice

### 2. Private mobility-space marketplace
- driveway
- private parking
- garage
- long-term storage
- detailing space
- DIY/workshop bay
- EV charging
- equipment
- optional service provider

### 3. Freight and heavy vehicle
- vans
- buses/coaches
- HGV
- articulated combinations
- trailers
- truck parking
- safe/secure parking
- ADR/reefer requirements
- services/security
- heavy-vehicle routing

### 4. Special assets
- abnormal/oversize transports
- indivisible cargo
- boats/yachts
- marine berths
- dry storage
- construction/agricultural machines
- mobile cranes
- industrial machinery
- private/GA aircraft ground spaces
- hangars/tie-downs/operator-authorised stands

### 5. Dynamic curb management
- parking
- loading
- pickup/dropoff
- taxi
- resident
- bus
- disabled/accessibility
- no-stopping
- event/emergency/snow/cleaning mode
- time-dependent curb use
- CDS interoperability

### 6. Regulatory and permit orchestration
- determine whether permit is required
- identify competent authorities
- identify road/asset operators
- collect required documents
- prepare applications
- route application to authority systems
- track comments/conditions
- calculate/pay administrative fees where supported
- collect approval
- build approved route/corridor
- revalidate before trip
- preserve permit provenance

### 7. Smart physical access
- ANPR
- QR
- temporary PIN
- NFC/BLE
- wallet credential
- smart gate
- smart lock
- PARK-IT Access Box
- provider adapters
- revocation/expiry/audit

### 8. Payments / settlement / roaming
- cards
- Apple Pay
- Google Pay
- PayPal
- SEPA/bank payment
- local methods by market
- B2B invoicing
- municipal wallet/credits
- split marketplace payments
- host/operator payouts
- deposits/preauthorisations
- refunds
- chargebacks
- recurring/subscription
- roaming between cities/operators

### 9. Fleet / logistics platform
- fleet vehicles/assets
- bulk access checks
- delivery/loading windows
- curb reservation
- permits
- truck/oversize staging
- route constraints
- booking
- API
- organisational billing

### 10. Digital twin / analytics / policy simulation
- occupancy
- demand
- turnover
- revenue
- violations
- tariff impact
- zone impact
- rerouting pressure
- P+R impact
- truck capacity
- curb usage
- planned temporary rules

### 11. Open platform / integrations
- APDS
- DATEX II
- OMF CDS
- OCPP/OCPI
- map/routing providers
- municipal GIS
- national access points
- permit-authority connectors
- payment providers
- ANPR/access systems
- fleet/telematics providers

## Universal domain

The platform should converge on a small set of universal abstractions:

```text
SUBJECT
  person / organisation / fleet / operator / authority

ASSET
  car / van / truck / trailer / boat / aircraft / machine / cargo

ASSET_ENVELOPE
  dimensions / mass / propulsion / operational constraints

SPACE
  parking bay / curb / garage / yard / berth / hangar / stand / workshop

CAPABILITY
  charging / lift / water / hardstanding / security / berth / crane / etc.

REGULATION
  public-authority rule

ENTITLEMENT
  permit / exemption / discount / access right

AVAILABILITY
  calendar / occupancy / sensor / provider state

BOOKING
  reservation/use contract

PERMIT_CASE
  regulatory application/approval workflow

ACCESS
  physical/digital credential

PAYMENT
  charge/fee/deposit/refund/payout

EVIDENCE
  events/documents/photos/operator decisions
```

## Critical rule

PARK-IT must always distinguish four separate questions:

1. **Is the asset physically compatible with the space?**
2. **Is the route physically compatible with the asset?**
3. **Is the movement/parking legally authorised?**
4. **Is the space actually available/reservable?**

A single YES must never imply the other three.

## Decision model

A search candidate can therefore be:

- COMPATIBLE_AND_ALLOWED
- COMPATIBLE_REQUIRES_PAYMENT
- COMPATIBLE_REQUIRES_PERMIT
- COMPATIBLE_REQUIRES_OPERATOR_APPROVAL
- COMPATIBLE_ROUTE_UNKNOWN
- INCOMPATIBLE_SPACE
- INCOMPATIBLE_ROUTE
- LEGALLY_PROHIBITED
- AVAILABILITY_UNKNOWN
- STALE_DATA

## UX principle

The user should not need to understand which authority or system sits behind the result.

Examples:

> "Can I drive my Euro 5 diesel to this hotel tomorrow?"

> "Find parking for my 16.5 m truck within 45 minutes with a shower."

> "I need to move a 4.2 m wide transformer from A to B next Thursday."

> "Find winter dry storage for my 9 m boat."

> "Book a secure yard for a 24 t excavator with low-loader access."

PARK-IT translates intent into structured policy/space/permit/payment workflows.

## Scope freeze rule

After the product areas in this document and linked architecture documents are represented in the executable master plan, V2 scope should be frozen.

New ideas should enter a post-V2 backlog unless they:
- close a P0/P1 product gap,
- are necessary for legal/security correctness,
- are required by an already-selected integration.

The implementation priority remains:
1. correct domain
2. public/private parking
3. booking/payment/access
4. municipal/UVAR
5. freight/special assets
6. permit orchestration
7. advanced integrations/analytics
