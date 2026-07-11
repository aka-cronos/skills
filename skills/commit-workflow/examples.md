# Commit Workflow Examples

## Example 1: Mixed concerns (split recommended)

Input:

- `.agents/rules/commit-best-practices.md`
- `.cursor/settings.json`
- `docs/architecture.md`
- `docs/overview.md`

Suggested grouping:

- Group A: tooling/config (`.cursor/**`)
- Group B: docs (`docs/**`)

Suggested order:

1. tooling/config
2. docs

Draft messages:

1. `chore(cursor): add commit rules and workspace settings`
2. `docs: add architecture and overview documentation`

Prompt:

`I detected multiple logical concerns and recommend split commits. Do you want to proceed with split commits or keep a single commit?`

If split is chosen, confirm each commit separately:

- `Do you want to proceed with commit 1/2? (yes/no)`
- `Do you want to proceed with commit 2/2? (yes/no)`

## Example 2: Single concern (single commit flow)

Input:

- `docs/architecture-decisions.md`
- `docs/overview.md`

Suggested message:

`docs: update architecture decisions and overview`

Body:

`Clarify architecture rationale and align overview terminology.`

Prompt:

`Do you want to proceed with this commit? (yes/no)`

## Example 3: Check result reporting

Checks:

- `pnpm lint`: passed
- `pnpm typecheck`: passed
- `pnpm format`: script not found
- `pnpm test`: script not found
- `pnpm build`: not run yet

Recommended response:

`Lint and type check passed. format and test scripts are not defined in package.json.`

Then ask:

`Do you want to proceed with the commit anyway? (yes/no)`
