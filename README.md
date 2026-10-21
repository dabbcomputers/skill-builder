# agent-system-builder

Skills you run inside your own repo to generate a tool-neutral agent setup: an orchestrator, engineers, a knowledge base, working skills and tool grants. First supported set: Software Dev.

- What it does and every file it creates: [INDEX.md](INDEX.md)
- How agents get tools on any AI tool: [capabilities.md](capabilities.md)
- Entry stage: [stages/00-build-agent-system.md](stages/00-build-agent-system.md)
- Per-tool wrapper forms (the builder writes these for you): [wrappers/](wrappers/)
- Shapes the generator fills in: [templates/](templates/)

## Language-agnostic by design

Works on a repo in any language or mix of languages. It is built around the software development lifecycle (ask, design, approve, implement, test, review, deploy, learn), which does not depend on the stack. Stack details are discovered from the repo and confirmed by the user.

## Design in one paragraph

Content lives in plain markdown under `docs/agents/` in the target repo. Each AI tool gets only a thin wrapper file, written for you by the builder. Roles name abstract capabilities, and one place translates them into a tool's concrete names. So the setup works with any LLM tool, and supporting a new tool means adding a wrapper form and one column in the capability table.

## Status

All eleven stages are drafted (entry plus ten forge stages), plus the capability model, templates and Claude Code wrapper form. One trial run was done on a public Go library (about 36 files generated, verify checks passed) and its findings were fixed. Not yet tried on a repo with a frontend and backend, or with a second AI tool.

## Independence

Agent free (plain markdown, wrappers generated per tool), path neutral (relative links only), and free of any private project's content. Details in [INDEX.md](INDEX.md#independence-rules).
