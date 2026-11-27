---
description: Review Frappe/ERPNext code against the frappe-code-review skill (Sensei's 79 checks) and output a findings table.
---

Review the Frappe/ERPNext code at `$ARGUMENTS`.

- If `$ARGUMENTS` is empty, review the files changed in git (`git diff --name-only` and `git status --short`), not the whole repo.
- If `$ARGUMENTS` is a file, review that file. If it is a folder, review the Python and JS files inside it.

## How to review

1. Load `skills/frappe-code-review/SKILL.md` and follow its 79-rule
   checklist (FR-001–FR-082; canonical rules in
   `irfanzahoor/frappe-sensei`). If a deterministic result is needed, run
   the Sensei checker as the skill describes and use its findings as the base.
2. For Server Scripts, also load `skills/frappe-server-scripts/SKILL.md`;
   for anything version-specific, also load `skills/frappe-upgrade/SKILL.md`.
3. Check, in order: code placement (own DocType files vs hooks/fixtures for
   other apps), standard APIs (`frappe.throw`, `_()` translations,
   `frappe.qb`, `frm.set_query` in `setup`), server-side validation (never
   JS-only), permissions in whitelisted methods, no queries in loops, no
   `["*"]` fields, no `frappe.db.commit()` in controllers or hooks.

## Output

A findings table, ordered by severity, with the Sensei rule ID for every
finding:

| Severity | File:line | Rule broken | Suggested fix |
|---|---|---|---|
| blocker / major / minor | ... | FR-0xx — title | ... |

End with a one-line verdict — approve, approve with fixes, or needs rework
(any blocker means needs rework, else any major means approve with fixes) —
and the commands to run (`migrate`, `bench build --app <app>`, `clear-cache`)
if the change needs them. Read code only; do not edit files unless the user
asks for a fix.
