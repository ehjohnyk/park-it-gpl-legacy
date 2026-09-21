# PARK-IT Self-Hosted Runner Bootstrap

Status: REQUIRED INFRASTRUCTURE GATE

The repository-side workflow is installed on default branch at:
`.github/workflows/server-ci.yml`

Trigger:
`/server-ci <exact-current-pr-head-sha>`

Required runner labels:
`self-hosted`, `ci-vps`

## Current finding

The PARK-IT workflow can be triggered and GitHub creates the run, but the `Authorize exact PR candidate` job remains queued when no PARK-IT repository runner with labels `self-hosted, ci-vps` is online/eligible.

Because the repositories are under a personal GitHub owner rather than an organization runner group, a repository-level runner used by another repository must not be assumed eligible for PARK-IT. The same VPS may host another runner instance registered specifically to `ehjohnyk/park-it`.

## Registration procedure

On GitHub:
1. Open `ehjohnyk/park-it -> Settings -> Actions -> Runners`.
2. Choose `New self-hosted runner`.
3. Select the VPS OS/architecture.
4. Use the commands GitHub generates at that moment; do not copy an old registration token from documentation.
5. On the VPS, install/configure the runner in its own PARK-IT runner directory/service.
6. Add the custom label `ci-vps` during configuration or immediately after registration.
7. Configure it as a persistent service under the existing server operating policy.
8. Never commit the registration token, PAT, service credentials or runner credentials.

## Validation

After the runner is ONLINE:
1. obtain the exact current PR head SHA;
2. comment `/server-ci <sha>` on that PR;
3. verify job runs on the intended VPS runner;
4. verify checkout HEAD exactly equals requested SHA;
5. observe run to terminal state;
6. verify `canonical/server-ci` status appears on the candidate SHA;
7. record run ID, job IDs, runner identity, SHA and result in the evidence/ledger.

Do not duplicate-run the same unchanged SHA.

## Recovery

If jobs stay queued:
- verify runner service is running;
- verify PARK-IT repository registration, not only another repository's runner;
- verify labels include both `self-hosted` and `ci-vps`;
- verify the runner is not disabled/offline;
- verify another job is not occupying the only PARK-IT runner;
- inspect GitHub Actions runner diagnostics/service logs.

If server access/registration token is unavailable, record `CI_VPS_RUNNER_REGISTRATION = OWNER_EXTERNAL` and continue independent machine-solvable P0 work. Do not claim CI GREEN.
