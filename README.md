---
name: git-commits
description: Use when creating, reviewing, or improving git commit messages. Generates concise, descriptive commits based on staged changes and follows Conventional Commits when appropriate.
license: MIT
metadata:
  author: Nasser
  version: "0.1.0"
---

# Git Commit Message Skill

A practical guide for writing clear, consistent, and useful git commit messages following the Conventional Commits specification. Use this when generating commit messages from staged diffs, reviewing existing commits, or coaching on commit hygiene.

---

## Conventional Commits Format

Every commit message follows this structure:

```text
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

### Full anatomy

```text
feat(auth): add OAuth2 login support

Replaces the legacy API-key authentication with OAuth2 to align
with the new identity provider requirements. Users are redirected
to the provider on login and receive a signed JWT on return.

BREAKING CHANGE: API key authentication is no longer supported.
Clients must migrate to OAuth2 before the next release.
Closes #142
```

| Part          | Required | Notes                                                       |
| ------------- | -------- | ----------------------------------------------------------- |
| `type`        | Yes      | Lowercase keyword describing the change category            |
| `scope`       | No       | Lowercase noun in parentheses identifying the area affected |
| `:`           | Yes      | Single colon and space after type/scope                     |
| `description` | Yes      | Short imperative summary, no period at the end              |
| `body`        | No       | Separated from subject by a blank line                      |
| `footer`      | No       | Separated from body (or subject if no body) by a blank line |

---

## Commit Types

### Primary types (most frequent)

| Type       | When to use                                                             |
| ---------- | ----------------------------------------------------------------------- |
| `feat`     | Adds a new capability, endpoint, UI component, or user-visible behavior |
| `fix`      | Corrects a bug, crash, incorrect output, or broken behavior             |
| `refactor` | Restructures code without changing behavior or external API             |
| `perf`     | Improves speed, memory usage, or efficiency without changing behavior   |

### Supporting types

| Type    | When to use                                                                                    |
| ------- | ---------------------------------------------------------------------------------------------- |
| `docs`  | Updates README, JSDoc, inline comments, or other documentation only                            |
| `style` | Formatting, whitespace, missing semicolons — no logic changes                                  |
| `test`  | Adds, updates, or fixes tests; no production code changes                                      |
| `build` | Dependency updates, bundler config, package.json scripts                                       |
| `ci`    | GitHub Actions, CI pipeline configs, Dockerfiles for CI                                        |
| `chore` | Maintenance tasks that touch neither src nor tests (renaming files, updating .gitignore, etc.) |

### Choosing between similar types

- `fix` vs `refactor`: if the code was broken, it is `fix`. If it worked but the code was messy, it is `refactor`.
- `refactor` vs `perf`: if the goal is measurable speed or resource gain, it is `perf`. General cleanup is `refactor`.
- `style` vs `refactor`: `style` never changes logic or variable names, only whitespace and formatting. Renaming a function is `refactor`.
- `build` vs `chore`: `build` affects how the project compiles or runs. `chore` is everything else that is purely administrative.
- `docs` vs `chore`: if the file being changed is documentation (`.md`, JSDoc, code comments), it is `docs`. Config file comments are `chore`.
- `ci` vs `build`: `ci` is for files only consumed by the CI system. `build` is for files consumed at development or production build time.

---

## Breaking Changes

A breaking change is any modification that requires consumers or callers to update their code.

### Method 1: Footer (preferred)

```text
feat(api): replace token auth with OAuth2

