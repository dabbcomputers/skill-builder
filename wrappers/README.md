# Wrappers

The builder's own stages (`stages/`) and everything it generates (`docs/agents/` in the user's repo) are plain markdown with no tool-specific syntax. A **wrapper** is the small file that makes one AI tool load one of those markdown files.

The builder writes wrappers for the user. The `forge-wrappers` stage does it:

1. Read `docs/agents/BUILD-STATE.md` for the chosen tools.
2. For each tool, read its form from this folder (`claude.md` today).
3. For every role, skill and the root entry, write that tool's wrapper, translating abstract capabilities from `docs/agents/tools.md` into the tool's concrete names.
4. Show the list of files it will write, wait for approval, then write them.

A wrapper contains: the tool's frontmatter, one sentence saying what it is, and a link to the neutral file. No role or skill content is copied into it.

## Adding a tool

Ask the user for the tool's real file formats and tool names, or read its documentation. Do not guess. Then add `<tool>.md` here with the same four forms as `claude.md` (root entry, role wrapper, skill wrapper, tool-name column) and fill that tool's column in `../capabilities.md`.

## Re-running

Wrappers are generated, never hand-maintained. Re-running `forge-wrappers` rewrites them from the neutral files and `tools.md`. If a wrapper has hand edits, show the diff and ask before overwriting.

## Forms available

- [claude.md](claude.md): Claude Code.
