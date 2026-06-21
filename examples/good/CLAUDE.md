# CLAUDE.md

Project instructions for the Acme Widgets API. Compliant with context-guard:
under 300 lines, with the rules block and behavioral guidelines below.

## Context Rules (context-guard)

> Managed by context-guard — keep these files within their limits.

- **CLAUDE.md** ≤ 300 lines. Anything not needed in context *every time* → move
  it to its own file and leave a one-line pointer (`> For X, see docs/x.md`).
- **CLAUDE.md** must always include this rules block (near the top) and the team
  behavioral guidelines block.
- **MEMORY.md** is optional; create it only if something must persist across
  sessions. Ceiling 150 lines. When over, condense the oldest entries (1–2 lines)
  and move full text to **ARCHIVE.md**, leaving a pointer.
- **AGENTS.md** and **.cursor/rules/*.md** stay in sync with CLAUDE.md where
  byte-identical sync is mandated.

## Project quick facts

- Stack: TypeScript, Node 20, Fastify, PostgreSQL.
- Entry point: `src/server.ts`. Routes under `src/routes/`.
- Tests: `npm test` (Vitest). Lint: `npm run lint`.

## Pointers (extracted to keep this file small)

> For the full architecture overview, see `docs/architecture.md`.
> For the deployment runbook, see `docs/deploy.md`.
> For the database schema and migration policy, see `docs/database.md`.
> For API style conventions, see `docs/api-style.md`.

## Behavioral guidelines

Behavioral guidelines to reduce common LLM coding mistakes. Merge with project-specific instructions as needed.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

---

**These guidelines are working if:** fewer unnecessary changes in diffs, fewer rewrites due to overcomplication, and clarifying questions come before implementation rather than after mistakes.
