# PARK-IT V2 Architecture & Product Freeze

Status: **CANONICAL / FROZEN BASELINE**
Applies to: all human contributors, Devin, coding agents, subagents and generated implementation plans.
Change policy: deviations require an explicit Architecture Decision Record (ADR) with migration impact, security/privacy impact, licensing impact and owner approval before implementation.

## 1. Product identity

PARK-IT V2 is not a single-purpose parking application. It is a **Mobility-Space Management Platform**.

The platform must model the complete chain:

```text
INTENT
-> ACTOR / TENANT
-> VEHICLE OR MOBILE ASSET
-> LEGAL + PHYSICAL ELIGIBILITY
-> ROUTE / JURISDICTION
-> PERMITS / APPROVALS
-> SPACE DISCOVERY
-> QUOTE / BOOKING
-> PAYMENT / BILLING
-> ACCESS
-> OCCUPANCY / SESSION
-> ENFORCEMENT / EVIDENCE
-> SETTLEMENT
-> ANALYTICS
```

No future feature may create a parallel product model for cars, trucks, boats, machinery or authorised aircraft. Vertical differences are represented through typed configuration, capabilities, envelopes, rules and provider adapters over common primitives.

## 2. Frozen core domain

The following primitives are canonical:

- Tenant / Organisation / Authority / Operator
- User / Driver / Host / EnforcementActor
- Vehicle
- Asset
- VehicleCombination / AssetEnvelope
- Space
- Zone / CurbSegment / Facility
- Capability / Equipment
- RegulationVersion / Rule / Entitlement
- Availability / Occupancy
- Quote
- Booking / Reservation
- PermitCase / AuthorityRulePack
- RouteCandidate / ApprovedRoute provenance
- Payment / Billing / Settlement
- AccessCredential / AccessEvent
- Evidence / AuditEvent

A vertical may extend these objects but may not bypass them with an independent booking, payment, access, policy or identity stack.

## 3. Frozen architecture

- **Modular monolith first.** Do not split into microservices without measured scaling, isolation or deployment evidence.
- **PostgreSQL + PostGIS** is the target system of record for V2 domain and geospatial state.
- Provider-specific SDK objects never leak into the domain model.
- External systems are isolated behind explicit adapters/contracts.
- Critical writes are idempotent.
- External callbacks/webhooks are untrusted and replay-safe.
- State transitions are explicit and auditable.
- Effective-dated/versioned policy is mandatory for public regulations, tariffs, permits and entitlements.
- Draft regulatory data never affects authoritative driver or enforcement decisions.
- Human/authority publication is required for legally effective rules.
- Physical access defaults fail-closed.
- Unknown/stale availability must remain UNKNOWN/STALE; it must never be presented as live truth.
- Navigation routes and authority-approved routes are separate provenance classes.
- AI/document extraction/GIS generation may propose; it may not silently create authoritative legal facts.

## 4. Product-layer order

Implementation dependencies are frozen in this order:

1. P0 repository recovery, installability, license/security/dependency baseline.
2. P1 shared identity/tenant/domain foundation.
3. P2 universal Space + Vehicle/Asset + Availability + Quote/Booking foundation.
4. P3 geospatial/search/routing adapter layer.
5. P4 municipal/public parking + UVAR/curb + policy engine.
6. P5 marketplace/operator flow.
7. P6 booking/session/concurrency hardening.
8. P7 payment/billing/settlement adapters.
9. P8 access/ANPR/device contracts.
10. P9 permit orchestration + approved-route provenance.
11. P10 evidence/dispute/trust.
12. P11 customer/operator mobile/PWA golden paths.
13. P12 staging/production/release seal.

Special verticals such as HGV, abnormal transport, marina, machinery and authorised aircraft must reuse the shared foundation rather than create parallel stacks.

## 5. Commercial entry wedge

The architecture must support a narrow sellable deployment before full platform completion.

First commercial-capable golden path:

```text
operator creates/publishes a space
-> defines eligibility, schedule, price and access rules
-> customer searches for compatible space
-> system gives truthful availability + quote
-> customer reserves
-> sandbox/payment provider confirms
-> time-bounded access entitlement is issued
-> check-in / occupancy / check-out is recorded
-> settlement/audit evidence is produced
```

This golden path is the minimum product slice. Broader verticals cannot block it.

## 6. Licensing / clean-room gate

The current repository is a fork of a GPL-3.0 project. Therefore:

- legacy code is treated as GPL-covered unless proven otherwise by file-level provenance;
- do not assume PARK-IT V2 can become a closed-source proprietary product by merely rewriting some files inside the same derivative work;
- P0 must inventory source provenance, copied assets, generated assets, dependencies and license obligations;
- before substantial V2 implementation, create a documented decision for either:
  1. **GPL-compatible product path**, or
  2. **clean-room V2 implementation boundary** in a fresh codebase/repository with no copying of GPL implementation code, while preserving only independently specified behaviour and product requirements where legally permissible;
- until that decision is recorded, new product-domain implementation must remain clearly separable from legacy code and may not be represented as commercially license-clean.

This is an engineering governance gate, not legal advice. Final commercial licensing requires qualified legal review.

## 7. External-truth gates

The platform must never fabricate or overstate external truth.

Fail closed or mark UNKNOWN when required evidence is unavailable for:
- real parking occupancy/availability;
- authority permits/approvals;
- bridge/height/weight/route restrictions;
- payment settlement;
- ANPR/access hardware state;
- municipal regulation publication;
- identity/e-signature/e-seal state;
- enforcement/penalty authority.

Sandbox, fixture and simulator data must be visibly classified as such.

## 8. Security / privacy invariants

- Secrets never enter source control or evidence logs.
- Plate/VIN/location/identity data have explicit retention and access rules.
- Tenant isolation is mandatory.
- Public/admin/operator roles are least-privilege.
- Access credentials are scoped, revocable and time-bounded.
- Permanent shared gate PINs are prohibited.
- Audit events are append-oriented and tamper-evident where appropriate.
- Production money movement, physical actuation and authority submission require explicit environment/owner gates.

## 9. CI / completion invariant

The repository's configured self-hosted/server CI is canonical.

A CI-required package is not COMPLETE unless:
- implementation is real;
- focused and full feasible local tests pass;
- real runtime/E2E evidence exists where applicable;
- exact-SHA server CI is GREEN;
- evidence and state ledgers are updated.

GitHub-hosted Actions must not be used merely to duplicate canonical server CI or consume hosted credits.

## 10. Scope-freeze rule

Agents may discover new requirements, but they must classify them as:
- REQUIRED FOR CURRENT FOUNDATION,
- FOLLOW-ON,
- EXTERNAL/HUMAN GATE,
- REJECTED AS DUPLICATIVE/PARALLEL ARCHITECTURE.

No agent may silently:
- replace the universal SPACE model with a vertical-specific one;
- introduce a second identity, booking, payment or policy engine;
- hard-code a single map/payment/access provider into the domain;
- bypass permit/route provenance;
- turn draft AI/GIS output into authoritative regulation;
- claim production readiness without external evidence.

## 11. Change procedure

Any proposed change to this freeze must create an ADR under `docs/adr/` containing:
- problem/evidence;
- current frozen rule affected;
- proposed decision;
- alternatives;
- migration cost;
- security/privacy impact;
- licensing impact;
- compatibility impact;
- acceptance tests;
- rollback plan;
- owner approval state.

Until approved, the frozen baseline remains authoritative.
