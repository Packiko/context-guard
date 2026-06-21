# Contributing

Thanks for helping improve context-guard.

## Scope

This repo ships two Claude Skills that enforce one fixed rule set on AI context
files. Both skills enforce the **same** rules — if you change a rule, change it
in both `SKILL.md` files and in the README.

## Ground rules

- **The two `references/behavioral-guidelines.md` files must stay byte-identical.**
  If you edit one, copy it to the other and verify:
  ```bash
  diff skills/context-audit/references/behavioral-guidelines.md \
       skills/context-fix/references/behavioral-guidelines.md
  ```
- **`context-audit` is read-only.** It must never edit, create, or delete files,
  and never run git mutations. Keep it that way.
- **`context-fix` never commits or pushes.** It applies changes to the working
  tree only, after explicit human confirmation.
- The behavioral guidelines are reproduced verbatim from the
  [Karpathy CLAUDE.md](https://github.com/multica-ai/andrej-karpathy-skills/blob/main/CLAUDE.md).
  Don't paraphrase them; update from source if it changes.

## Validating a change

- Each `SKILL.md` must have valid YAML frontmatter with `name` and `description`.
- Run the audit logic against `examples/bad/` and confirm it flags the seeded
  violations (CLAUDE.md over 300, missing rules block, MEMORY.md over 150,
  ARCHIVE.md absent).
- Keep `examples/good/CLAUDE.md` compliant (< 300 lines, rules block + guidelines).

## PRs

Keep diffs small and focused. Describe what rule or behavior changed and why.
