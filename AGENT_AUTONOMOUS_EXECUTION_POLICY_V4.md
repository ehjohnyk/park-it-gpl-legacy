# Autonomous Execution Policy V4 — CI Observability, Completion and Local Hygiene

Status: CANONICAL AGENT GOVERNANCE OVERRIDE

This policy supplements `AGENT_AUTONOMOUS_EXECUTION_CONTRACT.md`. If they conflict on CI observability, completion status, local-environment classification, long-running commands or unrelated workflow edits, this V4 policy wins.

## 1. End-to-end autonomy

For major work the agent must audit repository truth, update the executable plan, decompose work into dependency-aware TODOs and continue through every machine-solvable item.

Canonical loop:

`AUDIT -> PLAN/TODOS -> IMPLEMENT -> FOCUSED LOCAL TESTS -> FULL LOCAL VALIDATION -> REAL RUNTIME -> VISIBLE E2E -> FINAL DIFF AUDIT -> CANONICAL SELF-HOSTED CI -> OBSERVE TO TERMINAL -> FIX RED -> MINIMUM RERUN -> GREEN EXACT SHA -> EVIDENCE -> MERGE/CLOSURE -> RESYNC -> NEXT ITEM`

## 2. CI observability gate

Before triggering canonical CI establish `CI_OBSERVABILITY_GATE = PASS`.

The agent must be able to:
- identify exact candidate SHA;
- query PENDING/RUNNING/GREEN/RED;
- poll until terminal;
- identify failed job/stage;
- retrieve sufficient logs/artifacts for root cause.

Preferred channels:
1. repository CI tooling/status command;
2. authenticated GitHub API/CLI;
3. self-hosted CI server API/CLI/SSH;
4. trusted existing status bridge.

If CI can be triggered but not observed, record `CI_TRIGGERABLE_BUT_NOT_OBSERVABLE` and repair the observation path if machine-solvable. Do not create duplicate runs.

PENDING/RUNNING/UNKNOWN is not a terminal completion state.

## 3. Canonical self-hosted CI

Use configured server/self-hosted CI as the primary canonical gate. Do not switch to GitHub-hosted compute for convenience or duplicate equivalent validation.

A CI-required slice is COMPLETE only when the final unchanged exact SHA is GREEN, unless an explicit repository policy proves immutable equivalence.

## 4. RED means continue

On RED:
1. inspect exact failed job/stage;
2. retrieve logs/artifacts;
3. determine root cause;
4. reproduce/narrow locally where feasible;
5. fix root cause;
6. rerun relevant local gates;
7. rerun only minimum required CI scope;
8. continue to GREEN or genuine external blocker.

Never blind-rerun. Never weaken tests, security, tenant isolation, payment integrity, routing provenance, regulatory truth, access fail-closed behavior or acceptance criteria to obtain GREEN.

## 5. HUMAN_REQUIRED is narrow

Not automatically human-required:
- local Docker/runtime limitation;
- test/CI failure;
- merge conflict;
- dependency/toolchain repair;
- CI visibility issue that can be repaired;
- long-running command;
- line-ending warning.

Human/owner required is reserved for genuinely external credentials/entitlements, legal/commercial decisions, irreversible production authorization, non-emulatable physical environments, real payment/authority/hardware activation, or explicit owner gates.

## 6. Baseline proof

Do not call a failure pre-existing without comparing authoritative baseline and candidate using equivalent environment/command when practical.

Classify: `BASELINE`, `CANDIDATE_REGRESSION`, or `ENVIRONMENT/TOOLCHAIN`.

Candidate regressions must be fixed.

## 7. Long-running command watchdog

For long-running tests/builds/migrations/network/CI helpers:
- know expected process;
- use suitable timeouts;
- inspect process/log/CPU/I/O/locks/ports if silent unusually long;
- terminate only with evidence of hang/wrong wait;
- preserve work;
- never blind-rerun unexplained hangs.

## 8. Workflow isolation

Normal product slices must not modify CI/workflow files due only to formatter churn or unrelated checks. CI changes require explicit infrastructure scope.

Do not add temporary GitHub-hosted fallbacks while canonical self-hosted infrastructure is available.

## 9. Local limitations do not erase runtime requirements

If local environment cannot run a required DB/browser/provider/runtime, execute all feasible local gates and use canonical server CI or required dedicated environment for the missing gate. Never fabricate PASS or downgrade required E2E.

## 10. Final diff / frozen SHA

Before canonical CI:
- working tree clean;
- candidate reconciled to authoritative base;
- changed files reviewed;
- no unrelated churn;
- no secrets;
- no test weakening;
- required migrations/security/data invariants checked;
- candidate SHA frozen and recorded.

Do not create duplicate CI runs for unchanged SHA.

## 11. Completion vocabulary

For CI-required work terminal success requires:

- `REPO_CONSISTENCY_GATE = PASS`
- `CI_OBSERVABILITY_GATE = PASS`
- `CI_REQUIRED = YES`
- `CI_STATUS = GREEN`
- `CI_SHA = <final exact SHA>`
- required runtime/E2E evidence
- no unresolved in-scope blocker.

Only then report COMPLETE / SEALED / READY_FOR_MERGE.
