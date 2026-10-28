---
description: Triage the last Playwright run and draft bug reports for real app defects.
argument-hint: optional project name, for example seeded
agent: PT Bug Triage
---
Scope: ${input:scope:all projects}

Use the `qamart` MCP report tools, classify every failure (app bug, test bug, flaky, environment), and draft bug reports with `docs/templates/bug-report.md` for the app bugs.
