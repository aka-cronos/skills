# Commit Workflow Examples

## Example 1: Mixed concerns (split recommended)

Staged:

- `.agents/rules/commit-best-practices.md`
- `.cursor/settings.json`
- `docs/architecture.md`
- `docs/overview.md`

Grouping, in commit order:

1. tooling/config (`.agents/**`, `.cursor/**`) → `chore(rules): add commit rules and workspace settings`
2. docs (`docs/**`) → `docs: add architecture and overview documentation`

Prompt:

`I found two concerns and recommend an atomic commit for each. Split, or keep a single commit?`

If split, gate each one separately:

- `Do you want to proceed with commit 1/2? (yes/no)`
- `Do you want to proceed with commit 2/2? (yes/no)`

## Example 2: Single concern (single-commit flow)

Staged:

- `docs/architecture-decisions.md`
- `docs/overview.md`

Message — note the body is a bullet list, never prose:

```
docs: update architecture decisions and overview

- Clarify the rationale behind the current architecture.
- Align overview terminology with the decision records.
```

Prompt:

`Do you want to proceed with this commit? (yes/no)`
