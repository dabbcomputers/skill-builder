---
name: forge-kb
description: Stage 5. Builds the knowledge base for the user's repo, one file per feature area plus testing notes, and the uber-level router that sends agents to the right file. Asks whether per-folder code documentation should be created lazily or all at once, and sets that up.
argument-hint: Optional. A single feature area to build or refresh.
needs: read, search, edit, shell
---

## When to Use
Run after `forge-deployment-engineer`. Re-run for one feature area when it changes a lot. Out of scope: coding guidelines (stage 6), skills such as the knowledge logger (stage 7), and the deployment knowledge file (already written in stage 4).

## What to read first
- `docs/agents/BUILD-STATE.md`: Discovery (candidate feature areas, test setup) and earlier answers.
- `docs/agents/flows.md` and the files in `docs/agents/roles/`.

## Process
1. **Say what this stage does** in two lines: it writes what is true about the system so agents do not re-derive it, one area at a time, and it will ask when the code is unclear.
2. **Confirm the feature areas.** Take the list from Discovery. Show it with each area's main folder and ask the user to merge, split, rename or drop entries. Mark each area's status once the user says it (active, being retired, planned, parked).
3. **Ask the code documentation question** (see "Code documentation modes" below) and record the answer.
4. **Build the area files one at a time.** For each area:
   1. Read the area's code enough to describe it accurately: entry points, main flow, data touched, patterns, tests. Read only what you need; if the code is ambiguous, ask one question.
   2. Draft `docs/agents/KB/<area>.md` in the shape below. If the repo has distinct sub-areas (for example separate client and server sides), use sub-folders with matching names so each engineer reads only its side.
   3. Show it; take corrections; write it.
5. **Write testing notes** per engineer area: `docs/agents/KB/tests/<area>-testing.md` with where tests live, naming, how to run them, which run in CI, and conventions the user confirms. Commands come from Discovery and the user.
6. **Write `docs/agents/uber-level.md`** after the area files exist (see shape below). Show it and wait for approval.
7. **Set up code documentation** according to the chosen mode.
8. **Record** the areas and the mode in `docs/agents/BUILD-STATE.md` and mark stage 5 done.

## Feature area file shape

```
# <Area>

## Purpose
<what it does for users or the system, 1 to 3 lines>

## Status
<active | being retired | planned | parked, plus one line why>

## Flow
<how it works end to end, in plain prose or a short list>

## Code entry points
- <path relative to the repo root>: <one line>

## Data and storage touched
<stores, tables, collections, queues, files, with the fields that matter>

## Design patterns
<patterns the code already uses and agents must follow>

## Tests
<link to the testing notes, plus area-specific notes>

## Tech debt
<known problems, each one line>

## Open questions
<unresolved items, or "none">

## Related
<links to neighbouring knowledge files>
```

Each file must be self-contained enough to start work from. Link to neighbours instead of repeating them.

## `uber-level.md` shape

```
# <Project> overview and knowledge router

## What this product is
<short description, from the README and the user>

## How it got its shape
<history that explains odd names or structure, only if the user gives it>

## How to use this knowledge base
1. Find your area in the routing table.
2. Open the matching file(s).
3. If something is stale or wrong, fix it as part of your change.

## Routing table
| Area | Status | Knowledge file(s) | Notes |

## Cross-cutting conventions
<rules that apply to many areas; links to the coding guidelines once they exist>

## Also relevant
<other docs, per-folder code documentation, flows, open decisions, lessons>
```

## Code documentation modes
Ask once, with a recommendation:

- **Lazy (recommended for repos of more than a few dozen source files):** nothing is generated now. The first time an agent works in a folder with no code documentation, the `docs-sync` skill (stage 7) creates it for that folder only. Record the mode and note it in `uber-level.md` and in each engineer role's "What to read first".
- **All at once:** create it for every source folder now. Warn the user it takes longer, costs more to produce, and goes stale until regenerated.
- **None:** agents rely on the knowledge files and reading code. Record it.

Format of each `CODE_DOCUMENTATION.md`, identical for every language:

```
# Code documentation for <folder>

## <file name>
Path: <relative path>
Description: <what the file is for>

### Functions
#### <function or method name>
- Line: <number>
- Description: <one line>
- Calls: <names, or None>
- Called by: <names and files, or a count with examples>
```

Rules: produce it by reading the source, not with a language-specific parser, so any language works. Cover only source files, not generated, vendored or test-data files. In all-at-once mode, write one folder first, show it, get approval of the format, then continue folder by folder. Never open secret or environment files.

## Things to avoid
- Describing code you have not read. Mark anything unverified as an open question.
- Language, framework or product names that come from anywhere but the user's repo. Examples are invented or taken from the repo.
- Absolute paths or user names. Paths are relative to the repo root; links are relative.
- Copying long passages of source or docs into knowledge files; summarize and point.
- One giant file. One area per file; split when a file stops being quick to read.
- Creating code documentation before the user picks a mode.
- Treating the knowledge base as finished. State in `uber-level.md` that stale files get fixed when found, via the knowledge logger.
- Building the next area before the current one is approved.

## Related
- [`04-forge-deployment-engineer.md`](04-forge-deployment-engineer.md): wrote the deployment knowledge file that the router links to.
- Next: `forge-guidelines` (stage 6).
