---
name: forge-flows
description: Stage 2. Proposes the delivery flows for the user's repo (new feature, small change, bug fix, release, incident, knowledge update, retro), shows them as a diagram, asks about every unclear point one question at a time, and writes the agreed flows to docs/agents/flows.md. Later stages build roles and skills from this file.
argument-hint: Optional. A single flow name to redo, for example "bug fix".
needs: read, edit
---

## When to Use
Run after `forge-discover` has saved its findings. Re-run for one flow when the team changes how it works. Out of scope: writing role files, skills or guidelines. This stage only decides and records the flows.

## What to read first
- `docs/agents/BUILD-STATE.md`: the Discovery section and earlier answers. Do not re-ask what is recorded.
- [`../INDEX.md`](../INDEX.md): "Flow questions" and "Safety rules baked into every generated setup".

## Process
1. **Say what this stage does** in two lines: it proposes how work moves from ask to shipped, you correct it, and the result becomes the rulebook the roles follow.
2. **Propose the flows.** Start from the standard set below and trim or add based on Discovery. For each, show the steps as a numbered list with the role that owns each step and the stop points (where a human must approve).

   Standard set:
   - **New feature:** understand the ask, study the relevant knowledge, high-level design, approval stop, low-level design, delegate to engineers, scope check against the design, independent review, summary.
   - **Small change:** short plan, implement with tests, review. Skips the design documents. Needs a stated definition of "small".
   - **Bug fix:** reproduce or trace the cause, state root cause and options, user picks, implement, test, review. The stop is before any fix is dispatched.
   - **Release and deploy:** baseline check, build, deploy to the lowest environment, validate, then each higher environment with its own approval.
   - **Incident and log investigation:** read-only diagnosis first; any fix is handed to the owning engineer as a bug fix.
   - **Knowledge update:** a fact or decision from conversation is written to the right knowledge file at once.
   - **Retro:** a mistake is logged in a fixed short format to the right lessons file.
3. **Show a diagram** of the main flow (the new feature flow) as plain text boxes and arrows or a fenced diagram block the user's viewer can render. Mark the approval stops. Keep it to one screen.
4. **Ask the flow questions**, one at a time, skipping any the repo or earlier answers settle. Give a recommended default with each:
   1. Who approves a design, and at which points must an agent stop?
   2. What counts as a "small change" that may skip the full design?
   3. Where do tests live, and which run in CI? (Confirm Discovery's answer.)
   4. Is there shared or cross-cutting code (authentication, billing, common libraries) that needs extra care and explicit mention in designs?
   5. How are branches, pull requests and releases handled? Who merges?
   6. Where are open decisions and past mistakes recorded today? Should they move to the new files?
   7. Are there actions that must always wait for explicit approval besides prod (for example data deletion, public posting, spending money)?
5. **Handle unclear answers.** Restate what you understood and ask again. If still unclear, record it under "Open questions" in the output; never fill the gap with a guess.
6. **Check consistency.** Every flow's stop points must match the safety rules in the index (design approval gate, per-command approval for prod). Flag any conflict and ask the user to resolve it.
7. **Write `docs/agents/flows.md`** in the shape below. Show it to the user and wait for approval or changes.
8. **Record** the answers and mark stage 2 done in `docs/agents/BUILD-STATE.md`.

## Output shape: `docs/agents/flows.md`

```
# Delivery flows

## Roles in play
<role: one-line job>, from Discovery and the user.

## <Flow name>
When it applies: <one line>
Steps:
1. <step> (owner: <role>)
2. ...
Stops for approval: <which steps, who approves>
Records written: <design file, knowledge update, lessons entry, none>

## Definitions
Small change: <the agreed definition>
Needs extra care: <shared or cross-cutting areas, from question 4>
Always needs explicit approval: <list>

## Open questions
<anything unresolved, or "none">
```

## Things to avoid
- Inventing a flow the team does not want just because it is in the standard set; trim it.
- Treating "small" as self-evident. Without an agreed definition the design gate gets skipped by habit.
- Language, framework or tool names in the flows. They describe the lifecycle, not the stack.
- Absolute paths or user names in the output. Links are relative.
- Opening env, key or secret files to answer a question; ask for a command's output instead.
- Creating roles, skills or guidelines here. Those are later stages and they read `flows.md`.
- Asking several questions at once.

## Related
- [`01-forge-discover.md`](01-forge-discover.md): provides the repo facts this stage uses.
- Next: `forge-roles` (stage 3), which creates the roles named in `flows.md`.
