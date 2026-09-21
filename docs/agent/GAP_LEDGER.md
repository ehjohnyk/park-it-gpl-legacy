# PARK-IT GAP LEDGER

Live classification of repository/product gaps.

| ID | Severity | Area | Gap | State | Evidence/Next action |
|---|---|---|---|---|---|
| GAP-001 | P0 | Licensing | GPL-derived legacy code; commercial path unresolved | OPEN | P0 provenance + owner/legal decision |
| GAP-002 | P1 | Governance | default branch unprotected / rulesets empty | EXTERNAL_OWNER | configure GitHub ruleset/protection |
| GAP-003 | P1 | CI | canonical exact-SHA CI not yet evidenced for V2 planning state | OPEN | P0 CI baseline + observability |
| GAP-004 | P1 | Runtime | reproducible clean legacy runtime not yet re-proven | OPEN | P0 clean boot |
| GAP-005 | P1 | Dependencies | legacy dependency/security posture unknown | OPEN | P0 audit |
| GAP-006 | P1 | Data | legacy Mongo assumptions/migration path not reconciled | OPEN | P0 audit |
| GAP-007 | P1 | External APIs | legacy Google/maps/API assumptions not reconciled | OPEN | P0 audit |

P0 whole-repo hostile audit must add all valid findings and classify P0/P1/P2/P3/FALSE_POSITIVE/EXTERNAL_ONLY.
