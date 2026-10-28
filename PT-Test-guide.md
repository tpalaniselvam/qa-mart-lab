# pt-qa Plugin Guide

pt-qa is a GitHub Copilot agent plugin for testing QA Mart with Playwright. It covers all six component types: instructions, prompt files, skills, agents, an MCP server and hooks.

## 1. Layout

```
PT-QA-Plugin/
├── plugin.json / PT-QA-Plugin.json   manifest (identical content)
├── mcp.json                            MCP server registration
├── copilot-instructions.md             always-on rules (root copy)
├── instructions/
│   ├── copilot-instructions.md         repo-wide conventions
│   ├── specs.instructions.md           applies to spec files
│   └── page-objects.instructions.md    applies to Page Objects
├── prompts/
│   ├── test-from-story.prompt.md       story -> test table -> spec
│   └── triage-failures.prompt.md       last run -> classified failures + bug drafts
├── skills/
│   ├── coupon-boundary-testing/        SKILL.md, rules.md, template.md
│   ├── playwright-failure-analysis/    SKILL.md, summarize-report.cjs
│   └── test-risk-prioritization/       SKILL.md
├── com.github.copilot/
│   ├── agents/                         test-designer, test-reviewer, bug-triage
│   └── hooks/hooks.json                preToolUse + postToolUse
├── mcp/qamart-server.cjs               stdio MCP server (no dependencies)
└── scripts/                            guard-app.cjs, post-edit-checks.cjs
```

## 2. Install

Prerequisites: Node 20+, `npm install`, and the app running (`npm start`, http://127.0.0.1:3000).

```powershell
# from a local clone
copilot plugin install ./PT-QA-Plugin
# or from GitHub after you push the repo
copilot plugin install <owner>/<repo>:PT-QA-Plugin
copilot plugin list
```

Verify: start a new session and run `/skills`, `/agents`, `/mcp`. Run `npm run mcp:smoke` to check the MCP server.

No secrets are stored. `QAMART_URL` in `mcp.json` is the only setting.

## 3. Components

### Instructions
Always-on rules: tests derive from the story, not current app behavior; role-based locators; no hard waits; Page Objects in `pages/`; story id and AC in every test title; never edit `app/`. `specs.instructions.md` and `page-objects.instructions.md` load only for matching files.

### Prompt files
| Prompt | Use | Agent |
|---|---|---|
| `test-from-story` | `/test-from-story US-103` writes a test-case table and `tests/lab/us-103.spec.ts`, then typechecks, lints and runs it | PT Test Designer |
| `triage-failures` | `/triage-failures seeded` classifies failures and drafts bug reports | PT Bug Triage |

### Skills (auto-loaded by description)
- `coupon-boundary-testing`: boundary values, caps, rounding, reuse rules for SAVE10, FLAT100, WELCOME50; includes a case template.
- `playwright-failure-analysis`: reads `reports/results.json` via `summarize-report.cjs`; classifies failures.
- `test-risk-prioritization`: ranks flows/cases by impact x likelihood to choose what to automate first.

### Agents
| Agent | Tools | Purpose |
|---|---|---|
| PT Test Designer | read, search, edit, execute, `qamart/*` | Story to risk-ranked cases and a verified spec |
| Test Reviewer | read-only | Reviews specs/Page Objects against `docs/team-conventions.md`, reports by severity |
| PT Bug Triage | read, search, execute, `qamart/*` (no edit) | App bug / test bug / flaky / environment, plus bug reports |

Tool scoping is deliberate: reviewer and triage agents cannot modify code.

### MCP server `qamart`
Tools: `qamart_health`, `qamart_list_products`, `qamart_validate_coupon`, `qamart_place_order`, `qamart_list_orders`, `qamart_reset`, `tests_last_run_summary`, `tests_list_failures`, `tests_find_flaky`. `place_order` and `reset` change app state; use against the lab app only.

### Hooks
- `preToolUse` (`guard-app.cjs`): denies edit/create/patch of anything under `app/`, so a failing test can never be "fixed" by changing the product.
- `postToolUse` (`post-edit-checks.cjs`): after edits to specs/pages, runs typecheck and spec lint and feeds failures back to the agent.

## 4. Demo workflow (proves a clean install)

1. `npm start` in one terminal.
2. `/test-from-story US-101` (coupon story). The skill supplies boundary cases (for example the "X or more" threshold).
3. Confirm the generated spec: `npx playwright test tests/lab/us-101.spec.ts`.
4. Run `npm run test:seeded`, then `/triage-failures seeded`. Seeded defects should be classed as app bugs with drafted reports.
5. Try asking the agent to edit `app/server.js`; the hook must deny it.

## 5. Safety and quality rules

- Never weaken assertions to go green; report the app defect.
- Keep the MCP server pointed at local/lab URLs only.
- Do not commit tokens or `.env` files.
- Humans review every generated test: AI drafts, testers decide.

## 6. Troubleshooting

| Symptom | Fix |
|---|---|
| MCP tools missing | `npm run mcp:smoke`; confirm Node 20+ and `${PLUGIN_ROOT}` resolves |
| `qamart_health` fails | start the app: `npm start` |
| Failure tools return nothing | run `npm test` first to create `reports/results.json` |
| Hook blocks a legitimate edit | the path is under `app/`; edit only tests, pages, docs |
| Plugin changes not seen | reinstall: `copilot plugin uninstall pt-qa` then install again |