BREAKING CHANGE: The Authorization header format has changed from
"Bearer <token>" to "OAuth <token>". All API clients must update
their request headers before upgrading.
```

### Method 2: Exclamation mark in subject (concise)

```text
feat!: replace token auth with OAuth2
feat(api)!: replace token auth with OAuth2
```

Use the `!` form when the breaking nature is self-evident from the description alone. Add a `BREAKING CHANGE:` footer whenever migration guidance would be useful to the reader.

---

## Scope

Scope narrows the context to a specific part of the codebase. It is optional but adds significant value in medium-to-large projects.

**Good scope candidates:**

- Module or package name: `feat(auth)`, `fix(parser)`, `test(billing)`
- Layer or domain: `fix(api)`, `perf(db)`, `refactor(ui)`
- File or component name (for small projects): `fix(Header)`, `feat(LoginForm)`

**When to omit scope:**

- The change is truly cross-cutting (touches many unrelated areas)
- The project is small enough that scope adds noise rather than clarity
- The type already makes the scope obvious (`ci:`, `build:`)

**Rules:**

- Lowercase only
- No spaces; use hyphens if needed: `feat(user-profile)`
- Be consistent: pick a convention and stick to it across the project

---

## Subject Line Guidelines

The subject line is the single most-read part of a commit. It appears in `git log --oneline`, GitHub PR views, and changelogs.

**Rules:**

1. Aim for 50 characters or fewer; hard limit at 72
2. Use imperative mood ("add", "fix", "remove", not "added", "fixes", "removed")
3. No period at the end
4. Start with a lowercase verb after the colon and space
5. Be specific enough to understand without reading the diff
6. Do not use vague filler words ("stuff", "things", "misc", "updates", "changes")

**Imperative mood test:** read the subject as "If applied, this commit will \_\_\_". If it completes the sentence naturally, the mood is correct.

```text
If applied, this commit will... add pagination to the user list endpoint   ✅
If applied, this commit will... added pagination to the user list endpoint  ❌
If applied, this commit will... pagination for users                        ❌
```

---

## Body Guidelines

Use the body to answer **why**, not just what. The diff already shows what changed; the body explains the reasoning.

**When to include a body:**

- The change is non-obvious or has subtle implications
- You chose one approach over another and the reason matters
- There are known limitations, follow-up tasks, or tradeoffs
- The fix addresses a specific edge case that is not evident from the subject

**Formatting:**

- Separate from subject with a blank line
- Wrap lines at 72 characters
- Write in prose paragraphs; avoid bullet lists unless comparing multiple items
- Focus on motivation and context, not implementation details already visible in the diff

**Good body:**

```text
fix(cache): invalidate entry on concurrent write

Previously, two simultaneous writes to the same cache key could
result in the second write being silently dropped. The root cause
was a missing lock acquisition before the existence check. This
fix wraps the check-and-set operation in a mutex.
```

**Poor body:**

```text
fix(cache): invalidate entry on concurrent write

Changed the code so it works now. Also cleaned up some stuff.
```

---

## Footer Guidelines

Footers appear after the body (or after the subject if there is no body), separated by a blank line. Each footer is on its own line.

**Common footers:**

```text
BREAKING CHANGE: <description of what breaks and how to migrate>
Closes #<issue-number>
Fixes #<issue-number>
Refs #<issue-number>
Co-authored-by: Name <email>
Reviewed-by: Name <email>
```

**Rules:**

- `BREAKING CHANGE:` must be uppercase and followed by a colon and space
- One footer token per line
- Multiple footers are allowed

**Example with multiple footers:**

```text
feat(notifications): add email digest option

Adds a weekly email digest that aggregates unread notifications.
Users can enable it from the notification settings page.

Closes #88
Co-authored-by: Laila <laila@example.com>
```

---

## Atomic Commits and Commit Splitting

An atomic commit does one logical thing. It should be independently understandable, reversible, and deployable.

**Split commits when staged changes:**

- Fix a bug AND add an unrelated feature
- Refactor a function AND add tests for unrelated code
- Update dependencies AND change application logic

**Keep in a single commit when changes:**

- Are causally linked (a refactor that was necessary to enable the fix)
- Would break if applied separately (schema migration + code that uses the new column)
- Form a single coherent unit a reviewer would want to see together

**Practical signals to split:**

- You find yourself writing "and" in the subject line
- The type would need to be two different types
- Different reviewers would care about different parts

---

## Commit Generation Workflow

When generating a commit from staged changes, follow these steps in order.

### Step 1: Analyze staged changes

Read the full diff. For each file changed, note:

- What kind of file it is (source, test, config, doc, asset)
- What was added, removed, or modified
- Whether any public interfaces changed (function signatures, exports, API routes, schema)

### Step 2: Determine primary purpose

Ask: what is the single most important thing this diff accomplishes?

If there are multiple unrelated purposes, flag this and recommend splitting before generating a message.

### Step 3: Select the type

Apply the type selection rules from the Commit Types section. When in doubt between two types, prefer the one that most accurately describes the change from a consumer's perspective.

### Step 4: Select scope (if appropriate)

Identify the most specific module, layer, or component affected. Omit scope if the change is cross-cutting or the project convention omits it.

### Step 5: Write the subject line

- Start with the determined `type(scope):` prefix
- Write a concise imperative description of the primary change
- Keep under 50 characters where possible; never exceed 72
- No period

### Step 6: Decide whether a body is needed

Include a body if:

- The "why" is not self-evident
- The approach has tradeoffs worth recording
- There are follow-up considerations

### Step 7: Add footers

- Add `BREAKING CHANGE:` if any public interface was removed, renamed, or changed incompatibly
- Add issue references if traceable from context
- Use `!` in the subject as an alternative or supplement to `BREAKING CHANGE:`

---

## Type-by-Type Examples

### feat

```text
feat(search): add debounced search input

