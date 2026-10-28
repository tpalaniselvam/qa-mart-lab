---
name: test-risk-prioritization
description: "Use when choosing which QA Mart flows or test cases to automate first, ranking a test-case table by risk, or deciding what to protect with a limited time budget."
---
# Test risk prioritization

1. List candidate flows or cases (from `docs/stories/`).
2. Score each 1-5 for impact (money, security, data loss, legal) and likelihood (recent change, complexity, past defects).
3. Priority = impact x likelihood. Break ties by cheapest to automate at API level.
4. Choose the layer: rules and security at API, user journeys at UI, never the same check at both.
5. Output a table: case, story AC, impact, likelihood, score, layer, reason. Mark the top three as "protect first".

Defaults for QA Mart: coupon math and one-time coupons, order authorization (no access to other users' orders), stock limits, and the checkout happy path rank highest.
