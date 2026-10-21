# Giving tools to agents and skills, for any AI tool

## Problem

Claude Code lets an agent list tools by name (`Read, Write, Edit, Bash`). Other tools use other names, or have no allowlist at all. If role files name concrete tools, they stop being portable.

## Approach

1. Role and skill files declare **abstract capabilities** only.
2. The builder records the grants in one file in the user's repo: `docs/agents/tools.md`.
3. `forge-wrappers` translates capabilities into the concrete tool names of each chosen AI tool, and writes them only into that tool's wrapper files.

So the tool-neutral content never mentions a concrete tool, and adding a new AI tool means adding one column to the table below.

## Abstract capabilities

| Capability | Meaning | Risk |
|---|---|---|
| `read` | Read files | low |
| `search` | Find files and grep content | low |
| `edit` | Create and modify files | medium |
| `shell` | Run local commands | medium |
| `shell-remote` | Run commands on a remote host (SSH, cloud shell) | high, per-command approval |
| `web` | Search and fetch pages | low |
| `browser` | Drive a browser for UI checks | medium |
| `delegate` | Start another agent | medium, orchestrator only |
| `skill` | Invoke a skill | low |
| `mcp:<name>` | A named external integration (design tool, observability, tracker) | per integration |

## Defaults per role

| Role | Capabilities |
|---|---|
| Orchestrator | read, search, edit (docs only), skill, delegate, web |
| Frontend / Backend engineer | read, search, edit, shell, skill |
| Deployment engineer | read, shell, shell-remote, skill |
| Reviewer | read, search, shell (read-only commands), skill |
| Browser or UI checker | read, browser, skill |

Reviewer and Deployment engineer do not get `edit` on application source. That is how the "report, don't fix" boundary is enforced by the tool, not just by prose.

## Mapping to concrete tools

Only Claude Code is filled in. For every other tool the builder asks the user for the tool's real names, or reads that tool's docs, and never guesses.

| Capability | Claude Code | Other tool |
|---|---|---|
| read | Read | ask |
| search | Glob, Grep | ask |
| edit | Write, Edit | ask |
| shell | the host shell | ask |
| shell-remote | Bash running `ssh` | ask |
| web | WebSearch, WebFetch | ask |
| browser | a browser MCP server | ask |
| delegate | Agent | ask |
| skill | Skill | ask |

If a tool has no per-agent allowlist, the grant is enforced by the role file's "When to stop and ask" section and noted as soft in `tools.md`.

## `docs/agents/tools.md` shape (generated)

```
## <agent>
Capabilities: read, search, edit, shell, skill
Skills: bug-fixes, kb-logger
MCP: none
Needs approval each time: anything in shell-remote
Enforcement: hard (Claude Code allowlist) | soft (prose only)
```

## Skills that need tools

A skill states the capabilities it needs in its frontmatter (`needs: shell, web`). The builder checks that every agent allowed to invoke the skill holds those capabilities, and reports a mismatch instead of silently widening a grant.

## When a grant is written

- **Up front:** stage 8 (`forge-tools`) proposes grants per role and skill from the defaults above and records the approved result in `docs/agents/tools.md`.
- **At first need:** an agent that needs a capability it lacks must stop, name the capability and the reason, and ask. On approval it appends the grant to `tools.md` and `forge-wrappers` is re-run for that agent only. Integrations (`mcp:<name>`) are normally added this way.
- A grant is never added by editing a wrapper file directly; wrappers are regenerated from `tools.md`.
