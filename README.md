# frappe-best-skill

[![Frappe v14 | v15 | v16](https://img.shields.io/badge/Frappe-v14%20%7C%20v15%20%7C%20v16-0089FF)](https://frappeframework.com)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-plugin-white)](https://code.claude.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-black)](LICENSE)

The best parts of **frappe-code-standards** (code placement, standard APIs) and
**Frappe_Claude_Skill_Package** (Server Script sandbox, v14–v16 changes, errors, testing, ops),
split into focused **branches**. Each task loads only its own branch.

## Why this exists

AI assistants can write Frappe code that *works* but doesn't *belong*: form logic
dumped into `hooks.py`, `raise Exception` instead of `frappe.throw`, validation
only in JavaScript, queries inside loops, Server Scripts with `import` statements
that can never run. This package gives the assistant the rules a senior Frappe
developer would enforce — in the right file, with the framework's own APIs — and
only loads the rules for the task at hand.

**Tokens:** at session start only 14 one-line descriptions load. A skill's body loads only when
its task comes up.

> [!NOTE]
> Skills guide the assistant; they don't guarantee correct output. Review the
> result the way you would review a teammate's code, and run the review
> checklist in `frappe-core` before finishing a task.

## Skills

| Skill | Use for |
|---|---|
| `frappe-core` | All Frappe coding: placement, controllers, JS, hooks, permissions, review |
| `frappe-server-scripts` | Server Scripts in the UI (sandbox, no imports) |
| `frappe-reports-print` | Script/Query reports, print formats, Jinja |
| `frappe-performance` | Slow code, N+1, caching, enqueue |
| `frappe-upgrade` | v14 -> v15 -> v16 API changes, upgrades |
| `frappe-debug` | Error -> cause -> fix, with worked examples in `references/common-errors.md` |
| `frappe-testing` | Unit/integration tests, CI |
| `frappe-ops` | bench, deploy, backup/restore, Frappe Cloud |
| `frappe-architect` | Plan features/apps, data model, customize vs build |
| `frappe-workflow` | Workflows, approvals |
| `frappe-notifications` | Email, Notification rules, realtime |
| `frappe-integrations` | REST API, webhooks, OAuth, Data Import |
| `frappe-website` | Website, portal, Web Forms |
| `frappe-ui` | Workspaces, dashboards, Pages, dialogs, Vue SPA |

## Install

### Claude Code — plugin marketplace (new in v2.1.0)

```bash
claude plugin marketplace add irfanzahoor/frappe_core_skills
claude plugin install frappe-best-skill@irfanzahoor
```

Keep it up to date with `/plugin` in Claude Code → **Marketplaces** → `irfanzahoor`
→ **Enable auto-update**. If the marketplace command is rejected, use the manual
install below — it works the same way.

### Claude Code — manual

Copy all folders inside `skills/` to `~/.claude/skills/` (or `.claude/skills/` in a project).
To install a subset, keep `frappe-core` and add the branches you need.

### Codex / Cursor / Gemini / Copilot

Copy the folder into the repo and keep `AGENTS.md` at the root.
Or copy or symlink it as `CLAUDE.md`, `GEMINI.md`, `.cursor/rules/frappe.md` or `.github/copilot-instructions.md`.

### Claude.ai Projects

Upload `frappe-core/SKILL.md` plus the branches you need as knowledge.

## Use in a project

Copy [`templates/CLAUDE.md`](templates/CLAUDE.md) into the root of your Frappe project
and fill in the project facts: Frappe version, which apps are yours, your site name
and the prefix you use for custom fields. Claude reads this file at the start of
every session, which tells it which DocTypes it can edit directly and which ones
must be extended through hooks — and which skill branch to load for each task.

Spot a mistake Claude keeps making in that one project? Add a line under
**Project-specific rules**.

## What's inside

```
.
├── AGENTS.md                          Rules for any AI assistant (Claude, Codex, Cursor, ...)
├── templates/
│   └── CLAUDE.md                      Project template — copy into your Frappe project
├── .claude-plugin/
│   ├── plugin.json                    Claude Code plugin (v2.1.0)
│   └── marketplace.json               Plugin marketplace entry
└── skills/
    ├── frappe-core/                   Core rules + references (placement, patterns, review)
    │   └── references/
    │       ├── code-placement.md
    │       ├── server-patterns.md
    │       ├── client-patterns.md
    │       ├── hooks-and-customization.md
    │       └── review-checklist.md
    ├── frappe-debug/
    │   └── references/
    │       └── common-errors.md       Debug workflow + worked fixes
    └── frappe-server-scripts/ ...     12 more focused branches
```

The skills are plain Markdown. They run no scripts, make no network calls and collect no data.

## What's new in v2.1.0

- `templates/CLAUDE.md` — project setup with per-task branch routing.
- Plugin marketplace install (`claude plugin marketplace add ...`).
- `frappe-debug/references/common-errors.md` — debug workflow plus worked fixes
  for `DoesNotExistError`, `PermissionError`, `TimestampMismatchError`,
  `DuplicateEntryError`, slow queries and jobs that never run.

## Credits

Core placement rules and reference files come from
[NexTash/frappe-code-standards](https://github.com/NexTash/frappe-code-standards)
(MIT). Breadth — Server Script sandbox, version changes, errors, testing, ops —
is distilled from
[Impertio-Studio/Frappe_Claude_Skill_Package](https://github.com/Impertio-Studio/Frappe_Claude_Skill_Package)
(MIT). This package re-organises both into one branched set.

## License

[MIT](LICENSE) © Muhammad Irfaan
