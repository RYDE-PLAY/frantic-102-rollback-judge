# Rollback Judge Revision Report

This revision addresses the human review note for Frantic #102. The previous receipt proved that a review act sealed, but its act summary did not carry the judged rollback decision, reason, or release target. The revised package and PR make those values explicit receipt bindings.

## Submitted Revision

- Package: `ryde-play/rollback-judge@sha-c78cb2af1e56`
- Registry URL: https://runx.ai/x/ryde-play/rollback-judge@sha-c78cb2af1e56
- PR: https://github.com/runxhq/runx/pull/277
- Source commit: `a3ca02e25bb77a27a83ba65cea02d25274fb767f`
- Raw X.yaml: https://raw.githubusercontent.com/RYDE-PLAY/runx/a3ca02e25bb77a27a83ba65cea02d25274fb767f/skills/rollback-judge/X.yaml
- Raw SKILL.md: https://raw.githubusercontent.com/RYDE-PLAY/runx/a3ca02e25bb77a27a83ba65cea02d25274fb767f/skills/rollback-judge/SKILL.md
- Dogfood receipt: `runx:receipt:sha256:5e3bd02d3ad36ec4a51fbde4eeb58a3cc628b866cb5f09b3fc2fd2bdfed704ce`

## What Changed

- Added `act.reason_from: act_reason` so the sealed review act summary is model-authored from the rollback output.
- Added `act.decision_from: act_decision` and seeded `act_decision=approve` for the critical rollback case.
- Added `act.target_from: act_target_ref` and seeded `act_target_ref=runx:release:checkout@2026.07.12.2`.
- Added `act_reason` to the output schema and harness answer as `action=rollback reason=error_rate_critical target=2026.07.12.2`.
- Preserved pending run inputs through `runx resume` in the PR source so resume-time receipts can bind trusted inputs into `target_refs`.

## Receipt Proof

- Receipt act id: `act_judge`
- Receipt act form: `review`
- Receipt act summary: `action=rollback reason=error_rate_critical target=2026.07.12.2`
- Receipt act target ref: `runx:release:checkout@2026.07.12.2`
- Receipt decision target ref: `runx:release:checkout@2026.07.12.2`
- Verify verdict: `valid=true`, `signature.status=valid`, `signature.mode=production`

## Verification Runbook

- Install: `runx add ryde-play/rollback-judge@sha-c78cb2af1e56 --registry https://api.runx.ai --json`
- Hosted harness: `runx harness ./skills/ryde-play/rollback-judge/sha-c78cb2af1e56 --json`
- Dogfood: `runx skill ryde-play/rollback-judge@sha-c78cb2af1e56 --registry https://api.runx.ai --json --input-json deploy_signal=<fixtures/critical-signal.deploy_signal> --input-json current_version=<fixtures/critical-signal.current_version> --input-json prior_version=<fixtures/critical-signal.prior_version> --input-json forward_fix_evidence=<fixtures/critical-signal.forward_fix_evidence> --input act_decision=approve --input act_target_ref=runx:release:checkout@2026.07.12.2; runx resume run_agent_task-rollback-judge-output /tmp/rollback-judge-answer.json --json`
- Verify: `RUNX_RECEIPT_VERIFY_KID=<kid> RUNX_RECEIPT_VERIFY_ED25519_PUBLIC_KEY_BASE64=<public_key_base64> runx verify --receipt runx-receipt.json --json`
- Public key: see `verification-key.json`; the private signing seed is intentionally absent.

## Acceptance Coverage

- The package name remains exactly `rollback-judge`.
- The live registry package, PR source, raw files, evidence, verification, report, and receipt all point to `ryde-play/rollback-judge@sha-c78cb2af1e56` and source commit `a3ca02e25bb77a27a83ba65cea02d25274fb767f`.
- Local harness passed 2 cases: critical-signal-rollback, signal-contradictory-block.
- Hosted-installed harness passed 2 cases: critical-signal-rollback, signal-contradictory-block.
- The critical rollback case seals; the contradictory signal case remains `needs_agent`.
- The dogfood receipt records `action=rollback`, `reason=error_rate_critical`, and the target release version in receipt bytes.
- The skill still mints no authority and performs no deploy, rollback, provider write, or release publish.
