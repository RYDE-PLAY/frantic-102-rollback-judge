# Rollback Judge Release-Consumption Revision Report

This revision addresses the remaining Frantic #102 review item: the dogfood run must consume the rollback approval as a release gate in the same graph run and record the resulting release advancement.

## Submitted Revision

- Package: `ryde-play/rollback-judge@sha-90245faf0359`
- Registry: https://runx.ai/x/ryde-play/rollback-judge@sha-90245faf0359
- Source: https://github.com/RYDE-PLAY/runx/tree/06ee7dd89afbc09e7271fac50bf6429c97733c8f/skills/rollback-judge
- PR: https://github.com/runxhq/runx/pull/277
- Raw X.yaml: https://raw.githubusercontent.com/RYDE-PLAY/runx/06ee7dd89afbc09e7271fac50bf6429c97733c8f/skills/rollback-judge/X.yaml
- Raw SKILL.md: https://raw.githubusercontent.com/RYDE-PLAY/runx/06ee7dd89afbc09e7271fac50bf6429c97733c8f/skills/rollback-judge/SKILL.md
- Release rail source: https://raw.githubusercontent.com/RYDE-PLAY/runx/06ee7dd89afbc09e7271fac50bf6429c97733c8f/skills/rollback-judge/release.mjs
- Dogfood receipt: `runx:receipt:sha256:dd5855ab09e05e30fb2b4218126a594fd3989669802d7422cf877ae7bba95a41`

## Review Item Addressed

- The default `judge` graph now runs `decide` and then `release`.
- The `release` step consumes `decide.release_publish_approval` as `release.publish.approval`.
- The same dogfood graph produced `release_execution_result.consumed=true` and `release_execution_result.advanced=true`.
- The sealed act summary and artifact ref record rollback, `error_rate_critical`, and target `runx:release:rollback-judge-demo@2026.07.15.3`.

## Post-Publish Dogfood

- Published package: `ryde-play/rollback-judge@sha-90245faf0359`
- Run id: `run_judge_7f0575971c1b`
- Graph status: `Succeeded`
- Decide receipt: `sha256:e1c3b484145a07ec048aa4c4ed5f870310558873661b1d39dfdae61120ed7167`
- Release receipt: `sha256:f15a7b0e88359b9947495634dfcbe8ef5add99e968be2d0da98595ba0d946cdc`
- Primary receipt: `sha256:dd5855ab09e05e30fb2b4218126a594fd3989669802d7422cf877ae7bba95a41`
- Release rail operation: `advance_on_release_publish_approval`
- Gate: `release.publish.approval`
- Action: `rollback`
- Reason: `error_rate_critical`
- Target: `runx:release:rollback-judge-demo@2026.07.15.3`
- Advanced: `true`
- Consumed: `true`

## Live Source Read

- Current failing run: https://github.com/RYDE-PLAY/frantic-102-rollback-judge/actions/runs/29393017505
- Current commit: `d118cae3bfbe30e1d65d021f8b3ae163166e6041`
- Current marker: version `2026.07.15.4`, health `degraded`, 5xx `18.4% > 2%`.
- Prior healthy run: https://github.com/RYDE-PLAY/frantic-102-rollback-judge/actions/runs/29392993457
- Prior commit: `ab4d82d0f136bd4e5d3d9c7b2e1811e9a17005c9`
- Prior marker: version `2026.07.15.3`, health `healthy`, 5xx `0.4% <= 2%`.

## Verification

- `pnpm verify:fast` passed in the source worktree.
- Registry publish succeeded and registry read returned trusted community provenance.
- Clean install of `ryde-play/rollback-judge@sha-90245faf0359` succeeded.
- Clean installed-package harness passed two cases.
- Production `runx verify` returned `valid=true`, `signature.status=valid`, and no findings.

## Evidence Files

- `evidence.json`: summary and artifact index.
- `verification.json`: compact pass/fail verification record.
- `registry-dogfood.json`: full post-publish dogfood output, including both graph steps.
- `graph-state.json`: graph-state evidence for the same run.
- `runx-receipt.json` and `sha256:dd5855ab09e05e30fb2b4218126a594fd3989669802d7422cf877ae7bba95a41.json`: the submitted receipt bytes.
- `runx-verify.json`: production verification verdict.
- `verification-key.json`: public verification key only.
