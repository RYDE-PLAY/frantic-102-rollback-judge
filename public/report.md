# Rollback Judge Evidence Report

Published package: ryde-play/rollback-judge@sha-daf218f5cd7d

Public URL: https://runx.ai/x/ryde-play/rollback-judge@sha-daf218f5cd7d

Source: https://github.com/RYDE-PLAY/runx/tree/f805abe0df64968888068e3af2ee1c2d94f93582/skills/rollback-judge

PR: https://github.com/runxhq/runx/pull/277

Raw files:

- X.yaml: https://raw.githubusercontent.com/RYDE-PLAY/runx/f805abe0df64968888068e3af2ee1c2d94f93582/skills/rollback-judge/X.yaml
- SKILL.md: https://raw.githubusercontent.com/RYDE-PLAY/runx/f805abe0df64968888068e3af2ee1c2d94f93582/skills/rollback-judge/SKILL.md
- Harness evidence: https://raw.githubusercontent.com/RYDE-PLAY/runx/f805abe0df64968888068e3af2ee1c2d94f93582/skills/rollback-judge/harness-evidence.json

## Verification

- Local harness: passed, cases critical-signal-rollback, signal-contradictory-block
- Hosted-installed harness: passed, cases critical-signal-rollback, signal-contradictory-block
- Dogfood receipt: runx:receipt:sha256:0d8487ce947b67ae04d36bdcea199235e86d1a20e2c45db6030ada7e28bb6577
- Verify verdict: valid=true, signature.status=valid, signature.mode=production

## Dogfood Decision

The post-publish registry run consumed the critical-signal fixture and produced decision.action=rollback, reason=error_rate_critical, version_target=2026.07.12.2. It seeded release_publish_approval.gate_id=release.publish.approval with approved=true. The receipt act is review, and no authority terms were minted.

## Boundary

rollback-judge is a review-only handoff skill. It does not deploy, roll back, publish, call providers, or mint authority. The release graph owns actual consequences after the named approval handoff.
