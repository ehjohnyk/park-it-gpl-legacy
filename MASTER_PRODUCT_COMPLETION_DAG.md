# PARK-IT MASTER PRODUCT COMPLETION DAG

Status: CANONICAL EXECUTION GRAPH

The graph is dependency-aware. Agents must recompute readiness after each integrated slice.

## Program nodes

| ID | Node | Depends on | Initial state |
|---|---|---|---|
| P0 | Recovery + License/Dependency/CI Baseline | none | READY |
| P1 | Shared Tenant/Identity/Domain Foundation | P0 | BLOCKED_BY_DEP |
| P2 | Universal Space/Asset/Availability/Booking Core | P1 | BLOCKED_BY_DEP |
| P3 | Geo/Search/Routing Adapter Layer | P1,P2 | BLOCKED_BY_DEP |
| P4 | Municipal Parking/UVAR/Curb Policy Engine | P1,P2,P3 | BLOCKED_BY_DEP |
| P5 | Marketplace/Operator Flow | P1,P2 | BLOCKED_BY_DEP |
| P6 | Booking/Session/Concurrency Hardening | P2,P5 | BLOCKED_BY_DEP |
| P7 | Payment/Billing/Settlement | P5,P6 | BLOCKED_BY_DEP |
| P8 | Smart Access/ANPR/Device Contracts | P2,P6 | BLOCKED_BY_DEP |
| P9 | Permit Orchestration + Approved Route Provenance | P3,P4 | BLOCKED_BY_DEP |
| P10 | Evidence/Disputes/Trust | P4,P6,P7,P8,P9 | BLOCKED_BY_DEP |
| P11 | Customer + Operator PWA Golden Paths | P3,P4,P5,P6,P7,P8,P9 | BLOCKED_BY_DEP |
| P12 | Staging/Production/Release Seal | P0-P11 | BLOCKED_BY_DEP |

## P0 mandatory outputs

P0 is not only documentation. It must establish:
- clean/reproducible install/start/test reality;
- dependency/security inventory;
- tracked generated/dependency artifact inventory;
- legacy behavior characterization;
- secrets/config audit;
- Mongo/Google/API assumptions;
- file-level source/license/provenance inventory;
- GPL-compatible vs clean-room V2 commercial path decision record;
- canonical self-hosted/server CI path;
- `CI_OBSERVABILITY_GATE`;
- exact-SHA baseline evidence;
- executable detailed plan under `docs/agent/`.

## First sellable golden path

The DAG must preserve early delivery of:

`OPERATOR CREATES SPACE -> RULES/PRICE/SCHEDULE -> CUSTOMER SEARCH -> COMPATIBILITY -> TRUTHFUL AVAILABILITY -> QUOTE -> RESERVE -> SANDBOX PAYMENT -> TIME-BOUNDED ACCESS -> CHECK-IN/OCCUPANCY -> CHECK-OUT -> SETTLEMENT/AUDIT`

Broader verticals cannot block this path.

## Vertical rule

HGV, abnormal transport, marina, machinery and authorised-aircraft support extend shared primitives. They never create parallel identity/booking/payment/policy/access stacks.

## Completion rule

A node is COMPLETE only when its acceptance criteria, required tests, runtime/E2E, exact-SHA canonical CI and evidence are complete. Merge is a checkpoint; continue to next ready node.
