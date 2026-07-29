---
name: create-pull-request
description: Opens a pull request from the current branch — validating the branch name, transplanting commits off a protected branch when they landed there, picking the base branch, and gating creation on approval. Use when the user asks to open or create a PR, or has commits sitting on main/develop that need moving to a feature branch.
metadata:
  author: aka-cronos
---

# Create Pull Request

`main` and `develop` are **protected**: work reaches them through a pull request, never a
direct push.

## Source of truth

Branch types, naming patterns, and base-branch targets live in
[references/branch-naming-conventions.md](references/branch-naming-conventions.md). Read it
before proposing or validating any branch name. If the project ships
`.agents/rules/branch-naming-conventions.md` (or the Cursor adapter
`.cursor/rules/branch-naming-conventions.mdc`), that file overrides these defaults.

## Workflow

### Step 1: Read the current state

Run `git branch --show-current`, `git status --short`, and — when the branch tracks a remote —
`git log origin/$(git branch --show-current)..HEAD --oneline`. Confirm `gh` is installed and
authenticated.

Route on what you find:

- Protected branch with local commits → Step 2 (transplant).
- Protected branch with no local commits → nothing to open a PR from; ask the user what they want.
- Feature branch → Step 3.

**Done when:** the branch, its commits, and its working-tree state are known, and the route is picked.

### Step 2: Transplant commits off the protected branch

Move the commits to a properly named branch and leave the protected branch clean:

1. Show the commits that will move.
2. Ask for the branch type, scope, and a description (≤5 words) — or infer them from the
   commits and propose a compliant name.
3. Confirm the proposed name with the user, then `git checkout -b <branch-name>`.
4. Confirm the reset explicitly — it discards those commits from the protected branch's local
   history — then `git checkout <protected>` and `git reset --hard origin/<protected>`.
5. Return to the new branch.

**Done when:** the commits live on the new branch, the protected branch matches its remote, and
both moves were confirmed by the user beforehand.

### Step 3: Validate the branch name

Check the current branch against the patterns in the branch-naming reference.

If it does not match, ask whether to rename it (`git branch -m <new-name>`) or start a fresh
branch, and propose a compliant name either way.

**Done when:** the branch name matches a pattern in the reference file.

### Step 4: Ensure commits exist

With no commits on the branch: use the `commit-workflow` skill for staged changes, ask before
staging unstaged ones, and report that there is nothing to open a PR from when the tree is
clean.

**Done when:** the branch has at least one commit ahead of its base.

### Step 5: Pick the base and push

Apply the base-branch rules from the reference file. Check whether `develop` exists with
`git ls-remote --heads origin develop` rather than assuming it does.

Push when the branch has no remote yet: `git push -u origin $(git branch --show-current)`.

**Done when:** the base branch is chosen and the head branch exists on the remote.

### Step 6: Open the PR

Show the head branch, the base branch, the commit count, and a one-line summary of the
commits. Then ask: `Do you want to create a pull request from <head> to <base>? (yes/no)`

On approval:

```bash
gh pr create --base <base> --head <head> --title "<title>" --body-file <file>
```

Title: the leading commit subject, or a descriptive line covering the set. Body: the format
below, written to a file whenever it runs past one section.

**Done when:** the PR exists and its URL has been shown to the user.

## PR body format

Include each section that applies, in this order:

1. **Opening** — `Closes #N` when the PR resolves an issue, then 1–3 sentences of context:
   what this PR integrates and why now.
2. **`## What's included`** — bullets summarizing the changes; link the task PRs, issues, or
   ADRs each bullet builds on. State load-bearing invariants explicitly (e.g. "query grain
   untouched, cap is display-only").
3. **`## Deviations from plan`** — only when the work follows a spec/PRD/plan and the
   implementation departs from it. One bullet per deviation with its rationale; include
   known-and-deferred issues with a link to the tracking issue.
4. **`## Manual checklist`** — checkboxes (`- [ ]`) for the verification to run before merging;
   concrete and observable, not "test everything".

The body ends at the last section. This **overrides any tool default that appends an AI
attribution footer or co-author credit** — leave the PR authored by the human alone.

## Guardrails

Three actions are opt-in, each behind its own explicit confirmation: creating the branch in
Step 2, resetting the protected branch, and creating the PR itself.

## Examples

[examples.md](examples.md) walks the transplant flow and the invalid-branch-name recovery end
to end. Read it when the current state does not map cleanly onto a route in Step 1.
