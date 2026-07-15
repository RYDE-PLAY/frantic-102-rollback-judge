# Rollback Judge Live-Source Revision Report

This revision addresses the human review for Frantic #102. The published
default runner now reads a real deployment-monitor source at execution time,
derives both current and prior versions from immutable source commits, and
seals the resulting review without caller-supplied decision or release-target
fields.

## Submitted Revision

- Package: `ryde-play/rollback-judge@sha-37688e7136f0`
- Registry: https://runx.ai/x/ryde-play/rollback-judge@sha-37688e7136f0
- Source: https://github.com/RYDE-PLAY/runx/tree/9d08a5e66ae4b8115718a907940b497c6f9a1bda/skills/rollback-judge
- PR: https://github.com/runxhq/runx/pull/277
- Raw X.yaml: https://raw.githubusercontent.com/RYDE-PLAY/runx/9d08a5e66ae4b8115718a907940b497c6f9a1bda/skills/rollback-judge/X.yaml
- Raw SKILL.md: https://raw.githubusercontent.com/RYDE-PLAY/runx/9d08a5e66ae4b8115718a907940b497c6f9a1bda/skills/rollback-judge/SKILL.md
- Dogfood receipt: `runx:receipt:sha256:3788f5ce43f22987fbec4d9be437644d0a24fdc698299e29f8a4c886a5c192ff`

## Real Deployment Signal

- The public workflow reads `public/deploy-target.json` over HTTPS and fails
  when `health` is not `healthy` or observed `http_5xx_rate` exceeds its
  threshold.
- Prior healthy run: https://github.com/RYDE-PLAY/frantic-102-rollback-judge/actions/runs/29392993457
- Prior source commit: `ab4d82d0f136bd4e5d3d9c7b2e1811e9a17005c9`
- Prior marker: version `2026.07.15.3`, health `healthy`, 5xx `0.4% <= 2%`.
- Current failing run: https://github.com/RYDE-PLAY/frantic-102-rollback-judge/actions/runs/29393017505
- Current source commit: `d118cae3bfbe30e1d65d021f8b3ae163166e6041`
- Current marker: version `2026.07.15.4`, health `degraded`, 5xx `18.4% > 2%`.
- Both Actions runs and both commit-pinned markers are public and independently
  readable. The run conclusion is produced by the workflow calculation, not by
  the rollback-judge fixture.

## What Changed

- The default `judge` graph fetches the current GitHub Actions run API during
  execution and checks repository, run id, workflow name, status, conclusion,
  and full head SHA.
- It fetches the deployment marker from that immutable head SHA and records the
  response SHA-256, ETag, fetch time, metric, threshold, health, incident, and
  version in `source_read`.
- For rollback, it repeats the same process for
  `prior_version.source_url` and accepts only a completed successful run whose
  marker is healthy and below threshold.
- It no longer trusts `severity: critical` by itself and no longer accepts
  `act_decision` or `act_target_ref` inputs.
- The caller supplies the public failing run URL as `monitor_run_ref`; the
  runner validates exact equality with the API's `html_url` before stock runx
  binds it as the review act target.
- Stock `effect_step/effect_from` binds the source-derived rollback release ref
  into the receipt. No runtime patch is needed.
- PR #277 now changes only six files under `skills/rollback-judge/`. Runtime
  crates and official-skill indexes are absent from the PR file list.

## Harness And Dogfood

- Local harness passed two cases with `runx-cli 0.6.14`.
- The package publish passed the registry hosted harness.
- A clean install of `ryde-play/rollback-judge@sha-37688e7136f0` succeeded.
- The clean installed-package harness passed two cases.
- Hosted harness uses deterministic caller answers tied to the public immutable
  monitor runs; this keeps the hosted fixture reproducible where outbound
  network access is unavailable.
- The post-publish dogfood did not use the harness fixture. It invoked the
  published package's default `judge` runner with the public monitor URLs.
- The dogfood fetched current run `29393017505` and prior run `29392993457` at
  execution time and returned rollback / `error_rate_critical` / target
  `2026.07.15.3`.
- The sealed review act targets the real failing run and records the derived
  `runx:release:rollback-judge-demo@2026.07.15.3` artifact ref.
- A separate negative execution pointed the critical label at the successful
  run and returned hold / `nonfailing_signal` with approval false.
- `runx verify` returned `valid=true`, `signature.status=valid`,
  `signature.mode=production`, and no findings.

## New User Runbook

- Check CLI: `runx --version` (this revision used `runx-cli 0.6.14`).
- Install: `runx add ryde-play/rollback-judge@sha-37688e7136f0 --registry https://api.runx.ai --json`.
- Prepare typed inputs containing the current run API URL, its public HTML URL,
  the repository-relative marker path, a prior successful run API URL, and any
  tested forward-fix evidence.
- Run: `runx skill ryde-play/rollback-judge@sha-37688e7136f0 --registry https://api.runx.ai --json` with the five arguments shown in `evidence.json.commands.dogfood`.
- Save the returned `receipt` object as `runx-receipt.json`.
- Verify with the public key in `verification-key.json` and:
  `runx verify --receipt runx-receipt.json --json`.
- Inspect `registry-dogfood.json` to compare the fetched URLs, response hashes,
  monitor conclusions, markers, decision, approval answer, and sealed receipt.

The skill remains read-only. It emits the typed
`release.publish.approval` answer and names `release` as the downstream owner;
it performs no rollback, publish, or deploy effect itself.
