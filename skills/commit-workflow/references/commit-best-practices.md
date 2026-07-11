# Commit best practices

## Enforce

- Use Conventional Commits for every commit.
- One logical change per commit.
- Keep subjects <= 72 chars; imperative mood; lowercase.

## Types

- feat: new user-facing feature
- fix: bug fix
- chore: tooling/infra/maintenance (no production code change)
- docs: documentation only
- refactor: code change that neither fixes a bug nor adds a feature
- perf: performance improvement
- test: add or update tests
- build: build system or dependencies
- ci: CI configuration/files
- style: formatting/whitespace (no code meaning change)
- revert: revert a previous commit

## Format

- Subject: `type(scope): short summary`
  - Example: `feat(auth): add email/password sign-in flow`
- Body (optional but recommended): explain the "why" and approach.
  - Leave one blank line between the subject line and the body (Git convention).
  - **Agents:** whenever you include body text, format it as a **markdown bullet list**
    using `- ` at the start of each item (even for a single short point).
  - **Do not** use continuous prose paragraphs or naked sentences/lines without `- `
    as the body.
  - One bullet per distinct idea; concise parallel phrasing; nested bullets only when
    necessary.
  - **Line length:** each physical line of the body must be **<= 72 characters**.
    Wrap long bullets with continuation lines indented so wrapped segments stay under
    the limit.
  - Explain what changed and why in simple language.
  - Mention key tools or files only if necessary.
  - Avoid long narratives; do not insert blank lines between bullets unless separating
    body from footer.
- Footer (optional):
  - Breaking changes: `BREAKING CHANGE: description`
  - Issue refs: `Ref: #123`, `Closes: #456`

## Practices

- Commit early, but keep commits meaningful.
- Prefer small commits that are easy to review.
- Link issues in the body, not in the subject.
- Include screenshots/GIFs for UI changes in PRs.
- Ensure commits pass: lint, typecheck, tests, and build locally.

## Examples

- `fix(build): ensure edge runtime uses Node 18-compatible polyfills`
- `refactor(ui): extract Button variants to separate file`
- `docs: add environment setup and scripts`
- `perf(api): cache product list with revalidate=60`
- `test(auth): add e2e tests for reset flow`
- Avoid (body as prose or lines without bullets):

  ```
  chore(deps): bump tooling packages

  Updates lockfile and adjusts CI cache paths after the upgrade.
  ```

- Prefer:

  ```
  chore(deps): bump tooling packages

  - Refresh lockfile after dependency bumps.
  - Point CI cache paths at new package manager layout.
  ```

## Agent Behavior

- When making commits, use the format and types above.
- Always follow the commit conventions in this document when preparing and confirming commits.
- Never add Claude (or any AI assistant) as a commit co-author/participant
  (e.g. no `Co-Authored-By: Claude` trailer).
- Prefer short, atomic commits with clear subjects.
- Include a body when the change needs explanation or context.
- When proposing or writing a body, **always** use `- ` bullets and **<= 72 chars**
  per line; never substitute loose sentences or prose paragraphs for listed bullets.
