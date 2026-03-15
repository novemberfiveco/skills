---
name: technical approach
description: >
  Analyses business requirements (from Jira tickets, Confluence pages, or direct input)
  and the codebase to produce a high-level technical approach — architectural decisions,
  affected areas, and trade-offs — without a full implementation plan. Use this skill whenever
  the user wants to understand HOW to approach a Jira ticket technically, asks for a technical
  analysis of requirements, wants to know what parts of the codebase are affected by a change,
  or needs architectural guidance before diving into implementation. Also trigger when the user
  says things like "how would we build this", "what's the approach for this ticket", "break
  this down technically", or "where would this change go".
version: 1.0.0
authors:
  - jens.reynders@novemberfive.co
tags:
  - engineering
---

# Technical Approach

Translate business requirements into a high-level technical approach through codebase-aware analysis and iterative review with the user.

The goal is to bridge the gap between "what the business wants" and "how to think about building it" — without prescribing exact files or step-by-step implementation. The output should give a developer enough architectural clarity to start working confidently, while leaving room for implementation-level decisions during coding.

## Workflow

```
Phase 1: Gather Requirements
      ↓
Phase 2: Explore the Codebase
      ↓
Phase 3: Quick Alignment       ←── User feedback ──┐
      ↓ (confirmed)                                 │
Phase 4: Full Technical Approach ←── User feedback ─┘
```

---

## Phase 1: Gather Requirements

Requirements can come from two sources. Use whichever applies — or combine both if the user gives partial context alongside a ticket reference.

### From a Jira Ticket

If a Jira URL (`https://*.atlassian.net/browse/XXX-1234`) or ticket key (`XXX-1234`) is provided, fetch the ticket using whichever method is available. Prefer `acli` when it's installed — it tends to return richer, more reliable output.

#### Option A — Atlassian CLI (`acli`) (preferred)

Check if `acli` is available by running `which acli` or `acli --help`. If it is:

1. **Fetch the issue** — `acli jira --action getIssue --issue XXX-1234`. This returns the full issue including summary, description, acceptance criteria, status, labels, components, and sub-tasks in one call.

2. **Fetch linked Confluence pages** — check the issue output for Confluence links. If present, use `acli confluence --action getPageSource --space <SPACE> --title "<page title>"` (or `--id <pageId>`) to retrieve the content. Product specs, design docs, and technical RFCs often live in Confluence rather than the ticket itself — these are critical context.

3. **Fetch sub-tasks** — for each sub-task listed on the parent, run `acli jira --action getIssue --issue XXX-1235` to get its description and acceptance criteria. Large tickets often have a breakdown that reveals scope the parent description glosses over.

> `acli` supports many output formats and filters. Use `--outputFormat 2` for a more structured output when parsing is needed. Consult `acli jira --action getIssue --help` if you need to fine-tune the query.

#### Option B — Atlassian MCP

If `acli` is not available but the Atlassian MCP is connected:

1. **Resolve the Atlassian Cloud ID** — call `getAccessibleAtlassianResources()` and extract the `cloudId` for the relevant instance.

2. **Fetch the issue** — call `getJiraIssue(cloudId, issueIdOrKey)`. Extract:
   - Summary and description (the "what")
   - Issue type and status
   - Labels, components, and epic (hints about scope and domain)
   - Acceptance criteria (often embedded in the description)
   - Sub-tasks (a pre-existing breakdown that constrains the approach)

3. **Fetch linked documents** — call `getJiraIssueRemoteIssueLinks(cloudId, issueIdOrKey)`. If Confluence links are present, fetch them with `getConfluencePage(cloudId, pageId)`.

4. **Fetch sub-tasks** — for each sub-task key listed in the parent issue, call `getJiraIssue` to get its description and acceptance criteria.

> If neither `acli` nor the Atlassian MCP is available, let the user know and ask them to paste the relevant details directly.

### From Direct Input

If the user describes requirements in natural language, extract:

- What the feature or change is (user story or problem statement)
- Acceptance criteria or expected behaviour
- Any constraints mentioned (performance, backwards compatibility, platform-specific, etc.)
- Which parts of the system the user thinks are involved (if they have an opinion)

### Synthesise

Regardless of source, distil the requirements into a clear problem statement before moving on. This becomes the anchor for every decision in the approach. If anything is ambiguous, ask the user now rather than guessing later.

---

## Phase 2: Explore the Codebase

Use the requirements to guide a targeted exploration. The purpose here is not to read every file but to understand the architecture and patterns well enough to make informed high-level decisions.

### Project-specific context (optional)

Before exploring the codebase from scratch, check whether the project provides a `.claude/project-architecture.md` file (or similar — also look for `.claude/architecture.md`, `docs/architecture.md`, or `ARCHITECTURE.md` in the project root). If one exists, read it first — it typically describes the project's layer structure, module conventions, shared code boundaries, naming patterns, and other context that will make the rest of the exploration faster and more accurate.

This file is maintained by individual project teams and is entirely optional. If it doesn't exist, that's fine — discover the architecture through exploration as described below. If it does exist, treat it as a starting point and verify its claims against the actual code (documentation can drift).

### What to look for

Think about the requirements and identify which concerns they touch. Then explore each relevant concern:

