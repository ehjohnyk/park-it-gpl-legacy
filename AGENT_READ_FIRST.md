# PARK-IT — AGENT READ FIRST

Status: CANONICAL SESSION ENTRYPOINT

Every new coding-agent session MUST start here before planning, editing code, opening a PR, triggering canonical CI, merging, or declaring completion.

## Mandatory reading order

1. `AGENT_READ_FIRST.md`
2. `AGENTS.md`
3. `docs/PARK_IT_V2_ARCHITECTURE_FREEZE.md`
4. `AGENT_AUTONOMOUS_EXECUTION_CONTRACT.md`
5. `MASTER_AUTONOMOUS_COMPLETION_PROTOCOL.md`
6. `AGENT_AUTONOMOUS_EXECUTION_POLICY_V4.md`
7. `AGENT_AUTONOMOUS_EXECUTION_POLICY_V5.md`
8. `MASTER_PRODUCT_CURRENT_STATE.md`
9. `MASTER_PRODUCT_COMPLETION_DAG.md`
10. `MASTER_PRODUCT_EXECUTION_LEDGER.md`
11. `MASTER_PRODUCT_EXTERNAL_BLOCKERS.md`
12. `MASTER_PRODUCT_EVIDENCE_INDEX.md`
13. `docs/CI_RUNBOOK.md`
14. `SWE2_HIGH_ACCELERATION_PROTOCOL.md` and `SWE2_HIGH_EXECUTION_QUEUE.md` when applicable
15. task-specific product/architecture documents under `docs/`

Precedence when documents disagree:

`OWNER INSTRUCTION -> SAFETY/LEGAL/SECURITY -> ARCHITECTURE FREEZE -> EXECUTION CONTRACT -> MASTER COMPLETION PROTOCOL -> V4/V5 AUTONOMY POLICY -> LIVE STATE/LEDGERS -> QUEUE/ROADMAP -> STALE PROSE`

## Mandatory startup gate

Before substantial work establish and record:

- `REPO_CONSISTENCY_GATE`;
- remote/default branch and exact canonical SHA;
- current branch/HEAD/upstream;
- open PRs and exact head SHAs;
- working tree/stashes/worktrees when available;
- current exact-SHA CI state and CI observation path;
- license/provenance state;
- valid P0/P1/P2 findings;
- external/owner blockers;
- next dependency-ready machine-solvable node.

For PARK-IT the first substantive program is P0 recovery + licensing/provenance + CI baseline. Do not begin broad V2 implementation before that gate is reconciled.

## Non-negotiable product facts

PARK-IT V2 is a Mobility-Space Management Platform, not a collection of unrelated vertical applications.

Reuse one shared foundation for identity/tenant/authority/operator, vehicle/asset/envelope, space/zone/facility/curb, regulations/entitlements/tariffs, availability/occupancy, quote/booking/session, permits/route provenance, payment/billing/settlement, access/ANPR/device adapters, and evidence/audit.

Do not create parallel identity, policy, booking, payment, access or evidence stacks for passenger-car, HGV, abnormal transport, marina, machinery or authorised-aircraft verticals.

## Truth rules

Never fabricate live availability, authority approval, legal route validity, payment settlement, physical access state, production provider integration, enforcement authority or CI success. Fixtures/sandboxes/simulators must be labelled.

## Licensing gate

This repository is a GPL-3.0 fork. Treat legacy implementation as GPL-covered unless file-level provenance proves otherwise. P0 must record a commercial path:

1. GPL-compatible product; or
2. clean-room V2 implementation boundary in a fresh codebase/repository with no copying of GPL implementation.

No agent may call V2 commercially license-clean before this is resolved.

## Autonomous CI rule

Canonical self-hosted/server CI is the primary remote gate.

- Establish `CI_OBSERVABILITY_GATE = PASS` before triggering it.
- Freeze the exact candidate SHA.
- Poll CI to terminal state.
- RED means inspect logs, fix root cause, validate locally and rerun minimum necessary scope.
- Do not blind-rerun.
- Do not use paid GitHub-hosted runners merely to duplicate validation.
- While CI is PENDING/RUNNING, prepare the next dependency-ready slice in a separate branch/worktree and continue polling.
- Never mutate the candidate under CI.
- `WAITING_FOR_CI` is not a terminal status while independent machine-solvable work remains.
- A CI-required slice is COMPLETE only with exact-SHA GREEN evidence.

## Stop condition

Do not stop after one PR, one merge or one green run. Continue through the dependency graph until all machine-solvable P0/P1/P2 work in the assigned program is exhausted or a genuine external blocker prevents all remaining relevant work.
