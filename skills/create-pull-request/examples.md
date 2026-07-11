# PR Workflow Examples

Concrete scenarios demonstrating the PR creation workflow.

## Example 1: Commits on Protected Branch (main)

**Situation:**

- Currently on `main`
- 2 local commits ahead of `origin/main`
- Commits: "feat(auth): add login form" and "feat(auth): add validation"

**Workflow:**

1. Detect: On `main` with local commits
2. Review commits: Show commit history
3. Ask context: "What type of changes are these? (feature/fix/chore/etc.)"
4. User responds: "feature for authentication"
5. Propose: "I'll create branch `feature/auth-login-form`. Proceed? (yes/no)"
6. User confirms: "yes"
7. Create branch: `git checkout -b feature/auth-login-form`
8. Confirm reset: "Reset `main` to match `origin/main`? This will discard local commits on main. (yes/no)"
9. User confirms: "yes"
10. Reset: `git checkout main && git reset --hard origin/main`
11. Checkout feature branch: `git checkout feature/auth-login-form`
12. Push: `git push -u origin feature/auth-login-form`
13. Determine base: Check if `develop` exists → Yes → Target `develop`
14. Confirm PR: "Create PR from `feature/auth-login-form` to `develop`? (yes/no)"
15. Create PR: `gh pr create --base develop --head feature/auth-login-form --title "feat(auth): add login form" --body-file pr-body.md`, where `pr-body.md` follows the PR Body Format from SKILL.md:

    ```markdown
    Adds the login form as the entry point for the auth flow.

    ## What's included

    - Login form with client-side validation (builds on #12)

    ## Manual checklist

    - [ ] Invalid credentials show the inline error, not a redirect
    - [ ] Successful login lands on the dashboard
    ```

    (No `## Deviations from plan` here — there was no spec to deviate from. Never append AI attribution footers.)

## Example 2: Valid Feature Branch (develop exists)

**Situation:**

- Currently on `feature/ui-button-component`
- Branch follows naming convention
- 3 commits ready
- Branch already pushed to remote
- `develop` branch exists

**Workflow:**

1. Detect: On feature branch
2. Validate name: ✓ Matches `feature/<scope>-<description>` pattern
3. Check commits: Already has commits
4. Check push status: Already pushed
5. Determine base: `develop` exists → Target `develop`
6. Show summary:
   - Branch: `feature/ui-button-component`
   - Base: `develop`
   - Commits: 3
7. Confirm PR: "Create PR from `feature/ui-button-component` to `develop`? (yes/no)"
8. User confirms: "yes"
9. Create PR: `gh pr create --base develop --head feature/ui-button-component`

## Example 3: Feature Branch (develop doesn't exist)

**Situation:**

- Currently on `fix/api-error-handling`
- Branch follows naming convention
- `develop` branch does NOT exist
- Repository only has `main`

**Workflow:**

1. Detect: On feature branch
2. Validate name: ✓ Matches `fix/<scope>-<description>` pattern
3. Check for develop: `git ls-remote --heads origin develop` → Not found
4. Determine base: No `develop` → Target `main`
5. Confirm PR: "Create PR from `fix/api-error-handling` to `main`? (yes/no)"
6. Create PR: `gh pr create --base main --head fix/api-error-handling`

## Example 4: Hotfix Branch

**Situation:**

- Currently on `hotfix/critical-auth-bug`
- Branch follows naming convention
- `develop` exists but hotfix targets `main` per rules

**Workflow:**

1. Detect: On hotfix branch
2. Validate name: ✓ Matches `hotfix/<scope>` pattern
3. Determine base: Hotfix type → Always target `main` (regardless of develop existence)
4. Confirm PR: "Create PR from `hotfix/critical-auth-bug` to `main`? (yes/no)"
5. Create PR: `gh pr create --base main --head hotfix/critical-auth-bug`

## Example 5: On Develop Branch

**Situation:**

- Currently on `develop`
- Local commits ahead of `origin/develop`
- Want to create PR to `main`

**Workflow:**

1. Detect: On `develop` branch
2. Check commits: Has local commits
3. Determine base: On `develop` → Target `main` (special rule)
4. Push if needed: `git push -u origin develop`
5. Confirm PR: "Create PR from `develop` to `main`? (yes/no)"
6. Create PR: `gh pr create --base main --head develop`

## Example 6: Invalid Branch Name

**Situation:**

- Currently on `my-feature-branch`
- Branch name doesn't follow conventions
- Has commits ready

**Workflow:**

1. Detect: On feature branch
2. Validate name: ✗ Doesn't match required pattern
3. Inform user: "Branch name `my-feature-branch` doesn't follow naming conventions. It should be `feature/<scope>-<description>`"
4. Ask: "Do you want to rename this branch or create a new one? (rename/new)"
5. If rename: `git branch -m feature/<scope>-<description>`
6. If new: Follow Example 1 workflow to create properly named branch
7. Continue with PR creation

## Example 7: No Commits Yet

**Situation:**

- Currently on `feature/new-dashboard`
- Branch name is valid
- Has unstaged changes but no commits

**Workflow:**

1. Detect: On feature branch
2. Validate name: ✓ Valid
3. Check commits: No commits yet
4. Check status: Has unstaged changes
5. Ask: "No commits yet. Do you want to stage and commit these changes first? (yes/no)"
6. If yes: Use commit-workflow skill to create commit
7. Then proceed with PR creation

## Example 8: Commits on Develop (Moving to Feature Branch)

**Situation:**

- Currently on `develop`
- 1 local commit: "chore(deps): update dependencies"
- Want to move to proper branch

**Workflow:**

1. Detect: On `develop` with local commits
2. Review commit: Show "chore(deps): update dependencies"
3. Ask context: "This looks like a dependency update. Should I create `chore/deps-update-dependencies`? (yes/no)"
4. User confirms: "yes"
5. Create branch: `git checkout -b chore/deps-update-dependencies`
6. Confirm reset: "Reset `develop` to match `origin/develop`? (yes/no)"
7. User confirms: "yes"
8. Reset: `git checkout develop && git reset --hard origin/develop`
9. Checkout feature branch: `git checkout chore/deps-update-dependencies`
10. Push: `git push -u origin chore/deps-update-dependencies`
11. Determine base: `chore/*` → Target `develop` (if exists) or `main`
12. Create PR: `gh pr create --base develop --head chore/deps-update-dependencies`
