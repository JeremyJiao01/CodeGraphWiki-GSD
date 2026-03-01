---
name: gsd:map-codebase
description: Index codebase with CodeGraphWiki to build knowledge graph, vector embeddings, and wiki pages in .planning/codebase/wiki/
argument-hint: "[optional: rebuild — force full re-index even if cached]"
allowed-tools:
  - Read
  - Bash
  - Glob
  - Grep
  - Write
---

<objective>
Index the codebase using CodeGraphWiki to produce:
1. A persistent knowledge graph + vector embeddings (used by researcher and executor at query time)
2. Wiki pages written to .planning/codebase/wiki/ (reference docs for planning)

Output: .planning/codebase/wiki/ with multi-page wiki + INDEX.md summary.
</objective>

<execution_context>
@~/.claude/get-shit-done/workflows/map-codebase.md
</execution_context>

<context>
Rebuild flag: $ARGUMENTS (if "rebuild", pass rebuild=true to initialize_repository)

**Load project state if exists:**
Check for .planning/STATE.md - loads context if project already initialized

**This command can run:**
- Before /gsd:new-project (brownfield codebases) - creates codebase index first
- After /gsd:new-project (greenfield codebases) - updates index as code evolves
- Anytime to refresh codebase understanding
</context>

<when_to_use>
**Use map-codebase for:**
- Brownfield projects before initialization (understand existing code first)
- Refreshing codebase index after significant changes
- Onboarding to an unfamiliar codebase
- Before major refactoring (understand current state)
- When STATE.md references outdated codebase info

**Skip map-codebase for:**
- Greenfield projects with no code yet (nothing to index)
- Trivial codebases (<5 files)
</when_to_use>

<process>
1. Check if .planning/codebase/wiki/ already exists (offer to refresh or skip)
2. Run CodeGraphWiki init:
   ```bash
   python3 ~/.claude/commands/code-graph/cgb_cli.py init "$(pwd)"
   ```
3. List available wiki pages:
   ```bash
   python3 ~/.claude/commands/code-graph/cgb_cli.py list-wiki
   ```
4. For each page, read and write to .planning/codebase/wiki/:
   ```bash
   python3 ~/.claude/commands/code-graph/cgb_cli.py get-wiki <page_id>
   ```
5. Write .planning/codebase/INDEX.md summarizing available pages
6. Scan for secrets in written files
7. Commit codebase index
8. Offer next steps (typically: /gsd:new-project or /gsd:plan-phase)
</process>

<success_criteria>
- [ ] initialize_repository completed successfully
- [ ] Wiki pages written to .planning/codebase/wiki/
- [ ] INDEX.md written to .planning/codebase/
- [ ] Knowledge graph + embeddings available for researcher/executor MCP queries
- [ ] User knows next steps
</success_criteria>
