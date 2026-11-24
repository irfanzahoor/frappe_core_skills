# Changelog

All notable changes to this project are documented here.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

## [2.2.1] - 2026-11-24

### Fixed

- New fields no longer break form layout: added `references/field-layout.md` (Section/Column/Tab Break rules, field order, `insert_after`, child-table grid columns) and layout checks in the review checklist. `frappe-core` and `frappe-ui` now point to it.


## [2.2.0] - 2026-11-24

### Fixed

- LICENSE copyright corrected to Muhammad Irfaan (was NexTash).

### Added

- New agents: `frappe-reviewer` (code review) and `frappe-migrator` (upgrades).
- New slash commands: `/frappe-review` and `/frappe-upgrade-check`.
- New skills: `frappe-permissions`, `frappe-scheduler`, `frappe-erpnext-modules`.
- CI quality workflow (`.github/workflows/quality.yml`) validating skill frontmatter, JSON and README links.
- `CONTRIBUTING.md` with skill and release rules.
- README v14/v15/v16 compatibility table.

## [2.1.0] - 2026-11-24

### Added

- Plugin marketplace install (`claude plugin marketplace add irfanzahoor/frappe_core_skills`).
- `templates/CLAUDE.md` project template with per-task branch routing.
- `skills/frappe-debug/references/common-errors.md` with debug workflow and worked fixes.
- README rewrite with badges, install options and credits.

## [2.0.0] - 2026-11-24

### Added

- Initial branched release: 14 skills (`frappe-core` plus 13 focused branches).
- `AGENTS.md` with the same rules for non-Claude assistants (Codex, Cursor, Gemini, Copilot).
- MIT license.