- **Existing patterns** — How does the codebase already solve similar problems? If there's a feature that's analogous to what's being asked for, study its structure. Following established patterns reduces risk and review friction.
- **Architecture boundaries** — Where are the layer boundaries (e.g., data / domain / presentation, client / server, module boundaries)? Understanding these tells you where new logic belongs.
- **Shared vs. target-specific code** — If the codebase has shared modules consumed by multiple targets (e.g., a shared library, a core package, a common module used by multiple services or applications), understand the boundary. Changes to shared code have wider blast radius.
- **Configuration and feature flags** — Is there a feature flag system? Multi-tenant or brand-specific configuration? Understanding how variability is managed affects how you introduce new behaviour.
- **Data flow** — How does data get from its source (API, database, user input) to where it's consumed (UI, another service, export)? Trace the relevant flow end-to-end at a high level.
- **Dependencies and integration points** — What external systems, APIs, or libraries are involved? These are often where constraints and risks live.

### How to explore

Use search tools (grep, file search, etc.) to locate relevant code. Read key files to understand patterns, but stay at the level of "how is this structured" rather than "what does line 47 do". Look at:

- Module/package structure and naming conventions
- Public interfaces and contracts between layers
- Dependency injection setup (tells you how things are wired together)
- Existing tests (reveal expected behaviour and testing patterns)
- Configuration files (reveal build structure, environment setup, feature flags)

---

## Phase 3: Quick Alignment

Before investing in a full approach, present a brief summary to make sure you and the user are on the same page. This catches misunderstandings early.

Use this template:

```markdown
# Quick Alignment — [Feature Name / TICKET-KEY]

## My Understanding
[2-3 sentences summarising what you think needs to happen and why.]

## Proposed Direction
[The core architectural idea in 2-4 bullet points. Not "what to build" but "how to think about building it".]

## Areas Involved
[Which parts of the codebase this touches, at the module/layer level.]

## Things I Want to Confirm
- [ ] [Assumption or ambiguity that could change the approach]
```

Ask the user: *"Does this match your thinking? Anything I'm misreading or missing before I flesh out the full approach?"*

Incorporate feedback. If the direction needs to change significantly, repeat this phase.

---

## Phase 4: Full Technical Approach

Once alignment is confirmed, produce the complete technical approach.

```markdown
# Technical Approach — [Feature Name / TICKET-KEY]

## Problem Statement
[Clear, concise description of what needs to be solved and why it matters. Reference acceptance criteria where relevant.]

## Architectural Decisions

### [Decision 1 title]
**Decision**: [What you're recommending]
**Rationale**: [Why this is the right call, referencing codebase patterns or constraints]
**Alternatives considered**: [Brief mention of what else you thought about and why you didn't go that way]

### [Decision 2 title]
**Decision**: [...]
**Rationale**: [...]
**Alternatives considered**: [...]

[Add as many decisions as needed. Typical decisions cover things like: where new logic belongs, how data flows through the system, how the change is exposed to users or consumers, how configuration or feature flags are handled, error handling strategy, and API or contract design.]

## Affected Areas

For each area, describe WHAT changes at a high level — not specific files, but modules, layers, or components.

### [Area name — use the project's own terminology for modules, layers, or services]
- What changes: [High-level description of the change in this area]
- Why: [Ties back to a requirement or decision]
- Patterns to follow: [Reference an existing analogous implementation in the codebase]

### [Another area]
- What changes: [...]
- Why: [...]
- Patterns to follow: [...]

## Cross-Cutting Concerns
- **Testing**: [What kinds of tests matter most for this change and why]
- **Migration / Backwards Compatibility**: [Any data migration, API versioning, or backwards-compat considerations — or "None"]
- **Feature Flags / Rollout**: [Whether this should be behind a flag and why — or "Not needed"]
- **Performance**: [Any performance implications worth thinking about — or "No concerns identified"]

## Risks & Open Questions
- [Risk or uncertainty that could affect the approach, with a suggested mitigation or next step]
- [Question that needs input from the team, product, or design before implementation]

## Dependencies & Sequencing
[If the work spans multiple areas or teams, what order makes sense and why. E.g., "Shared module changes should land first since downstream consumers depend on them."]
```

Present this to the user and invite feedback: *"Here's the full technical approach. Does the reasoning hold up? Anything you'd push back on or want to explore further?"*

---

## Tips

1. **Lead with "why", not "what"** — Every architectural decision should explain its reasoning. A developer reading the approach should understand not just what to do but why that's the right call, so they can adapt when they hit unexpected details during implementation.

2. **Stay at the right altitude** — Think modules and layers, not files and functions. "Extend the authentication module to support this new flow, following the existing pattern" is the right level. "Create a new file called X in directory Y" is too detailed for this skill.

3. **Reference existing patterns by name** — When you find a good analogy in the codebase ("this should work like the existing payment flow"), call it out. It gives the developer a concrete anchor without needing to spell out every detail.

4. **Surface risks early** — Uncertainty about scope, unclear requirements, tricky integration points, performance concerns — these belong in the approach, not discovered during implementation.

5. **Don't over-decide** — The approach should give direction, not remove all choice. Leave room for the implementing developer to make tactical decisions. If something genuinely doesn't matter at the architectural level, don't force a decision on it.

6. **Respect the codebase's conventions** — Even if you'd design things differently from scratch, the approach should work with the existing architecture, not against it. Consistency with established patterns almost always trumps theoretical elegance.
