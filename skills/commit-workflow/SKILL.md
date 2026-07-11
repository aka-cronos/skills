---
name: commit-workflow
description: Analyzes staged git changes, proposes Conventional Commit messages, and runs a confirmation-first commit workflow. Defaults to split-commit recommendations when staged files span multiple logical concerns. Use when the user asks to commit, write a commit message, review staged changes, or split commits.
metadata:
  author: aka-cronos
---

# Commit Workflow

Authoritative workflow for preparing commits with Conventional Commits, split-first behavior, and explicit user confirmation.

## Source of truth

- If the project has `.agents/rules/commit-best-practices.md` (or the Cursor adapter `.cursor/rules/commit-best-practices.mdc`), follow it for commit policy; otherwise use the defaults in this skill.
- Use this skill for process and decision flow.

## Use when

Use this skill when at least one applies:

- User asks to commit staged changes.
- User asks for a commit message based on staged diff.
- User asks whether changes should be split across commits.
- User runs `/commit` or asks for a commit workflow.

## Workflow

### Step 1: Inspect staged state

Run:

- `git status --short`
- `git diff --cached --name-only`
- `git diff --cached --stat`

If no staged changes exist, report it and stop.

### Step 2: Detect logical concerns

Classify staged files into concern groups, for example:

- docs (`docs/**`, `README*`)
- tooling/config (`.cursor/**`, `.agents/**`, `.github/**`, config files)
- app code (`apps/**`, `src/**`, `app/**`, `components/**`)
- packages (`packages/**`)
- backend/data (`**/convex/**`, `db/**`, migrations)
- tests (`**/*.test.*`, `tests/**`)

Use best judgment if files do not match these examples.

### Step 3: Split-first decision

If more than one concern group is present:

1. Recommend split commits first.
2. Propose group boundaries and commit order.
3. Draft one message per proposed commit.
4. Ask the user to choose:
  - split now, or
  - proceed as one commit.

If only one concern group is present, continue with single-commit flow.

### Step 4: Draft commit message(s)

For each commit candidate:

- Subject format: `type(scope): short summary`
- Subject <= 72 characters
- Imperative mood, lowercase
- Body optional but recommended when why is not obvious
- When including a body, if the project has `.agents/rules/commit-best-practices.md`,
  follow it; otherwise use these defaults: only hyphen-space markdown bullets (even
  for one item); <= 72 characters per line with indented wraps as needed; never
  prose paragraphs or naked sentences without bullets

If uncertain about type, scope, or split boundaries, ask concise clarification.

### Step 5: Run quality checks

Prefer project scripts and only run checks that exist:

- `pnpm lint`
- `pnpm typecheck`
- `pnpm format` (if script exists)
- `pnpm test` (if script exists)
- `pnpm build` (if script exists)

If checks fail or a script does not exist, report status clearly and ask whether to proceed.

### Step 6: Confirmation gate (required)

Before each commit, always show:

1. Full commit message
2. Commit preview (`git diff --cached --stat` or split-specific preview)
3. Explicit prompt: `Do you want to proceed with this commit? (yes/no)`

Do not run `git commit` without explicit approval.

### Step 7: Commit and verify

After explicit approval:

1. Create commit.
2. Verify with `git log -1 --oneline`.
3. Show details with `git show --stat HEAD`.

For split flow, repeat confirmation and verification for each commit in sequence.

## Guardrails

- Never commit likely secrets (`.env*`, credentials, token files).
- Never run destructive git commands unless explicitly requested.
- Never amend a commit unless explicitly requested.
- Do not force a split; recommend it and let user decide.

## Output format

Use this structure when proposing:

1. Staged summary
2. Concern grouping
3. Recommended path (split or single)
4. Draft message(s)
5. Check status
6. Confirmation question

## Additional resources

- Examples: [examples.md](examples.md)