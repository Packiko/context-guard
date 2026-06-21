# context-guard — canonical rules (source of truth)

This file is the single source of truth for two things both skills share: the
**fixed-rules stanza** and the **rules-block template**. It is copied verbatim
into each skill's `references/rules.md` so every skill is self-contained when
installed alone. If you edit this file, copy it to every `references/rules.md`
and re-run the drift diff (see each SKILL.md verification step). All copies MUST
be byte-identical.

The rules are stated declaratively (the compliant end-state), so the same text
works for the read-only audit skill and the applying fix skill alike.

## Fixed-rules stanza

1. **CLAUDE.md** ≤ **300 lines**. Anything not needed in context *every time*
   belongs in its own file, with a one-line pointer left behind
   (`> For X, see docs/x.md`).
2. **CLAUDE.md** must always contain the team **behavioral guidelines** (the four
   sections in `references/behavioral-guidelines.md`) plus a **rules block** near
   the top.
3. **MEMORY.md** is optional. Ceiling = **150 lines**. When exceeded, the OLDEST
   entries are condensed (1–2 lines each) and their full original text lives in
   **ARCHIVE.md**, with a pointer left in MEMORY.md.
4. **AGENTS.md** and **.cursor/rules/*.md** stay in sync with CLAUDE.md where the
   project mandates byte-identical sync. Drift is reported.

## Rules-block template

Belongs near the top of CLAUDE.md (adjust wording to the project, keep all four
rules intact):

```markdown
## Context Rules (context-guard)

> Managed by context-guard — keep these files within their limits.

- **CLAUDE.md** ≤ 300 lines. Anything not needed in context *every time* belongs
  in its own file, with a one-line pointer left behind (`> For X, see docs/x.md`).
- **CLAUDE.md** must always include this rules block (near the top) and the team
  behavioral guidelines block.
- **MEMORY.md** is optional; it exists only if something must persist across
  sessions. Ceiling 150 lines. When over, the oldest entries are condensed (1–2
  lines) and their full text lives in **ARCHIVE.md**, with a pointer left behind.
- **AGENTS.md** and **.cursor/rules/*.md** stay in sync with CLAUDE.md where
  byte-identical sync is mandated.
```
