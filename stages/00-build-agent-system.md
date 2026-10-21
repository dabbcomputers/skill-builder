---
name: build-agent-system
description: Entry stage. Explains what agent-system-builder will create in the user's repo, asks which agentic system set to build (Software Dev today) and which AI tools to target, then runs the build stages one at a time with approval between each. Use when the user wants an orchestrator, engineers, KB and skills setup in their own repo.
argument-hint: Optional. A stage name to resume or re-run, for example forge-kb.
---

## When to Use
The user runs this in their own repo to get a role, KB and skill setup. Re-run it to resume (it reads `docs/agents/BUILD-STATE.md`) or to redo a single stage. Not for editing an already-built setup by hand; use the generated `kb-logger` and role files for that.

## What to read first
- [`INDEX.md`](../INDEX.md): the full list of what gets created. Do not create anything not listed there.
- [`capabilities.md`](../capabilities.md): how tool grants work.
- `docs/agents/BUILD-STATE.md` in the user's repo, if it exists.

## Process
1. **Introduce yourself in a few lines, before any other action.** Say: you build a tool-neutral agent setup (roles, KB, skills, tool grants) into this repo; you work one piece at a time and wait for approval between pieces; you will not open env or secret files; you will only create what the index lists. Offer the index.
2. If `BUILD-STATE.md` exists, show progress and offer to resume or redo a stage. Otherwise continue.
3. Ask: "Which agentic system set do you want to build?" Offer **Software Dev** (the only one available now). If the user names something else, say it is not available yet and stop.
4. Ask which AI tools to target (for example Claude Code, or another tool that reads an `AGENTS.md`). If a tool is not Claude Code, ask for its real skill and tool-name conventions; never invent them.
5. Create `docs/agents/BUILD-STATE.md` with these sections: `System set`, `Tools`, `Stage status` (one line per stage), `Discovery` (filled by stage 1), `Answers` (one line per decision, with the stage that asked it), and `Generated files` (every path the builder wrote, relative to the repo root, appended by each stage as it writes). Later stages and the verify stage rely on this shape.
6. Run the stages in the order of the index table, one at a time. For each stage: say what it will do, do it, show the result, ask for approval or changes, record the outcome in `BUILD-STATE.md`, then move on.
7. At the end run `forge-verify` and give a short summary of what exists and the commands or files to try first.

## Questioning rules
- Read the repo before asking. Ask only what the repo cannot answer.
- One question at a time, with a recommended default when there is one.
- When the answer is unclear, say what you understood and ask again; never fill the gap with a guess.
- Record every answer in `BUILD-STATE.md` so the next stage does not ask again.
- Every stage that writes a file appends its relative path to `Generated files` in `BUILD-STATE.md` before it marks itself done.

## Things to avoid
- Opening `.env`, key or secret files, even to read key names. Give the user a command to run instead.
- Writing tool-specific names into role, KB or skill bodies. Concrete tool names belong only in wrapper files.
- Building several stages before showing the first.
- Assuming a language, framework or test runner. The repo may be in any language or several; learn it from the repo and the user.
- Copying one repo's specifics (names, paths, products) into another repo's setup. Templates are generic; content comes from this repo and the user.
- Creating `CODE_DOCUMENTATION.md` files before the user picks lazy or all-at-once.

## Related
- [`templates/`](../templates/): role and skill shapes. [`wrappers/`](../wrappers/): per-tool wrapper forms.
- [`INDEX.md`](../INDEX.md)
