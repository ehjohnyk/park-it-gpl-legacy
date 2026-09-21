# PARK-IT Permit Orchestration and Payments

## 1. Why this exists

A mobility-space platform is incomplete if it can say "you need a permit" but leaves the user to discover:
- which authority is responsible,
- which application form/system is needed,
- what attachments are required,
- which route must be approved,
- who must comment on bridges/roads/rail/trolley lines,
- how fees are paid,
- what escort/time restrictions apply,
- whether the approval is still valid on trip day.

PARK-IT should therefore include a **Permit Orchestration Engine**.

The engine is not the legal authority. It coordinates authoritative systems and preserves provenance.

## 2. Existing market evidence

Country-specific digital systems already prove this workflow is viable.

Examples:
- Germany: VEMAGS supports an electronic process for large/heavy transports from application through authority consultations to issuance.
- Netherlands: RDW/DWO supports exceptional-transport route applications and consultation with road managers.
- Slovakia: nadmerná/nadrozmerná doprava is subject to special road-use permission; authority responsibility depends on route/jurisdiction and supporting consents/road-manager/police inputs may apply.

The product opportunity is **cross-jurisdiction orchestration**, not replacing each authority's legal competence.

## 3. PermitCase

Core object:

```text
PermitCase
  id
  applicant
  asset/transport configuration
  origin
  destination
  candidate route(s)
  jurisdictions
  required authorities
  required documents
  required consultations
  fees
  submissions
  conditions
  decisions
  approved route/corridor
  validity
  trip-day checks
  audit trail
```

## 4. Permit discovery

Input:

```text
origin
destination
date/time
vehicle combination
load
dimensions
mass
axle data
cargo type
ADR
purpose
operator/company
```

Engine steps:

1. identify countries/regions/road authorities on candidate corridors;
2. load authority-specific RulePack versions;
3. determine ordinary vs abnormal movement;
4. identify permit products:
   - single trip
   - repeated/multi-trip
   - annual/long-term
   - corridor/network permit
   - special purpose
5. identify related approvals:
   - road manager
   - bridge/structure review
   - rail crossing
   - trolley/overhead line
   - police/escort
   - municipal access
   - port/terminal
   - customs/border
6. calculate missing information/documents;
7. present application plan.

Output example:

```text
Transport: 28.0 m / 4.2 m / 4.5 m / 80 t

Country A:
- abnormal-load permit REQUIRED
- road manager consultation REQUIRED
- escort class: pending authority decision

Country B:
- permit REQUIRED
- bridge engineering review REQUIRED
- night travel window likely

Municipal destination:
- ZTL special-entry permit REQUIRED

Staging yard:
- reservation REQUIRED
```

## 5. Authority RulePack

Do not hard-code one global permit rule table.

Each RulePack is:
- jurisdiction scoped
- authority scoped
- effective-dated
- versioned
- sourced
- reviewed
- capable of being superseded

It can define:
- thresholds
- permit type
- competent authority resolution
- required attachments
- fees/formulas
- processing lead time
- escort rules
- travel windows
- route constraints
- application endpoint/form
- e-signature requirements
- payment method
- result format

Status:
- VERIFIED_AUTHORITATIVE
- OPERATOR_CONFIRMED
- MANUALLY_REVIEWED
- USER_DECLARED
- INFERRED

Only authoritative/approved states may drive "legal approval" claims.

## 6. Authority adapters

Provider interface:

```text
PermitAuthorityAdapter

resolveAuthority()
validateApplication()
submitApplication()
uploadAttachment()
payFee()
getStatus()
getRequestsForInformation()
respondToAuthority()
getDecision()
getPermitDocument()
getApprovedRoute()
cancelApplication()
```

Adapter implementations may be:
- API
- government portal integration where contractually/technically allowed
- e-government connector
- structured email/workflow
- document package generation for manual submission
- operator-assisted process

If an authority does not provide machine integration, PARK-IT can still produce a complete submission package and workflow checklist.

## 7. Workflow state machine

```text
DRAFT
-> DATA_REQUIRED
-> READY_TO_SUBMIT
-> SUBMITTED
-> AUTHORITY_REVIEW
-> CONSULTATIONS
-> INFORMATION_REQUESTED
-> RESPONSE_SUBMITTED
-> FEE_REQUIRED
-> DECISION_PENDING
-> APPROVED | APPROVED_WITH_CONDITIONS | REJECTED
-> TRIP_READY
-> ACTIVE
-> COMPLETED
-> EXPIRED / CANCELLED
```

