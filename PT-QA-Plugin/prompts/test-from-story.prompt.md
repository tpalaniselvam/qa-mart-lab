---
description: Turn a QA Mart story into a test-case table and a first Playwright spec.
argument-hint: story id, for example US-103
agent: PT Test Designer
---
Story id: ${input:story:US-103}

1. Read the matching file in `docs/stories/`. If none matches, say so and stop.
2. Write a test-case table per `docs/templates/test-case.md` with technique, UI/API and priority.
3. Write `tests/lab/<story id lowercase>.spec.ts` for the highest-priority rows with the story id and AC in each title.
4. Run `npm run typecheck`, `npm run lint:specs` and the spec.
5. Report app-vs-story disagreements as bugs. Do not weaken assertions or edit `app/`.
