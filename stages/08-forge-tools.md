---
name: forge-tools
description: Stage 8. Proposes tool grants per role and per skill as abstract capabilities, checks that every agent allowed to invoke a skill holds the capabilities the skill needs, and writes the approved grants to docs/agents/tools.md. Also writes the first-need rule into each role, so later grants are added deliberately.
argument-hint: Optional. A role or skill name to regrant, or a capability to add.
needs: read, edit
---

## When to Use
Run after `forge-skills`. Also run (for one agent) whenever an agent hits the first-need rule and the user approves a new capability. Out of scope: tool-specific names and wrapper files (stage 9).

## What to read first
- [`../capabilities.md`](../capabilities.md): the abstract capability list, risk levels and role defaults.
- `docs/agents/roles/*.md`, `docs/agents/skills/*.md`, `docs/agents/flows.md`.
- `docs/agents/BUILD-STATE.md`: chosen tools, deployment access answers.

## Process
1. **Say what this stage does** in two lines: it decides what each agent may do, in tool-neutral terms, and nothing is widened later without asking.
2. **Propose grants.** For each role, start from the defaults in `capabilities.md`, then adjust using the role file and the deployment answers (remote shell only if granted in stage 4). For each skill, read its `needs:`.
3. **Check consistency.**
   - Every skill an agent may invoke has `needs:` that the agent holds. Report each mismatch and propose a fix. Prefer removing the skill from that role and listing it under the role that runs it; grant a capability only when the role truly needs to run the skill itself. Never grant `shell` to the orchestrator just to satisfy a skill.
   - No role that must not change application source (reviewer, deployment engineer) holds `edit` on it.
   - `shell-remote` appears only where stage 4 granted it.
4. **Ask about integrations.** List any external integrations the flows imply (design tools, trackers, observability services). Do not add any now unless the user names one and its use is clear; the rest come at first need.
5. **Show the grant table** and wait for approval or changes.
6. **Write `docs/agents/tools.md`** in the shape below.
7. **Write the first-need rule** into each role's "When to stop and ask" section: if a task needs a capability not listed in `tools.md`, stop, name the capability and the reason, and ask the user; on approval append it to `tools.md`; never widen a grant by editing a wrapper file.
8. **Record** the approval and mark stage 8 done in `docs/agents/BUILD-STATE.md`.

## First-need protocol (used after this stage)
1. An agent finds it needs a capability it lacks (for example the first browser check, the first remote command, or a new external integration).
2. It stops and states the capability, the exact task that needs it, and the risk level from `capabilities.md`.
3. The user approves or declines in chat. Approval covers that grant for that agent, not other agents.
4. On approval the orchestrator appends the grant to `docs/agents/tools.md` and runs `forge-wrappers` for that agent only.
5. Remote and production capabilities still need per-command approval after being granted.

## Output shape: `docs/agents/tools.md`

```
# Tool grants

Capabilities are abstract; see the builder's capabilities list for meanings. Concrete tool names appear only in wrapper files.

## <agent or skill-owning role>
Capabilities: <list>
Skills: <list>
Integrations: <names or none>
Needs approval each time: <capabilities or actions>
Enforcement: hard (the tool restricts it) | soft (prose rule only)

## Change log
<date> <agent> <capability added> <reason>
```

## Things to avoid
- Concrete tool names anywhere in this stage's output.
- Granting broad capabilities "to be safe". Start from least access.
- Giving `edit` on application source to reviewer or deployment roles.
- Granting `shell-remote` without a stage 4 answer that allows it.
- Adding integrations speculatively.
- Absolute paths or user names in `tools.md`.

## Related
- [`../capabilities.md`](../capabilities.md)
- [`07-forge-skills.md`](07-forge-skills.md): provides the `needs:` values.
- Next: `forge-wrappers` (stage 9).
