---
name: branch
description: >
  Create a git branch following the project's naming conventions.
  Use when the user asks to create a branch, start work on a ticket, or says "/branch".
version: 1.0.0
authors:
  - jens.reynders@novemberfive.co
tags:
  - engineering
---

# Branch

Create a properly named git branch following the project's naming conventions.

## Workflow

1. **Gather info** — determine the branch type and purpose. If a Jira ticket key is provided (or can be inferred), include it. If not provided, ask the user.
2. **Determine base** — default to `develop` if it exists, otherwise `main`. Use `main` for hotfixes. The user can override the base branch.
3. **Create branch** — create and checkout the new branch
4. **Confirm** — show the created branch name

## Branch Naming Convention

```
<type>/<short-description>-<JIRA-KEY>
```

### Types

| Type | Use for | Base branch |
|------|---------|-------------|
| `feature` | New features | `develop` or `main` |
| `bugfix` | Bug fixes | `develop` or `main` |
| `task` | Tasks | `develop` or `main` |
| `chore` | Chores, maintenance | `develop` or `main` |
| `release` | Release branches (format: `release/x.x.x`) | `develop` or `main` |
| `hotfix` | Hotfix branches (format: `hotfix/x.x.x`) | `main` |

### Rules

- Use lowercase kebab-case for the description
- Include the Jira ticket key when available, unless it's a release/hotfix branch
- Keep the description short but meaningful (2-5 words)
- For release/hotfix branches, use semantic version (`x.x.x`) instead of a description

### Examples

```
feature/dark-mode-settings-PROJ-1234
bugfix/fix-null-pointer-article-PROJ-5678
task/update-dependencies-PROJ-9012
chore/cleanup-unused-imports-PROJ-3456
release/2.5.0
hotfix/2.4.1
```

## Important

- If the user provides a Jira ticket URL or key, extract the ticket number automatically
- If no Jira ticket is mentioned and it's not a release/hotfix, ask the user for one
- Always fetch latest before branching: `git fetch origin`
- Detect the default base branch: check if `origin/develop` exists, otherwise fall back to `origin/main`
- Create the branch from the appropriate base: `git checkout -b <branch> origin/<base>`
