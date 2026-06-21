---
description: Audit or fix the repo's AI context files (CLAUDE.md, MEMORY.md, etc.) against the context-guard rules. Thin router over the context-audit and context-fix skills.
argument-hint: "[audit|fix]"
---

You are routing a `/context-guard` request. Treat `$ARGUMENTS` as a single
free-form string and interpret the user's intent from it — do not parse
positional arguments in shell.

Decide the route:

- If `$ARGUMENTS` indicates **audit / check / report / verify / lint / review**
  (or is empty or ambiguous), run the **context-audit** skill workflow: locate &
  measure the context files, assess them against the fixed rules, and produce the
  report + plan only. Make **no edits** and run no git mutations.
- If `$ARGUMENTS` indicates **fix / apply / trim / split / archive /
  make-compliant / "clean up memory" / "apply the plan"**, run the
  **context-fix** skill workflow: build the plan, show the exact diffs, and
  **require explicit human confirmation before writing anything**. Never commit
  or push; leave changes in the working tree for human review.
- If the intent is unclear, default to **context-audit** (the safe, read-only
  path) and say so explicitly before proceeding.

This command is only a router — the canonical logic lives in the `context-audit`
and `context-fix` skills. Invoke the appropriate skill and follow its workflow.

User request: $ARGUMENTS
