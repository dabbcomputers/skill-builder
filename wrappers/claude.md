# Claude Code wrappers

Every wrapper points at a neutral file. Paths below are relative to the user's repo.

## Root entry: `CLAUDE.md`

```
# CLAUDE.md

This file only points Claude Code at the real instructions. It is tool-specific glue, not the content.

At the start of a new conversation, ask which role the user wants: {{role list}}. Wait for the answer before loading anything else.

## {{ROLE}}
Adopt the {{ROLE}} role, defined tool-neutrally at [`docs/agents/roles/{{role}}.md`](docs/agents/roles/{{role}}.md). Read it before doing anything non-trivial. Once selected, say "I'm your {{ROLE}}." in one line.
```

## Sub-agent wrapper: `.claude/agents/<name>.md`

```
---
name: {{name}}
description: {{one or two sentences, used to match the agent to a task}}
tools: {{concrete tool names for this agent's capabilities}}
---

Full role definition (tool-neutral) lives at [`docs/agents/roles/{{name}}.md`](../../docs/agents/roles/{{name}}.md). Read it before doing anything. This file is only the tool wiring; do not duplicate the role content here.
```

The orchestrator is the root session, so it gets no file here, only its section in `CLAUDE.md`.

## Skill wrapper: `.claude/skills/<name>/SKILL.md`

```
---
name: {{name}}
description: {{copied from the neutral skill's description}}
argument-hint: {{copied from the neutral skill}}
---

Follow the instructions in [`docs/agents/skills/{{name}}.md`](../../../docs/agents/skills/{{name}}.md). This file is only the tool wiring.
```

## Builder stage wrappers

The same skill-wrapper form is used to expose the builder's own stages (`stages/*.md`) to Claude Code, so users can run `/build-agent-system`. The installer writes these into the user's repo, pointing at the copy of `stages/` it was installed with.

## Capability to tool names

| Capability | Claude Code `tools:` value |
|---|---|
| read | Read |
| search | Glob, Grep |
| edit | Write, Edit |
| shell | the host shell |
| shell-remote | Bash |
| web | WebSearch, WebFetch |
| delegate | Agent |
| skill | Skill |
| browser, mcp:* | the integration's tool names, asked from the user |
