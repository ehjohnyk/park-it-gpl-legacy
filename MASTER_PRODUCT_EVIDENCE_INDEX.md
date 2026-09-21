# PARK-IT MASTER PRODUCT EVIDENCE INDEX

Canonical registry of evidence used to support readiness claims.

## Governance baseline — 2026-09-21

Observed repository facts:
- repo: `ehjohnyk/park-it`;
- default branch: `master`;
- pre-planning base SHA: `71f7549683fdb961465109313af8902741057970`;
- V2 planning PR: #2;
- repository is a fork of `nishoknm/park-it`;
- license: GPL-3.0;
- `master` observed unprotected;
- repository rulesets observed empty;
- planning candidate observed with no reported commit status.

Canonical architecture evidence:
- `docs/PARK_IT_V2_ARCHITECTURE_FREEZE.md`
- `docs/PARK_IT_PRODUCT_ARCHITECTURE.md`
- `docs/MOBILITY_SPACE_PLATFORM_SCOPE.md`
- `docs/PERMIT_ORCHESTRATION_AND_PAYMENTS.md`

## P0 evidence

PENDING. P0 must populate:
- clean-install/start logs;
- exact runtime/tool versions;
- dependency/security scan;
- provenance/license inventory;
- characterization tests;
- legacy API/config inventory;
- CI runner identity;
- exact tested SHA;
- run/job ID;
- terminal result;
- relevant logs/artifacts;
- clean-room/GPL decision record.

## Evidence rule

Mocks/fixtures/simulators prove logic only, not real integration. Evidence must state environment and provenance. A new runtime-affecting SHA normally requires new closure CI/runtime evidence.
