---
name: context-fix
description: >-
  Apply fixes to AI context files (CLAUDE.md, MEMORY.md, ARCHIVE.md, AGENTS.md,
  .cursor/rules/*) so they meet the fixed size/structure rules. USE THIS when
  the user explicitly wants the fixes APPLIED, not just a report: "fix my
  CLAUDE.md", "trim it", "split this file", "archive the old memory", "clean up
  memory", "make it compliant", "bring it under the limit", or "apply the plan
  from context-audit". This skill builds the audit plan, shows exact diffs,
  REQUIRES human confirmation, then edits the working tree. It never commits or
  pushes — it leaves changes in the working tree for human review. For a
  read-only report with no edits, use context-audit instead.
---

# context-fix

Applies the context-guard rules to the working tree after explicit human
confirmation. **Never** run `git commit`, `git push`, or any destructive git
command — leave changes in the working tree for the human to review and commit.
Follow smallest-diff discipline: touch only what the rules require.

## The fixed rules

Canonical wording lives in `references/rules.md` (kept byte-identical with
context-audit). The fixed-rules stanza:

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

### Step 1 — Audit (build the plan)

Run the full audit (Steps 1–5 of the **context-audit** skill): locate &
measure, assess CLAUDE.md, assess MEMORY.md, verify the behavioral guidelines,
verify the rules block, and check AGENTS.md / `.cursor/rules` for drift. Count
logical lines with `awk 'END { print NR }'` (not `wc -l`) so a missing final
newline still counts. The output is a concrete plan: which sections to extract
where, which MEMORY entries to condense/archive, which blocks to insert, which
files to sync.

### Step 2 — Show plan + diffs, get confirmation

Present the plan and the **exact diffs** of every change (new files, pointer
insertions, condensed entries, archived text, inserted blocks). Then **stop and
require explicit human confirmation** before writing anything. No silent edits.

### Step 3 — Apply (on confirmation)

- **Trim CLAUDE.md:** create the new file(s) with the extracted section content,
  remove that content from CLAUDE.md, and insert a one-line pointer in its place
  (`> For X, see docs/x.md`). Extract least-frequently-needed first until
  CLAUDE.md ≤ 300 lines.
- **Condense MEMORY.md:** append the full original text of the oldest entries to
  **ARCHIVE.md** (create it if absent), replace those entries in MEMORY.md with
  a 1–2 line condensed version plus a pointer, until MEMORY.md ≤ 150 lines.
- **Insert rules block** near the top of CLAUDE.md if missing (template below).
- **Insert behavioral-guidelines block** if missing — copy it **verbatim** from
  this skill's `references/behavioral-guidelines.md` (everything below the `---`
  separator). Do not paraphrase.
- **Sync** AGENTS.md / `.cursor/rules/*.md` with CLAUDE.md where the project
  requires byte-identical sync.

### Step 4 — Re-measure & verify

Re-count every file and confirm each is within its ceiling. Report before/after
line counts and show the final diffs.

```bash
for f in CLAUDE.md MEMORY.md ARCHIVE.md AGENTS.md .cursor/rules/*.md; do
  [ -f "$f" ] && printf "%6s  %s\n" "$(awk 'END { print NR }' "$f")" "$f"
done
```

### Step 5 — Hand off

Leave all changes in the working tree for human review — do not stage and do not
commit. Tell the human what changed and that it's ready for their review and
commit. **Never** commit, push, or run destructive git commands.

## Rules-block template

Insert near the top of CLAUDE.md (adjust wording to the project, keep all four
rules intact). This is byte-identical to the template context-audit reports and
to `references/rules.md`:

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
