# Agent Operating Contract

**MANDATORY ENTRYPOINT:** read `AGENT_READ_FIRST.md` first in every new agent session. Its reading order and precedence rules are canonical.

Every coding agent, orchestrator and subagent must read and follow `MASTER_AUTONOMOUS_COMPLETION_PROTOCOL.md` before substantial work.

For PARK-IT V2, `docs/PARK_IT_V2_ARCHITECTURE_FREEZE.md` is the canonical product/architecture baseline. `AGENT_AUTONOMOUS_EXECUTION_POLICY_V4.md`, `AGENT_AUTONOMOUS_EXECUTION_POLICY_V5.md`, and `docs/CI_RUNBOOK.md` are mandatory for autonomous CI/continuation behavior. It is mandatory reading before planning or implementation. An agent must not silently deviate from it. A material deviation requires an ADR under `docs/adr/` and explicit owner approval before implementation.

A substantial task is part of the whole-project completion program, not an isolated slice. Maintain an executable dependency-aware master graph, use parallel specialist/subagent audits where useful, continue automatically with the next machine-solvable item after a track closes, and reserve `Task complete` for full machine-exhaustion of the project.

Repository-specific safety, secrets policy, irreversible actions and explicit owner authorization remain stricter than the master protocol.

Default execution pattern:

`AUDIT -> CONSISTENCY GATE -> LICENSE/PROVENANCE GATE -> MASTER PLAN -> PARALLEL AUDITS -> IMPLEMENT -> HOSTILE REVIEW -> FOCUSED/FULL TESTS -> REAL RUNTIME/E2E WHERE SAFE -> EXACT-SHA SERVER CI -> CLOSURE -> RESYNC -> NEXT READY ITEM`

Never fabricate external evidence or readiness. Never expose secrets. Never perform irreversible production actions without explicit owner authorization.

## PARK-IT V2 non-negotiable gates

1. Treat this repository as legacy GPLv3-derived code until source provenance proves otherwise.
2. Before substantial V2 implementation, inventory licensing/provenance and record the chosen GPL-compatible or clean-room V2 path.
3. Preserve the universal shared domain; do not build parallel vertical stacks for car/truck/boat/machinery/aircraft.
4. Modular monolith first; PostgreSQL/PostGIS target; provider adapters at volatile external boundaries.
5. Draft/AI/GIS data is never authoritative by default.
6. Navigation route is not an authority-approved route.
7. Unknown/stale live data stays UNKNOWN/STALE.
8. Physical access fails closed.
9. Production money movement, authority submission and physical hardware activation require explicit gates.
10. Canonical self-hosted/server CI must be exact-SHA GREEN for CI-required closure.

## SWE-2 High acceleration program

During the temporary SWE-2 High availability window, every substantial coding workflow must also read and follow `SWE2_HIGH_ACCELERATION_PROTOCOL.md` and maintain `SWE2_HIGH_EXECUTION_QUEUE.md`. SWE-2 High is reserved for the highest-value difficult implementation packages; orchestration, read-only audit and lightweight work should be delegated appropriately. Repository-specific safety and owner authorization remain stricter.
