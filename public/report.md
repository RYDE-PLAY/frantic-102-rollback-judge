# Rollback Judge Graph Revision Report

This revision fixes Frantic #102 by removing the resume-dependent dogfood path.
The default `judge` runner is now a one-step review graph that seals the domain
act in a single stock `runx-cli 0.6.14` run, so the receipt records the
decision, reason, and target without relying on patched resume behavior.

## Submitted Revision

- Package: `ryde-play/rollback-judge@sha-9c10d9cc79c2`
- Registry URL: https://runx.ai/x/ryde-play/rollback-judge@sha-9c10d9cc79c2
- PR: https://github.com/runxhq/runx/pull/277
- Source commit: `1d6ebc7607aab780db2d71293beec9c2ca15be5d`
- Raw X.yaml: https://raw.githubusercontent.com/RYDE-PLAY/runx/1d6ebc7607aab780db2d71293beec9c2ca15be5d/skills/rollback-judge/X.yaml
- Raw SKILL.md: https://raw.githubusercontent.com/RYDE-PLAY/runx/1d6ebc7607aab780db2d71293beec9c2ca15be5d/skills/rollback-judge/SKILL.md
- Harness evidence: https://raw.githubusercontent.com/RYDE-PLAY/runx/1d6ebc7607aab780db2d71293beec9c2ca15be5d/skills/rollback-judge/harness-evidence.json
- Dogfood receipt: `runx:receipt:sha256:7cdf335e3d940dede2ce5c469426f714bb3ccfb249a116fc18304b4ab23f9044`

## What Changed

- Added a default graph runner `judge` that runs `run.mjs` and seals the graph turn as `act.form=review`.
- Kept `agent_review` for the inline harness so `critical-signal-rollback` seals and `signal-contradictory-block` still returns `needs_agent` without seeding approval.
- The graph act uses `reason_step: decide`, `reason_from: act_reason`, `decision_from: act_decision`, and `target_from: act_target_ref`.
- The post-publish dogfood command passes `act_decision=approve` and `act_target_ref=runx:release:checkout@2026.07.12.2` as trusted receipt bindings.
- No deploy, rollback, publish, provider write, or authority mint occurs; the skill emits only a review packet and release approval answer.

## Receipt Proof

- Receipt act id: `act_turn`
- Receipt act form: `review`
- Receipt act summary: `action=rollback reason=error_rate_critical target=2026.07.12.2`
- Receipt act target ref: `runx:release:checkout@2026.07.12.2`
- Receipt decision choice: `close`
- Receipt decision target ref: `runx:release:checkout@2026.07.12.2`
- Verify verdict: `valid=true`, `signature.status=valid`, `signature.mode=production`

## Verification Runbook

- Install: `runx add ryde-play/rollback-judge@sha-9c10d9cc79c2 --registry https://api.runx.ai --json`
- Hosted harness: `runx harness ./skills/ryde-play/rollback-judge/sha-9c10d9cc79c2 --json`
- Dogfood: `runx skill ryde-play/rollback-judge@sha-9c10d9cc79c2 --registry https://api.runx.ai --json --input-json deploy_signal=<fixtures/critical-signal.deploy_signal> --input-json current_version=<fixtures/critical-signal.current_version> --input-json prior_version=<fixtures/critical-signal.prior_version> --input-json forward_fix_evidence=<fixtures/critical-signal.forward_fix_evidence> --input act_decision=approve --input act_target_ref=runx:release:checkout@2026.07.12.2`
- Verify: `RUNX_RECEIPT_VERIFY_KID=<kid> RUNX_RECEIPT_VERIFY_ED25519_PUBLIC_KEY_BASE64=<public_key_base64> runx verify --receipt runx-receipt.json --json`
- Public key: see `verification-key.json`; the private signing seed is intentionally absent.

## Acceptance Coverage

- The package name remains exactly `rollback-judge`.
- The live registry package, PR source, raw files, evidence, verification, report, and receipt all point to `ryde-play/rollback-judge@sha-9c10d9cc79c2` and source commit `1d6ebc7607aab780db2d71293beec9c2ca15be5d`.
- Local harness passed 2 cases: `critical-signal-rollback`, `signal-contradictory-block`.
- Hosted-installed harness passed 2 cases: `critical-signal-rollback`, `signal-contradictory-block`.
- The critical rollback dogfood seals a review receipt with action, reason, and target in receipt bytes.
- The contradictory signal harness case remains `needs_agent` and seeds no release approval.
- `runx verify` passed with a production Ed25519 signature.