Every state transition records:
- actor
- timestamp
- authority/provider
- document/version
- reason
- evidence

## 8. Route approval engine

A normal navigation route is not an authorised abnormal-load route.

Use separate route layers:

```text
NAVIGATION_ROUTE
ENGINEERING_CANDIDATE_ROUTE
AUTHORITY_SUBMITTED_ROUTE
AUTHORITY_APPROVED_ROUTE
TRIP_DAY_VALIDATED_ROUTE
```

Route constraints may include:
- road width
- bridge capacity
- tunnel/height
- turning geometry
- axle/structure constraints
- temporary works
- events
- overhead/trolley clearance
- rail crossings
- prohibited times
- escort assembly
- staging
- border/port slots

Do not infer bridge capacity or structural approval from ordinary map data.

## 9. Multi-authority consultation

One permit may require comments from multiple road managers/operators.

PARK-IT should show:

```text
PermitCase #AT-2026-1842

Authority A ........ APPROVED
Municipality B ..... APPROVED WITH CONDITION
Bridge operator C .. ENGINEERING REVIEW
Police ............. ESCORT CONDITION ISSUED
Rail operator ...... APPROVED
Destination ZTL .... APPROVED

Overall:
NOT YET TRIP-READY
```

Trip-ready is achieved only when all mandatory dependencies are satisfied.

## 10. Conditions as machine-readable policy

Approval conditions should be parsed/entered into structured rules:

- permitted dates
- time windows
- maximum speed
- escort type/count
- police coordination
- lane position
- bridge crossing instructions
- convoy spacing
- weather/visibility constraint
- roadwork coordination
- pre-notification
- temporary sign removal/reinstatement
- specific staging stops

The source permit document remains authoritative.

## 11. Trip-day revalidation

Before departure, PARK-IT rechecks:
- permit validity
- route version
- closures
- roadworks
- events
- temporary restrictions
- weather-sensitive authority conditions where applicable
- staging reservation
- escort assignment
- destination access
- facility availability

Result:

```text
TRIP_READY
TRIP_READY_WITH_WARNINGS
BLOCKED_BY_ROUTE_CHANGE
PERMIT_EXPIRED
AUTHORITY_RECONFIRMATION_REQUIRED
```

## 12. Permit service marketplace

Optional later marketplace:
- permit specialists
- route survey companies
- structural engineers
- escort/pilot vehicle providers
- police coordination service where legally applicable
- temporary traffic-management providers
- crane/handling operators
- port/terminal agents

PARK-IT can request quotes and attach the selected provider to PermitCase.

Provider qualifications must be verified where regulated.

## 12A. Identity, signature and trusted delivery

Authority adapters may require:
- national eID/e-government login
- eIDAS-compatible identification
- advanced or qualified electronic signature
- electronic seal
- trusted timestamp
- registered electronic delivery
- official eDesk/mailbox

PARK-IT should implement a TrustServiceAdapter boundary rather than assuming one national identity/signature technology.

Every signed/submitted document preserves:
- signer/seal identity
- signature level
- validation result
- timestamp
- document hash
- authority destination
- delivery receipt

## 12B. Document intelligence

Document AI may:
- OCR/parse uploaded technical documents
- extract vehicle/load dimensions
- extract axle count/spacing/loads
- detect document type/version
- extract permit validity/conditions
- compare values across documents
- prefill application fields
- detect contradictions/missing attachments
- translate non-authoritative helper text for the user

Controls:
- source page/file reference for every extracted field
- confidence
- human verification state
- no fabricated missing value
- authoritative original retained
- redaction/privacy policy


## 13. Payments architecture

Create a **Payment Orchestrator**, not a single-provider checkout.

Initial consumer-facing methods should support, subject to PSP/market availability:
- Visa / Mastercard / major cards
- Apple Pay
- Google Pay
- PayPal
- SEPA/bank payment
- local bank redirect/open-banking methods
- municipal credits/wallet where legally appropriate
- B2B invoice/monthly billing
- fleet payment integrations

For Central/EU rollout, Apple Pay and Google Pay are essential wallet methods; current PSP platforms such as Adyen and PayPal/Braintree expose them in Europe, including Slovakia depending on merchant/integration eligibility.

## 14. PaymentGateway abstraction

```text
PaymentGateway
  createAuthorisation()
  adjustAuthorisation()
  capture()
  cancel()
  refund()
  partialRefund()
  createMandate()
  getStatus()
  reconcile()
```

Marketplace extension:

