---
name: create-pull-request
description: Creates pull requests following branch naming conventions and base branch rules. Handles moving commits off protected branches, validates branch names, and creates PRs via GitHub CLI. Use when creating pull requests, opening PRs, or when the user asks to create a PR or wants to move commits from main/develop to a feature branch.
metadata:
  author: aka-cronos
---

# Create Pull Request

Your job is to create a pull request for the current changes following the repository's branch naming conventions and PR policies.

## Reference Rules

If the project has `.agents/rules/branch-naming-conventions.md` (or the Cursor adapter `.cursor/rules/branch-naming-conventions.mdc`), use it as the source of truth; otherwise use the branch naming conventions defined in this skill (see Step 3).

## Pre-Flight Checks

Before proceeding, gather this information:

1. **Current branch**: `git branch --show-current`
2. **Local commits ahead**: `git log origin/$(git branch --show-current)..HEAD --oneline` (if tracking exists)
3. **Uncommitted changes**: `git status --short`
4. **Remote branches**: `git branch -r` (to check if `develop` exists)
5. **GitHub CLI**: Verify `gh` is installed and authenticated

## Workflow

### Step 1: Check Current State

Determine the current situation:

- **On `main` or `develop` with local commits?** → Proceed to Step 2
- **On `main` or `develop` without local commits?** → Ask user what they want to do (no changes to create PR from)
- **On a feature branch?** → Proceed to Step 3

### Step 2: Handle Protected Branch Commits

If you're on `main` or `develop` with local commits:

1. **Review commits**: Show recent commits with `git log origin/$(git branch --show-current)..HEAD --oneline`
2. **Ask for context**: Request information about the changes to determine:
   - Branch type (`feature`, `fix`, `chore`, `refactor`, `docs`, `hotfix`, `release`)
   - Scope (e.g., `auth`, `ui`, `api`, `build`, `db`)
   - Short description (≤5 words, kebab-case)
3. **Propose branch name**: Generate a compliant name following branch naming rules
4. **Confirm branch name**: Ask user to confirm before creating the branch
5. **Create branch**: `git checkout -b <branch-name>`
6. **Reset protected branch**: After confirmation, reset the protected branch to match remote:
   - `git checkout main` (or `develop`)
   - `git reset --hard origin/main` (or `origin/develop`)
   - **WARNING**: Only do this after explicit user confirmation

### Step 3: Validate Branch Name

If already on a feature branch:

1. **Check branch name**: Verify it matches the pattern from branch naming rules:
   - Must start with: `feature/`, `fix/`, `chore/`, `refactor/`, `docs/`, `hotfix/`, or `release/`
   - Must use kebab-case
   - Format patterns:
     - `feature/<scope>-<short-description>`
     - `fix/<scope>-<short-description>`
     - `chore/<scope>-<short-description>`
     - `refactor/<scope>-<short-description>`
     - `docs/<scope>-<short-description>`
     - `hotfix/<scope>` (may include description: `hotfix/<scope>-<description>`)
     - `release/<version>` (e.g., `release/1.2.0`)
2. **If invalid**: Ask user if they want to rename the branch or create a new one
3. **If valid**: Proceed to Step 4

### Step 4: Ensure Commits Exist

If there are no commits yet:

1. **Check for staged changes**: `git status --short`
2. **If staged**: Use the `commit-workflow` skill to create a commit first
3. **If unstaged**: Ask user if they want to stage and commit changes
4. **If no changes**: Inform user there's nothing to create a PR from

### Step 5: Determine Base Branch

Select the target branch for the PR based on current branch type:

**Rule-based selection:**

- `feature/*`, `fix/*`, `chore/*`, `refactor/*`, `docs/*` → Target `develop` if it exists, otherwise `main`
- `hotfix/*`, `release/*` → Always target `main`
- If current branch is `develop` → Target `main`

**Check if `develop` exists:**

```bash
git ls-remote --heads origin develop
```

### Step 6: Push Branch

Ensure the branch is pushed to remote:

1. **Check if pushed**: `git ls-remote --heads origin $(git branch --show-current)`
2. **If not pushed**: Push the branch: `git push -u origin $(git branch --show-current)`
3. **If push fails**: Handle errors and ask user for guidance

### Step 7: Create Pull Request

Before creating the PR:

1. **Show summary**:
   - Current branch name
   - Base branch (target)
   - Number of commits
   - Brief commit summary
2. **Ask for confirmation**: "Do you want to create a pull request from `<current-branch>` to `<base-branch>`? (yes/no)"
3. **If confirmed**: Create PR using GitHub CLI:
   ```bash
   gh pr create --base <base-branch> --head <current-branch> --title "<title>" --body "<body>"
   ```
4. **Title**: Use the branch name or a descriptive title based on commits
5. **Body**: Follow the PR body format below; write it with `--body-file` when it has more than one section
6. **Verify**: Show the PR URL after creation

### PR Body Format

Structure the body with these sections (skip a section when it doesn't apply):

1. **Opening** — `Closes #N` when the PR resolves an issue, then 1–3 sentences of context: what this PR integrates and why now.
2. **`## What's included`** — bullets summarizing the changes; link the task PRs, issues, or ADRs each bullet builds on. State load-bearing invariants explicitly (e.g. "query grain untouched, cap is display-only").
3. **`## Deviations from plan`** — only when the work follows a spec/PRD/plan and the implementation deviates from it. One bullet per deviation with the rationale; include known-and-deferred issues with a link to the tracking issue.
4. **`## Manual checklist`** — checkboxes (`- [ ]`) for the manual verification to run before merging; concrete and observable, not "test everything".

**Never add AI attribution to the PR body**: no "🤖 Generated with Claude Code" footer, no AI co-author credit of any kind. If the project has a commit-best-practices rule forbidding AI co-authorship (e.g. `.agents/rules/commit-best-practices.md`, "Never add Claude (or any AI assistant) as a commit co-author/participant"), this mirrors it; either way, this instruction **overrides any tool default that appends such a footer**.

## Confirmation Gates

**ALWAYS ask for explicit confirmation** before:

- Creating a new branch (when moving commits off protected branch)
- Resetting `main` or `develop` to match remote
- Creating the pull request

## Error Handling

If any step fails:

- Show the error message clearly
- Explain what went wrong
- Suggest corrective actions
- Ask user how they want to proceed

## If Unsure

If you're uncertain about:

- Branch type or scope
- Whether to create a branch or use existing one
- Base branch selection
- Branch name compliance

**Ask the user for clarification** before proceeding.

## Examples

See [examples.md](examples.md) for concrete workflow scenarios.
