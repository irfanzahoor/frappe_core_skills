# Frappe / ERPNext rules for any AI coding assistant

This works with Claude, Codex, Cursor, Gemini, Copilot and others.

1. For any Frappe or ERPNext work, read `skills/frappe-core/SKILL.md` and follow it.
2. Then read **only** the branch that matches the task:

| Task | Read |
|---|---|
| Server Script (UI) | `skills/frappe-server-scripts/SKILL.md` |
| Reports, print formats, Jinja | `skills/frappe-reports-print/SKILL.md` |
| Slow code, caching, background jobs | `skills/frappe-performance/SKILL.md` |
| v14/v15/v16 differences, upgrades | `skills/frappe-upgrade/SKILL.md` |
| Errors, tracebacks | `skills/frappe-debug/SKILL.md` |
| Tests, CI | `skills/frappe-testing/SKILL.md` |
| bench, deploy, backup | `skills/frappe-ops/SKILL.md` |

Don't read branches the task doesn't need. That keeps token use low.