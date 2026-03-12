---
name: health-check
description: >
  Perform a structured project health assessment following the company's standardised process.
  Produces a comprehensive report with RAG status and prioritised action items.
  Use when the user asks for a health check, project review, or code quality assessment.
version: 1.0.0
authors:
  - jens.reynders@novemberfive.co
tags:
  - engineering
---

# Health Check

Perform a structured project health assessment following the company's standardised process. Produces a comprehensive report with RAG status and prioritised action items.

## Workflow Overview

```
Phase 1: Preparation & Scoping
      |
Phase 2: Systematic Analysis (per category)
      |
Phase 3: Report Generation
      |
Phase 4: Review & Next Steps
```

---

## Phase 1: Preparation & Scoping

### Clarify Project Type

Before starting, determine the project type — it affects which categories apply:

| Project type | Crash monitoring | Availability tests | Load testing | Firebase/backend |
|---|---|---|---|---|
| **Application** | Yes | Yes | Yes | Yes |
| **SDK / Library** | No (consuming apps handle) | No | No | No |

### Clarify Context

Ask the user (if not already clear):

- What type of project is this? (app, SDK, library, backend service)
- Is CI/CD set up? Which platform? (Bitrise, GitHub Actions, GitLab CI, etc.)
- Are there test coverage metrics available?
- Where is the repository hosted? (GitHub, GitLab, Bitbucket)
- Are there cloud/backend integrations? (AWS, Firebase, etc.)

### Key Files to Discover

Use Glob and Read to find and review:

- **Documentation**: `README.md`, `CHANGELOG.md`, `docs/`
- **Dependencies**: `build.gradle*`, `Package.swift`, `package.json`, `Podfile`, `Gemfile`, `requirements.txt`, `Cargo.toml`, `go.mod`, `*.csproj`
- **CI/CD config**: `.github/workflows/`, `.gitlab-ci.yml`, `bitrise.yml`, `Jenkinsfile`, `.circleci/`
- **Test directories**: `**/test*/**`, `**/spec*/**`, `**/*Test*`, `**/*Spec*`
- **Source code**: Main source directories for TODOs, FIXMEs, deprecated code

### Search For

Use Grep to find:

- `TODO`, `FIXME`, `XXX`, `HACK`, `DEPRECATED` comments
- Hardcoded credentials: `apiKey`, `password`, `secret`, `token`, `credential`
- Version strings: check for consistency across files
- Security-sensitive code: network calls, authentication, data storage

---

## Phase 2: Systematic Analysis

Go through each category. Skip categories that don't apply to the project type (see Phase 1 table).

### 1. General

- Document all technical debt (TODOs, FIXMEs, workarounds)
- Identify developer experience improvements
- List code deprecations and outdated dependencies
- List external third-party integrations/APIs with deprecations or changes
- Check README status and completeness
- List documentation to update, improve, deprecate, or delete
- Check compiler/linter warnings

### 2. Security

- Review repository security settings
- Check for hardcoded credentials, API keys, secrets
- Review network requests for proper SSL/TLS
- Review user data handling
- Check for security vulnerabilities in dependencies

### 3. Observability

- Review logging implementation
- Check monitoring setup (crash monitoring for apps, not SDKs)
- Review performance monitoring and SLAs
- Identify monitoring adjustments needed

### 4. QA

- Assess test coverage (unit, integration, E2E)
- Check if automated tests run in CI/CD
- Identify parts requiring more or better tests
- Verify test coverage metrics

### 5. DevOps

- Review Git branching strategy
- Check PR process and review quality
- Verify CI/CD setup and configuration
- Document CI/CD pipeline status

### 6. Platform-Specific

Apply whichever are relevant:

- **Mobile (iOS/Android)**: OS version compatibility, SDK compatibility, crash rates, sample/demo apps
- **Frontend**: Framework version, dependencies, bundle size
- **Backend**: API docs, cloud service deprecations, cost optimisations
- **SDK/Library**: API stability, thread safety, error handling, logging capabilities

---

## Phase 3: Report Generation

Generate the report using this structure. Save as `HEALTH_CHECK_REPORT.md` in the project root (or a platform-specific subdirectory if doing a multi-platform check).

```markdown
# Health Check Report — [Project Name]

**Date**: [today's date]
**Project type**: [Application / SDK / Library / Backend Service]
**RAG Status**: [GREEN / AMBER / RED]

---

## Executive Summary

- **Overall status**: [RAG status with rationale]
- **Critical issues**: [count and brief description, or "None"]
- **Key strengths**: [what's working well]
- **Top priorities**: [1-3 most important items]

---

## 1. General

### Current State
[Assessment of technical debt, documentation, dependencies]

### Findings
| # | Finding | Severity | File / Location |
|---|---------|----------|-----------------|
| 1 | [description] | [CRITICAL/HIGH/MEDIUM/LOW] | `path/to/file:line` |

### Action Items
- [ ] [Actionable item with priority]

---

## 2. Security

### Current State
[Assessment of security posture]

### Findings
| # | Finding | Severity | File / Location |
|---|---------|----------|-----------------|
| 1 | [description] | [CRITICAL/HIGH/MEDIUM/LOW] | `path/to/file:line` |

### Action Items
- [ ] [Actionable item with priority]

---

## 3. Observability

[Same structure as above]

---

## 4. QA

[Same structure as above]

---

## 5. DevOps

[Same structure as above]

---

## 6. Platform-Specific

[Same structure as above — only include relevant subsections]

---

## Summary of Action Items

### Critical (Immediate Action Required)
- [ ] [item]

### High Priority
- [ ] [item]

### Medium Priority
- [ ] [item]

### Low Priority
- [ ] [item]

---

## Recommended Next Steps

1. [Prioritised, actionable next step with clear ownership]
2. [...]
```

### RAG Status Guidelines

| Status | Criteria |
|--------|----------|
| **GREEN** | No critical issues. Good test coverage (>80%). CI/CD configured. Documentation current. Dependencies up to date. No security vulnerabilities. |
| **AMBER** | Critical issues present but fixable. Functional and operational. Some areas need attention. Issues don't prevent current operation. Could move to GREEN with fixes. |
| **RED** | Critical blocking issues. Security vulnerabilities. Missing essential infrastructure (CI/CD). Poor test coverage. Operational risks. |

---

## Phase 4: Review & Next Steps

After generating the report:

1. Present the report to the user for review
2. Ask if any sections need deeper investigation
3. Suggest creating tickets for action items (offer to use Jira MCP if available)
4. Recommend scheduling a follow-up review after fixes

---

## Tips

1. **Start with Grep for quick wins** — TODOs, FIXMEs, and hardcoded secrets surface issues fast
2. **Check dependency versions against latest** — outdated deps are a common finding
3. **Always include file paths and line numbers** — makes findings immediately actionable
4. **Adapt categories to the project** — skip what doesn't apply, add what's missing
5. **Be specific in action items** — "Update dependency X from v1.2 to v2.0" beats "Update dependencies"
6. **Prioritise ruthlessly** — not everything is critical, and over-alarming reduces trust
7. **Note what's good** — a health check isn't just a list of problems