feat(user-profile): support uploading custom avatar

feat(api): expose /health endpoint for load balancer checks
```

```text
feat(checkout): add support for discount codes

Discount codes can now be applied at checkout. The system validates
the code against the promotions table and applies the percentage or
fixed amount reduction before calculating tax.

Closes #201
```

### fix

```text
fix(auth): redirect to login when session token expires

fix(form): prevent double submission on slow connections

fix(date-picker): correct off-by-one error in month calculation
```

```text
fix(upload): handle files with spaces in filename

The S3 key was not URL-encoding the filename before upload, causing
a 403 on filenames containing spaces or special characters.
```

### docs

```text
docs: add contributing guide

docs(api): document rate limiting headers in README

docs(hooks): add JSDoc to useDebounce return type
```

### style

```text
style: apply prettier formatting across src/

style(Button): fix inconsistent spacing in className string
```

### refactor

```text
refactor(parser): extract token validation into separate function

refactor(db): replace raw SQL with query builder in user module

refactor: remove dead code from legacy payment flow
```

### perf

```text
perf(images): lazy-load below-the-fold thumbnails

perf(query): add index on users.email to speed up login lookup

perf(bundle): replace moment.js with date-fns to reduce bundle size
```

### test

```text
test(auth): add coverage for expired token edge case

test(cart): add integration test for coupon application

test: fix flaky timeout in WebSocket connection test
```

### build

```text
build: upgrade typescript to 5.4

build(deps): update next.js from 14.1 to 14.2

build: add path alias for @ pointing to src/
```

### ci

```text
ci: add lint step to pull request workflow

ci: cache node_modules between workflow runs

ci: run tests on Node 18 and 20 in matrix
```

### chore

```text
chore: remove unused .env.example entries

chore: rename migrations directory to db/migrations

chore: update .gitignore to exclude .DS_Store
```

---

## Common Mistakes

### Vague subject lines

```text
❌  fix: stuff
❌  chore: updates
❌  feat: new things
❌  refactor: code cleanup
```

These are useless in a log. Specify what was fixed, updated, added, or cleaned.

```text
✅  fix(cart): remove duplicate item on rapid add click
✅  chore: remove unused environment variables from .env.example
✅  feat(notifications): add in-app badge count
✅  refactor(auth): extract token refresh logic into dedicated hook
```

### Past tense subject

```text
❌  feat: added user authentication
❌  fix: resolved memory leak
```

Commits describe what a patch _does_, not what you _did_.

```text
✅  feat: add user authentication
✅  fix: resolve memory leak in token parser
```

### "And" in the subject (non-atomic commit)

```text
❌  feat: add login page and fix signup validation
❌  refactor: clean up utils and update tests
```

Split into separate commits. Each commit should do one thing.

### Wrong type

```text
❌  chore(api): add new /export endpoint      (should be feat)
❌  feat: fix typo in error message           (should be fix or docs)
❌  style: rename function to follow naming   (should be refactor)
```

### Body that only restates the subject

```text
❌
fix(auth): handle missing refresh token

Fixed the refresh token handling so it works when the token is missing.
```

The body should add context, not repeat the subject in different words.

### Missing breaking change annotation

```text
❌  feat(api): remove v1 endpoints
```

Removing endpoints breaks callers. Always annotate:

```text
✅
feat(api)!: remove deprecated v1 endpoints

BREAKING CHANGE: All /v1/* routes have been removed. Migrate to /v2/*.
Migration guide: https://docs.example.com/migration/v2
```

---

## Quick Reference

```text
# Minimal valid commit
fix(scope): short imperative description

# Full commit with body and footer
type(scope): short imperative description

Explain why this change was necessary and what tradeoffs were made.
Wrap at 72 chars. Focus on motivation and context, not the diff.

BREAKING CHANGE: describe what breaks and how to migrate.
Closes #123
```

**Subject length targets:**

- Ideal: under 50 characters
- Acceptable: 50 to 72 characters
- Avoid: over 72 characters

**Mood check:** "If applied, this commit will `<your subject>`" must read naturally in English.
