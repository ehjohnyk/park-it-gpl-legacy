# PARK-IT MASTER PRODUCT EXTERNAL BLOCKERS

Tracks genuine external/owner/legal/credential blockers. A blocker stops only dependent work.

## Current blockers

### CI_VPS_RUNNER_REGISTRATION — OWNER/INFRA
Repository-side `/server-ci <sha>` workflow is active on `master`, but validation run 35567913127 queued at the first `[self-hosted, ci-vps]` job. PARK-IT requires an eligible online repository runner on the VPS (or equivalent approved runner scope). Follow `docs/SERVER_CI_RUNNER_BOOTSTRAP.md`. This blocks canonical CI closure but not independent P0 audit/remediation.

### COMMERCIAL_LICENSE_PATH — OWNER/LEGAL
The repository is GPL-3.0-derived. Before claiming a proprietary/commercially license-clean V2, decide and review:
- GPL-compatible distribution path; or
- clean-room V2 implementation in a fresh codebase/repository.

Engineering can prepare provenance evidence; final legal determination is external.

### GITHUB_BRANCH_PROTECTION — OWNER/ADMIN
`master` was observed unprotected and rulesets empty. Current connected GitHub interface exposes reads but not repository administration writes for this control. Owner/admin must configure protection/rulesets if desired.

### PRODUCTION_PAYMENT_ACTIVATION — OWNER/EXTERNAL
Real money movement/KYC/payout activation requires PSP account, credentials and owner authorization.

### AUTHORITY/GOVERNMENT_INTEGRATIONS — EXTERNAL
Production permits, authoritative municipal rules, enforcement and e-government submission require jurisdiction/provider credentials/agreements.

### AUTHORITATIVE_ROUTE_INFRASTRUCTURE_DATA — EXTERNAL
Real abnormal-load route/bridge/height/weight clearance claims require authoritative licensed data/integration.

### PHYSICAL_ACCESS_ANPR_HARDWARE — OWNER/EXTERNAL
Real gates/locks/ANPR/Access Box activation requires hardware/environment credentials and explicit activation authorization.

### PRODUCTION_LEGAL_PRIVACY_REVIEW — EXTERNAL
Consumer terms, marketplace liability, GDPR, municipal/enforcement roles and payment regulatory posture require qualified review before production.

## Machine-solvable work remains

External blockers above do NOT block:
- P0 audit;
- architecture/domain design;
- simulators/sandboxes;
- provider contracts/adapters;
- deterministic tests;
- local runtime;
- server CI;
- non-production golden paths.

Never stop the whole program because one provider/authority/hardware blocker exists.
