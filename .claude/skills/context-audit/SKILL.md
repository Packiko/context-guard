---
name: context-audit
description: >-
  Audit and report on AI context files (CLAUDE.md, MEMORY.md, ARCHIVE.md,
  AGENTS.md, .cursor/rules/*) against fixed size/structure rules. USE THIS
  whenever the user asks to check, audit, lint, review, or verify these files;
  asks whether CLAUDE.md or MEMORY.md is "too long", "too big", "over the
  limit", or "bloated"; asks "is my CLAUDE.md too big?", "is my context file
  ok?", or "are my context files compliant?"; wants to verify the team
  behavioral guidelines are present; or wants to confirm AGENTS.md /
  .cursor/rules are in sync with CLAUDE.md. READ-ONLY: this skill only reports
  findings and a fix plan — it never edits, creates, or deletes any file and
  never runs git mutations. To APPLY fixes (fix / trim / split / archive /
  make-compliant / "clean up memory"), use context-fix instead.
---

# context-audit

Report-and-plan only. You **MUST NOT** edit, create, or delete any file, and
**MUST NOT** run `git add/commit/push/rm/reset/checkout` or any other mutating
command. You only read, measure, and hand a human-applyable plan to the user.
Everything below describes what *would* change in the plan — you never carry it
out. If the user wants the changes applied, point them to the **context-fix**
skill.

## The fixed rules

Canonical wording lives in `references/rules.md` (kept byte-identical with
context-fix). The fixed-rules stanza:

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

## Workflow

### Step 1 — Locate & measure

Find the context files (check repo root first, then common locations). Count
logical lines and compare to the ceiling. Use `awk 'END { print NR }'` rather
than `wc -l` so a missing final newline still counts as a line.

```bash
for f in CLAUDE.md MEMORY.md ARCHIVE.md AGENTS.md .cursor/rules/*.md; do
  [ -f "$f" ] && printf "%6s  %s\n" "$(awk 'END { print NR }' "$f")" "$f" || echo "  MISSING  $f"
done
```

Note which files exist, which are missing, and each line count (compare against
300 for CLAUDE.md and 150 for MEMORY.md).

### Step 2 — Assess CLAUDE.md

If CLAUDE.md ≤ 300, it passes on size — note it and continue. If > 300:

- Read the file and break it into its major sections.
- For each section ask: **"Does the team/agent need this in context EVERY
  time?"** Needed-every-time content stays inline (the rules block and the
  behavioral guidelines always stay inline).
- For the rest, report that it **would be moved** (in the plan) to a separate
  file with a one-line pointer left in its place. Order the extractions
  **least-frequently-needed first**, until the projected line count is ≤ 300.
- Show the **projected line count** after the proposed extractions, and include
  the exact pointer text in the plan.

### Step 3 — Assess MEMORY.md

- **Absent:** confirm whether anything actually needs remembering across
  sessions. If not, recommend leaving it absent (do not propose inventing a
  file).
- **> 150 lines:** identify the oldest entries, propose a condensed 1–2 line
  replacement for each, and report that the full original text **would be moved**
  (in the plan) to **ARCHIVE.md** — noting it **would need to be created** if
  absent — with a pointer left in MEMORY.md. Show the **projected line count**.

### Step 4 — Verify behavioral guidelines

Confirm CLAUDE.md contains the four sections from
`references/behavioral-guidelines.md`: **1. Think Before Coding**,
**2. Simplicity First**, **3. Surgical Changes**, **4. Goal-Driven Execution**
(plus the Tradeoff note and the closing "These guidelines are working if…"
line). Report any that are missing or altered.

### Step 5 — Verify rules block & sync

- Confirm a **rules block** is present near the top of CLAUDE.md. The canonical
  template (identical to context-fix) is:

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

  If it is missing, report that this block **would be added** (in the plan) near
  the top of CLAUDE.md, and include the block text above in the plan for a human
  to add.
- Compare CLAUDE.md ↔ AGENTS.md ↔ `.cursor/rules/*.md` for drift where the
  project requires them in sync. Report differences (a `diff` of the relevant
  blocks is fine).

### Step 6 — Report

Produce:

1. A summary table:

   | File | Lines | Ceiling | Status |
   |------|------:|--------:|--------|
   | CLAUDE.md | … | 300 | PASS / OVER / MISSING |
   | MEMORY.md | … | 150 | PASS / OVER / MISSING / N/A |
   | ARCHIVE.md | … | — | present / absent |
   | AGENTS.md | … | — | in sync / DRIFT / MISSING |

2. A findings list (what's wrong and why it matters).
3. A concrete, ordered **plan** a human (or context-fix) can apply directly:
   which sections would be extracted to which files, the exact pointer lines to
   add, which MEMORY entries would be condensed/archived, and any block that
   would be added.

Stop after the report. You apply nothing.
