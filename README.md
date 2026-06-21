# context-guard

Two installable [Claude Skills](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview)
that keep your team's AI context files — `CLAUDE.md`, `MEMORY.md`, `ARCHIVE.md`,
`AGENTS.md`, `.cursor/rules/*` — within fixed size and structure rules.

## The problem

`CLAUDE.md` and friends quietly grow until they blow the context budget, bury
the important instructions, and drift out of sync with `AGENTS.md` and
`.cursor/rules`. context-guard gives you one fixed rule set and two skills to
enforce it: one that **reports**, one that **fixes**.

## The fixed rules

1. **CLAUDE.md ≤ 300 lines.** Anything not needed in context *every time* moves
   to its own file with a one-line pointer left behind (`> For X, see docs/x.md`).
2. **CLAUDE.md always contains** the team
   [**behavioral guidelines**](https://github.com/multica-ai/andrej-karpathy-skills)
   (four sections, see attribution below) plus a **rules block** near the top.
3. **MEMORY.md is optional**, ceiling **150 lines**. When over, the oldest
   entries are condensed to 1–2 lines and their full text moves to **ARCHIVE.md**
   (the long-term filing cabinet), leaving a pointer behind.
4. **AGENTS.md and .cursor/rules/\*** stay in sync with CLAUDE.md where the
   project mandates byte-identical sync. Drift is reported.

## The two skills

| Skill | What it does | Safe? |
|-------|--------------|-------|
| **context-audit** | Measures the files, checks them against the rules, and prints a summary table + findings + a concrete fix plan. | ✅ Read-only — never edits files or touches git. |
| **context-fix** | Builds the same plan, shows exact diffs, asks for confirmation, then applies the changes to the working tree. | ✍️ Edits files (with confirmation); never commits or pushes. |

**Use `context-audit`** to find out what's wrong. **Use `context-fix`** when you
want it fixed. context-fix always shows the plan and diffs and waits for your
explicit confirmation before writing anything, and it leaves changes in the
working tree for you to review and commit.

## Install

**Source of truth:** the canonical skills live under `skills/` (each is a
`SKILL.md` plus its `references/`). The repo also ships a ready-to-use copy
under `.claude/` (`.claude/skills/` + `.claude/commands/`) so anyone who clones
the repo gets the skills and the `/context-guard` command working immediately.
Edit `skills/`; the `.claude/skills/` copies are the installed mirror.

- **Claude Code:** if you cloned this repo, it already works — `.claude/skills/`
  and `.claude/commands/context-guard.md` are picked up automatically. To use the
  skills in another project, drop the folders into a skills dir Claude Code reads,
  e.g. `~/.claude/skills/` (personal) or `<repo>/.claude/skills/` (project):
  ```bash
  cp -r skills/context-audit skills/context-fix ~/.claude/skills/
  ```
- **Claude.ai:** Settings → Features → upload each skill as a zip
  (Pro/Max/Team/Enterprise with code execution). Zip each folder under `skills/`
  and upload `context-audit.zip` and `context-fix.zip` separately.

## Usage

Audit (read-only):

```
> is my CLAUDE.md too big?
> audit CLAUDE.md and MEMORY.md
> check that the behavioral guidelines and rules block are present
```

→ context-audit prints a table (file → lines → ceiling → PASS/OVER/MISSING),
the findings, and a plan.

Fix (applies changes, with confirmation):

```
> fix my CLAUDE.md, it's over the limit
> trim CLAUDE.md and archive the old memory
> apply the plan from context-audit
```

→ context-fix shows the plan and diffs, waits for your confirmation, applies the
edits, re-measures, and leaves changes in the working tree for you to review and
commit (it never stages, commits, or pushes).

You can also use the `/context-guard` command in Claude Code:

```
/context-guard audit    # report only (safe, default)
/context-guard fix      # apply changes, with confirmation
/context-guard          # bare → defaults to audit
```

## examples/

- `examples/good/CLAUDE.md` — a compliant file (< 300 lines, rules block +
  behavioral guidelines present).
- `examples/bad/` — a deliberately violating set used for testing:
  `CLAUDE.md` is over 300 lines and missing the rules block; `MEMORY.md` is over
  150 lines; `ARCHIVE.md` is absent. Run either skill against this folder to see
  it flag every violation.

## Attribution

The behavioral guidelines block (the four sections **Think Before Coding**,
**Simplicity First**, **Surgical Changes**, **Goal-Driven Execution**, the
Tradeoff note, and the closing line) is reproduced verbatim from the
[Karpathy CLAUDE.md](https://github.com/multica-ai/andrej-karpathy-skills/blob/main/CLAUDE.md).

## License

MIT — see [LICENSE](LICENSE).
