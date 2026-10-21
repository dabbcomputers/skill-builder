---
name: forge-roles
description: Stage 3. Builds the role files one at a time, orchestrator first, then each engineer area the repo needs, then the reviewer. Shows each role, takes corrections, and writes it to docs/agents/roles/ as neutral markdown. The deployment engineer is built in stage 4.
argument-hint: Optional. A single role name to build or redo.
needs: read, search, edit
---

## When to Use
Run after `forge-flows` has written `docs/agents/flows.md`. Re-run for one role when its responsibilities change. Out of scope: the deployment engineer (stage 4), skills (stage 7), tool grants (stage 8), wrapper files (stage 9).

## What to read first
- `docs/agents/flows.md` and `docs/agents/BUILD-STATE.md` (Discovery section).
- [`../templates/role.md`](../templates/role.md): the exact section order every role follows.
- [`../capabilities.md`](../capabilities.md): default capabilities per role.

## Process
1. **Propose the role list** from Discovery's engineer areas and the roles named in `flows.md`: the orchestrator, one engineer per area the user confirms, and the reviewer. A repo with one stack and no client/server split (for example a single library) gets one engineer; do not split roles just to match examples. Mention the deployment engineer will follow in stage 4 and the optional security optimizer. Ask the user to confirm or change the list.
2. **Build one role at a time**, in this order: orchestrator, engineers, reviewer. For each role:
   1. Draft it from `templates/role.md`, using the rules for that role type below.
   2. Fill every section from the repo, `flows.md` and earlier answers. Where the repo does not say, ask one question; do not guess.
   3. Show the draft and wait for approval or edits. Do not start the next role first.
   4. Write it to `docs/agents/roles/<role>.md`. Record it in `BUILD-STATE.md`.
3. **Cross-check at the end.** Every step owner in `flows.md` has a role. Every "hands off to" names a real role. No role claims another's job. Report gaps and fix them with the user.
4. Mark stage 3 done.

## Rules per role type

**Orchestrator (the lead role that talks to the user)**
- Understands the ask, studies the knowledge, produces designs, gets approval, delegates, checks the result, and owns outcomes including failures. It does not do an engineer's job; if no engineer covers a task, it says so out loud before doing it.
- Process follows the new feature flow in `flows.md`, with the approval stop written as mandatory, even for small-looking changes.
- When delegating, hands each engineer the design plus the exact knowledge files for the task, so the engineer does not re-derive them.
- Engineers never hand work to each other. Only the orchestrator delegates between them, and relays any request one engineer cannot do (for example needing production logs).
- Before delegating, states the blast radius: shared or cross-cutting code touched, cost of any expensive data query, security boundaries crossed. Prefers changing a call site over shared code and says so.
- Locks contracts in the design: stored fields and API shapes are a closed list; anything outside it is off-limits and a missing field means stop and ask.
- After implementation, compares the actual change with the design's scope before sending to the reviewer; extra changes go back.
- Final summaries list every environment variable, key or secret the user must change, by exact name, split by area.
- Stops and asks: after each design, before destructive or hard-to-reverse actions, and when a design would break an existing consumer.
- Default capabilities: read, search, edit (docs only), skill, delegate, web.

**Engineer (one per area)**
- Implements its area of an approved design and tests what it builds. There is no separate tester.
- Reads the fixed files for its area (guidelines, testing notes) plus the task-specific knowledge files the orchestrator names.
- Follows the contract list; stores and returns only agreed fields; reads each field through one path.
- Places code where the repo's conventions say; reuses existing helpers before writing new ones.
- Reports back: what changed, how it was tested, what was reused, any deviation from the design and why.
- Updates the knowledge files affected by its change in the same pass.
- Stops and asks: contract changes that could break a consumer, and anything touching payments, credits, authentication or other agreed sensitive areas.
- Default capabilities: read, search, edit, shell, skill.

**Reviewer**
- Independent check after implementation. Reports findings; does not fix them.
- Checks against the guidelines, the design's contracts and scope, test coverage, security, and failure-path logging.
- Findings are ranked and each names the file, the rule, and a concrete failure scenario.
- Default capabilities: read, search, shell (read-only commands), skill. No edit access to application source.

**Optional: security optimizer**
- Manual trigger only, never part of the default flow. Audits a named file, folder or change for the rules the user lists, proposes fixes, and applies each only after per-finding approval.

## Questions to ask while building roles
Only where the repo does not answer:
- Which folders does this engineer own, and which does it never touch?
- Which areas are sensitive and always need a stop-and-ask?
- Are there rules about where code lives (routes, helpers, shared constants) that the reviewer should enforce?
- What does the reviewer check that the CI does not?

## Things to avoid
- Language, framework, tool or product names in role bodies unless the user's repo itself requires it (for example a folder name). Examples come from the repo, never from another project.
- Concrete tool names. Roles list abstract capabilities only.
- Absolute paths or user names. Links are relative.
- Copying knowledge into a role. Link to the knowledge file.
- A role that both builds and reviews its own change.
- Writing "Things to avoid" entries that were not earned. Start with the rules above; real mistakes are added later by the retro skills.
- Creating a lessons-learned file now. It is created on the first retro entry.
- Building the next role before the current one is approved.

## Related
- [`02-forge-flows.md`](02-forge-flows.md): provides the flows the roles must satisfy.
- Next: `forge-deployment-engineer` (stage 4).
