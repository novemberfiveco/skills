---
name: commit
description: >
  Create a git commit following Conventional Commits specification with proper type, scope, and message format.
  Use when the user asks to commit changes, says "/commit", or wants to stage and commit work.
version: 1.0.0
authors:
  - jens.reynders@novemberfive.co
tags:
  - engineering
---

# Commit

Create a well-formatted git commit following the Conventional Commits specification.

## Workflow

1. **Assess changes** — run `git status` and `git diff --staged` (and `git diff` for unstaged changes) to understand what will be committed
2. **Stage files** — if nothing is staged, stage the relevant files (prefer explicit file names over `git add -A`). Never stage files that likely contain secrets (`.env`, credentials, etc.)
3. **Draft message** — compose a commit message following the format below
4. **Commit** — create the commit
5. **Verify** — run `git status` to confirm success

## Commit Message Format

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

### Types

| Type | Use for |
|------|---------|
| `feat` | A new feature |
| `fix` | A bug fix |
| `docs` | Documentation only changes |
| `style` | Formatting, whitespace, semicolons — no logic changes |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `perf` | Performance improvement |
| `test` | Adding or correcting tests |
| `build` | Build system or external dependency changes |
| `ci` | CI configuration changes |
| `chore` | Other changes that don't modify src or test files |
| `revert` | Reverts a previous commit |

### Scope (optional)

The scope should be the name of the module, package, or component affected (e.g., `auth`, `navigation`, `api`, `shared`, `ios`, `android`, `web`).

### Rules

- Use imperative, present tense ("add feature" not "added feature")
- Do NOT capitalize the first letter of the description
- No period (.) at the end of the description
- Reference Jira ticket numbers when applicable (e.g., `PROJ-1234`)
- Keep the description concise but descriptive
- Use the body to explain what and why, not how
- Never include `Co-Authored-By` lines

### Examples

```
feat(auth): add biometric login support

fix(api): resolve null pointer exception in repository layer

docs: update README with new setup instructions

refactor(navigation): simplify coordinator logic

chore: update dependencies to latest versions

feat(player): implement episode data fetching PROJ-1234
```

## Important

- Always use a HEREDOC when passing the commit message to `git commit -m`
- Never amend existing commits unless explicitly asked
- Never use `--no-verify` unless explicitly asked
- If a pre-commit hook fails, fix the issue and create a NEW commit (do not amend)
- Ask the user before committing if the scope or type is ambiguous
