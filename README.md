# frappe-best-skill

The best parts of **frappe-code-standards** (code placement, standard APIs) and
**Frappe_Claude_Skill_Package** (Server Script sandbox, v14-v16 changes, errors, testing, ops),
split into focused **branches**. Each task loads only its own branch.

| Skill | Use for |
|---|---|
| `frappe-core` | All Frappe coding: placement, controllers, JS, hooks, permissions, review |
| `frappe-server-scripts` | Server Scripts in the UI (sandbox, no imports) |
| `frappe-reports-print` | Script/Query reports, print formats, Jinja |
| `frappe-performance` | Slow code, N+1, caching, enqueue |
| `frappe-upgrade` | v14 -> v15 -> v16 API changes, upgrades |
| `frappe-debug` | Error -> cause -> fix |
| `frappe-testing` | Unit/integration tests, CI |
| `frappe-ops` | bench, deploy, backup/restore, Frappe Cloud |
| `frappe-architect` | Plan features/apps, data model, customize vs build |
| `frappe-workflow` | Workflows, approvals |
| `frappe-notifications` | Email, Notification rules, realtime |
| `frappe-integrations` | REST API, webhooks, OAuth, Data Import |
| `frappe-website` | Website, portal, Web Forms |
| `frappe-ui` | Workspaces, dashboards, Pages, dialogs, Vue SPA |

**Tokens:** at session start only 14 one-line descriptions load. A skill's body loads only when
its task comes up.

## Install
- **Claude Code**: copy all folders inside `skills/` to `~/.claude/skills/` (or `.claude/skills/` in a project).
  To install a subset, keep `frappe-core` and add the branches you need.
- **Codex / Cursor / Gemini / Copilot**: copy the folder into the repo and keep `AGENTS.md` at the root.
  Or copy or symlink it as `CLAUDE.md`, `GEMINI.md`, `.cursor/rules/frappe.md` or `.github/copilot-instructions.md`.
- **Claude.ai Projects**: upload `frappe-core/SKILL.md` plus the branches you need as knowledge.