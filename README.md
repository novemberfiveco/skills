# November Five — Skills

A public library of **Skills** maintained by [November Five](https://novemberfive.co).

Skills are structured instruction sets that AI assistants and agents load dynamically to perform specialized tasks consistently. They encode how we work — our engineering standards, architecture patterns, communication conventions, and tooling workflows — so any AI tool used across the team operates with the same shared context.

This repository is shared publicly to contribute back to the community and to serve as practical examples for other teams building their own skill libraries.

---

## What is a Skill?

A skill is a folder containing a `SKILL.md` file with a description and a set of instructions. When an AI tool loads a skill, it gains specific, reliable behavior for a well-defined task — rather than relying on ad-hoc prompting each time.

Think of skills as reusable, version-controlled expertise: write them once, use them everywhere.

```text
skills/
├── .claude-plugin/
│   └── marketplace.json        ← Claude Code marketplace manifest
├── .cursor-plugin/
│   └── marketplace.json        ← Cursor marketplace manifest
├── november-five-skills/
│   ├── .claude-plugin/
│   │   └── plugin.json         ← Claude plugin manifest
│   ├── .cursor-plugin/
│   │   └── plugin.json         ← Cursor plugin manifest
│   ├── commands/               ← slash commands
│   ├── skills/                 ← skill definitions
│   └── README.md
└── ...

# Each skill follows this structure:
skills/
└── my-skill-name/
    ├── SKILL.md          ← instructions and metadata
    └── assets/           ← optional: scripts, templates, examples
```

---

## Compatibility

These skills follow the open [Agent Skills specification](https://agentskills.io) and are designed to be tool-agnostic. They can be used with:

| Tool | How |
|---|---|
| **Claude** (claude.ai / API) | Upload via Settings → Skills, or use the Skills API |
| **Claude Code** | Install via the marketplace (see [Installation](#installation)) |
| **Cursor** | Add via team marketplace settings (see [Installation](#installation)) |
| **Any MCP-compatible agent** | Serve skills as context through an MCP server |
| **Custom agents** | Load `SKILL.md` content into your system prompt at runtime |

The `SKILL.md` format is plain Markdown with a small YAML frontmatter block — no lock-in, no proprietary dependencies.

---

## Installation

### Claude Code

Add the November Five marketplace and install the skills plugin:

```bash
claude plugin marketplace add https://github.com/novemberfiveco/skills
claude plugin install november-five-skills
```

### Cursor

Add the marketplace URL in your Cursor team marketplace settings:

```
https://github.com/novemberfiveco/skills
```

The `november-five-skills` plugin will then be available for installation.

### Skills CLI

Use the `skills` CLI to add individual skills directly into your project:

```bash
# Install a specific skill
npx skills add https://github.com/novemberfiveco/skills --skill <skill-name>

# Install all skills
npx skills add https://github.com/novemberfiveco/skills
```

This copies the skill folder into your local `.skills/` directory, ready to be picked up by your AI tool of choice.

### Manual

Clone the repository directly if you want to browse or adapt everything locally:

```bash
git clone https://github.com/novemberfiveco/skills.git
```

---

## Skill template

```markdown
---
name: my-skill-name
description: >
  A clear description of what this skill does and when an agent should activate it.
  Be specific — this text is what triggers skill selection.
version: 1.0.0
authors:
  - your-name@novemberfive.co
tags:
  - engineering       # domain: engineering | architecture | product | tooling
---

# My Skill Name

[Short description of the task or domain this skill addresses.]

## When to use this skill

- Trigger condition 1
- Trigger condition 2

## Instructions

[Step-by-step or principle-based guidance the agent should follow.]

## Examples

- Example usage 1
- Example usage 2

## Guidelines

- Guideline 1
- Guideline 2
```

---

## Using this repository

This repository is maintained exclusively by the team at [November Five](https://novemberfive.co). We are not accepting external contributions at this time.

If a skill inspires you or you build something on top of it, we'd love to hear about it — feel free to open a Discussion or reach out at [hello@novemberfive.co](mailto:hello@novemberfive.co).

---

## Disclaimer

Skills in this repository reflect our current practices and are provided as working examples. Results may vary depending on the AI tool, model version, and context in which a skill is used. Always review agent output before using it in production.

---

*Maintained by the team at [November Five](https://novemberfive.co).*