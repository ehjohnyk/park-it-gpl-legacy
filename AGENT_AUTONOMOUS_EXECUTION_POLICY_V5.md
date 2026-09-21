# Autonomous Execution Policy V5 — Pipelined Slice Continuation

Status: CANONICAL AGENT GOVERNANCE OVERRIDE

This policy supplements `AGENT_AUTONOMOUS_EXECUTION_CONTRACT.md` and V4. If they conflict on waiting behavior, next-slice preparation, CI overlap or continuation, V5 wins.

## 1. Never idle on CI

When exact-SHA canonical CI is PENDING/RUNNING, continue polling it but do not otherwise idle while independent machine-solvable work exists.

Prepare the next dependency-unblocked slice in parallel: audit, plan, tests and implementation may proceed in a separate branch/worktree when safe.

## 2. Freeze in-flight candidate

A candidate under canonical CI is immutable. Do not move its SHA or mix next-slice work into it.

One frozen candidate per CI run. No duplicate run for unchanged SHA.

## 3. Dependency-aware speculative preparation

A next slice may be prepared before prior merge if isolated safely. If it depends on unmerged code, it must be reconciled/rebased/reconstructed against final authoritative default branch before its own final validation/CI.

Never treat an unmerged candidate as canonical truth.

## 4. Terminal CI priority

If CI turns RED, prioritize root-cause repair of that candidate while preserving isolated next-slice work.

If GREEN, complete review/merge/post-merge gate, refresh authoritative default branch, reconcile next slice, then freeze its own candidate.

## 5. Continuous program execution

After a slice becomes COMPLETE, refresh repository truth, recompute dependency graph, select next ready machine-solvable slice and continue automatically.

Do not stop because CI is running, one PR merged, one selector finished, owner has not sent another prompt, or next slice was not explicitly named when derivable from repository truth.

Stop only when assigned machine-solvable program is exhausted or a genuine external blocker prevents all remaining relevant work.

## 6. Parallel safety

- separate branches/worktrees;
- no conflicting parallel writes without reconciliation plan;
- dependent slice cannot merge before prerequisite is GREEN/integrated;
- each next candidate reconciles to authoritative branch before final CI;
- all security/migration/runtime/E2E requirements remain unchanged.

## 7. Preferred pipeline

`SLICE N IMPLEMENT -> LOCAL CLEAN -> FREEZE SHA -> CANONICAL CI N`

while CI N runs:

`POLL CI N + AUDIT/PLAN/IMPLEMENT SLICE N+1 IN ISOLATION`

then:

- RED: `REPAIR N -> GREEN -> MERGE N -> RECONCILE N+1`
- GREEN: `MERGE N -> RECONCILE N+1 -> LOCAL CLEAN -> FREEZE N+1 -> CI N+1`

Repeat until machine-solvable work is exhausted.

## 8. Status reporting

`WAITING_FOR_CI` is not terminal while independent work remains.

Final report states:
- current/last completed slice;
- exact CI SHA/status;
- next selected/prepared slice;
- whether reconciled to authoritative branch;
- remaining machine-solvable work;
- genuine external blockers only.
