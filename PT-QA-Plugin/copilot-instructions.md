# Copilot instructions: QA Mart Playwright tests (PT-Test)

## Situation
- App under test: QA Mart at http://localhost:3000 (`npm start`). Playwright Test, TypeScript, Chromium default.
- The v2 redesign changes classes, ids, DOM order. Accessible names stay the same; tests must pass on v1 and v2.

## Terms
- Page Objects: `pages/<Name>Page.ts`, readonly locators, async actions, no assertions.
- Fixtures: `tests/fixtures.ts`. Data and credentials: `tests/test-data.ts`.
- Locator order: `getByRole` > `getByLabel` > `getByText` > `getByTestId`. No XPath, CSS classes, ids or `.nth()`.
- Web-first assertions only. No `waitForTimeout`; no read-then-assert.
- One behavior per test, story id (for example `US-101 AC3`) in the title, `test.step` for multi-step flows.
- Never edit `app/`. Never weaken an assertion; report app bugs with `docs/templates/bug-report.md`.
- Never commit secrets; use `QA_USER` and `QA_PASS` environment variables.
- Before handing over: `npm run typecheck`, `npm run lint:specs`, `npm test`.
