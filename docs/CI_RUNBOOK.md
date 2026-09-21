# PARK-IT CI RUNBOOK

Status: CANONICAL CI OPERATIONS POLICY

## Principle

CI is a final verification gate, not the primary debugger.

Use cheap/deterministic local checks first. Push only plausible final candidates. Close CI-required work only against terminal GREEN evidence for the exact candidate SHA.

Canonical compute is the owner's configured self-hosted/server CI. GitHub Actions, if used, is only an orchestration/status plane for approved self-hosted compute unless an explicit repository decision says otherwise.

## Layer A — local gates

Before remote CI run all feasible:
- formatting/lint/static checks;
- unit tests;
- focused integration tests;
- database/migration checks;
- API/provider contract tests with fixtures/sandboxes;
- security/license/dependency checks;
- build/start smoke;
- browser E2E when locally available;
- `git diff --check`;
- final changed-file/secrets audit.

Do not push to discover obvious failures remotely.

## Layer B — self-hosted/server CI

P0 must establish the real repository command/profile and record it here.

Canonical server CI must:
- use clean checkout;
- pin exact 40-char candidate SHA;
- verify `git rev-parse HEAD` equals requested SHA;
- fail fast;
- install from repo truth;
- run stable repository-owned gates;
- record runner identity;
- expose terminal status/logs/artifacts to the agent.

No CI credential belongs in source control.

## CI observability

Before triggering:
`CI_OBSERVABILITY_GATE = PASS`

Agent must be able to trigger (when authorized), poll status, inspect jobs and retrieve failure logs.

If trigger works but observation does not:
`CI_TRIGGERABLE_BUT_NOT_OBSERVABLE`
is an infrastructure defect to repair, not successful handoff.

## Exact-SHA rule

Every canonical run records:
- repository;
- PR/branch;
- exact SHA;
- runner/environment;
- selector/profile;
- run/job ID;
- result;
- evidence/log location.

Runtime-affecting change after GREEN invalidates old closure evidence unless repository policy explicitly proves immutable equivalence.

## Cost discipline

- no blind reruns;
- inspect first failure;
- root-cause locally where feasible;
- batch related fixes;
- rerun minimum required scope;
- no duplicate runs for unchanged SHA;
- do not use GitHub-hosted runners merely to replace available self-hosted compute.

## Pipelined autonomy

While CI N runs:
- keep polling N;
- freeze N SHA;
- prepare N+1 on separate branch/worktree;
- do not merge dependent N+1 before N is GREEN and integrated;
- if N turns RED, repair N first while preserving isolated N+1;
- after N merge, reconcile N+1 to authoritative branch before final gates.

`WAITING_FOR_CI` is not terminal while independent work remains.

## Runtime/E2E truth

Mocks prove logic, not production integration.

When a visible golden path is required:
1. known clean state;
2. actual app/services for claimed layer;
3. perform complete workflow;
4. on blocking defect, stop;
5. fix root cause;
6. restart full golden path from beginning;
7. archive sanitized evidence and exact SHA.

## PR closure gate

Before READY_FOR_MERGE/COMPLETE:
- PR head equals tested SHA;
- required local gates green;
- canonical server CI terminal GREEN;
- required runtime/E2E evidence complete;
- no unresolved valid P0/P1/P2 in scope;
- ledgers/evidence updated;
- no secrets/unrelated workflow churn;
- branch reconciled to authoritative base.

P0 must replace any TBD CI command/runner detail in this runbook with actual repository truth.
