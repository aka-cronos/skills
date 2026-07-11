# Branch Types and Naming

## Enforce

- The default branch is `main`.
- Never push directly to `main`; create a branch and open a PR.
- `develop` is optional as an integration branch for team workflows.

## Branch Types

- `feature/<scope>-<short-description>`
  - Example: `feature/auth-signin-form`
- `fix/<scope>-<issue-or-bug>`
  - Example: `fix/build-vercel-edge-runtime`
- `chore/<tool>-<action>`
  - Example: `chore/eslint-upgrade`
- `refactor/<scope>-<what>`
  - Example: `refactor/ui-theme-provider`
- `docs/<scope>`
  - Example: `docs/readme-setup`
- `release/<version>`
  - Example: `release/1.2.0`
- `hotfix/<scope>` (PR to `main`)
  - Example: `hotfix/critical-auth-redirect`

## Conventions

- Use `kebab-case`; no spaces or uppercase letters.
- Keep `<short-description>` concise (`<= 5` words).
- Prefer specific scopes such as `auth`, `ui`, `api`, `build`, `db`, `i18n`.
- `release/*` branches target `main`.
- `hotfix/*` branches target `main`.
- `feature/*`, `fix/*`, `chore/*`, `refactor/*`, and `docs/*` target `develop` when it exists; otherwise target `main`.

## Agent Behavior

- When an agent needs to create a branch, it must choose one of the branch types above.
- If branch naming is ambiguous, agents should pick the closest valid type and keep the scope specific.
- Always follow the branch naming conventions in this document when creating PRs.
