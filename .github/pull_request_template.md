## What changed

<!-- One change per PR. List the files and the rule or feature added. -->

## Why

<!-- The bad output Claude produced on a real project, and what the fix should be. -->

## Checklist

- [ ] PR is against the `develop` branch (not `main`).
- [ ] One change per PR.
- [ ] Every new `SKILL.md` has frontmatter with `name:` (kebab-case, matching the folder) and `description:` under 1024 characters, starting with what the skill is and a `Use when...` trigger.
- [ ] New skill added to the table in `README.md`, the routing table in `templates/CLAUDE.md`, and the list in `AGENTS.md`.
- [ ] Version bumped in `.claude-plugin/plugin.json` **and** `.claude-plugin/marketplace.json`, and an entry added to `CHANGELOG.md`.
- [ ] CI checks pass (frontmatter, JSON, links, markdown style — see `.github/workflows/quality.yml`).
- [ ] `claude plugin validate` run, if anything under `.claude-plugin/` changed.
- [ ] No other app's files (Frappe, ERPNext, HRMS) edited — extended through hooks and fixtures instead.