```text
MarketplaceGateway
  onboardPayee()
  verifyPayee()
  splitPayment()
  transfer()
  payout()
  reverseTransfer()
```

The domain must not store provider-specific objects as business truth.

## 15. Payment use cases

### Parking
- immediate payment
- extend session
- capped tariff
- resident/BEV/permit discount

### Marketplace garage/workshop
- preauthorise
- capture at booking/use
- platform commission
- host payout
- deposit/hold
- damage/dispute workflow

### EV charging
- parking fee
- energy fee
- idle fee
- combined settlement

### Truck/special asset
- reservation
- staging
- security/service add-ons
- operator approval fee

### Permit
- government/authority administrative fee
- engineering/route-study fee
- escort/service-provider payment
- platform service fee

Government fees should be clearly distinguished from PARK-IT fees.

## 16. Payment method priorities

Recommended launch order:

1. cards
2. Apple Pay
3. Google Pay
4. PayPal
5. SEPA / bank-transfer/open-banking options
6. B2B monthly invoice
7. local payment methods by country
8. fleet-card/payment-network integrations

Do not build a regulated stored-value wallet unless legal/payment-regulatory review approves the model.

## 17. Marketplace split settlement

For marketplace bookings support:
- gross amount
- tax/VAT metadata
- host/operator share
- platform commission
- payment fee
- refund liability
- chargeback liability
- payout status

Use PSP marketplace functionality for regulated onboarding/KYC and payout where possible rather than PARK-IT custodying client funds itself.

## 18. Deposits and holds

Use payment-provider preauthorisation/hold functionality where supported.

Do not call a simple payment hold "escrow" unless the legal/payment arrangement genuinely qualifies.

## 18A. Toll / vignette / road-charge orchestration

Payment and route planning should also support regulated road charging.

Potential categories:
- motorway vignette
- distance-based HGV toll
- bridge/tunnel/ferry infrastructure charge
- congestion charge
- ZTL/LEZ access fee
- special-route fee
- permit administration fee

Provider interfaces:
```text
RoadChargeProvider
  quote(route, asset)
  identifyRequiredProducts()
  purchaseOrLink()
  verifyCoverage()
  reconcile()
```

European Electronic Toll Service (EETS) should be treated as an interoperability/integration target for electronically tolled road networks.

PARK-IT must not duplicate a toll or vignette purchase when a fleet vehicle is already covered by its EETS/fleet provider.

## 18B. Freight information interoperability

Track the EU eFTI framework as a future integration point for electronic freight information exchanged with authorities.

The PermitCase/Document model should be able to reference certified external freight-information platforms/documents without copying authority status incorrectly.


## 19. Billing / receipts / invoices

Support:
- consumer receipt
- VAT invoice metadata
- municipality receipt
- host invoice
- B2B consolidated monthly invoice
- fleet cost centre
- permit/admin-fee receipt
- multi-currency records
- export to accounting

## 20. Reconciliation ledger

PARK-IT keeps its own immutable financial ledger references:
- booking
- public parking session
- permit case
- charge
- capture
- refund
- payout
- authority fee
- provider fee
- commission
- chargeback

Provider reports/webhooks reconcile into this ledger.

## 21. Security/compliance

- PSD2/SCA where applicable
- tokenised payment credentials
- do not store raw card data
- idempotent webhook handling
- signed webhook verification
- double-spend/replay protection
- payout/KYC gating
- AML/regulatory responsibilities delegated to licensed PSP where applicable
- PCI scope minimisation
- clear distinction between authority fee and platform/service fee

## 22. One-click mobility checkout

Long-term UX:

```text
Destination/use case selected
-> vehicle/asset automatically evaluated
-> permit/tariff/space price calculated
-> best compliant option selected
-> Apple Pay / Google Pay / preferred method
-> booking + permit fee + access in one flow
```

The user sees one coherent checkout even when PARK-IT internally orchestrates multiple providers/authorities.

## 23. Golden path: abnormal transport

```text
User enters:
Bratislava -> Rotterdam
Transformer
28 m / 4.2 m / 4.5 m / 80 t

PARK-IT:
1. creates transport configuration
2. computes candidate corridors
3. identifies every jurisdiction/authority
4. lists permits + documents + expected fees
5. generates application packages
6. submits through available connectors
7. tracks authority consultations
8. receives route/conditions
9. resolves staging/truck/escort services
10. collects required payments
11. builds AUTHORITY_APPROVED_ROUTE
12. revalidates before departure
13. provides trip package/audit trail
```

This is the target experience. It is not a claim that every authority currently exposes an API.
