# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Claude Code skill plugin (`project-analysis`) that produces comprehensive codebase documentation — 8 docs covering stack, structure, architecture, conventions, integrations, testing, concerns, and workflow blueprints. It is NOT a traditional application; it has no build step, no test suite, and no runtime dependencies beyond Python 3.8+ and git.

## Structure

```
SKILL.md              # Skill definition + complete workflow instructions (loaded by Claude at runtime)
plugin.json           # Plugin metadata (name, version, author, license)
scripts/scan.py       # Phase 1 scanner — Python 3.8+, no dependencies, run from target project root
references/           # Loaded during Phase 2 investigation
  inquiry-checkpoints.md   # Per-template investigation questions
  stack-detection.md       # Manifest→ecosystem mappings, framework detection tables
assets/templates/     # Copied into docs/codebase/ during Phase 3
  STACK.md, STRUCTURE.md, ARCHITECTURE.md, CONVENTIONS.md,
  INTEGRATIONS.md, TESTING.md, CONCERNS.md, WORKFLOWS.md
```

## Key Commands

```bash
# Test scan.py on the current directory (run from target project root)
python3 /home/li/hot-skills/installed/PDCA/project-analysis/scripts/scan.py

# Test scan.py with output file
python3 /home/li/hot-skills/installed/PDCA/project-analysis/scripts/scan.py --output /tmp/scan-output.txt
```

No build, lint, or test commands — this is a documentation-generation skill, not a compiled project.

## How the Skill Works (4-phase workflow)

1. **Phase 1** — Run `scripts/scan.py` from the target project root, search for PRD/README/SPEC files, summarize stated intent
2. **Phase 2** — Investigate using checkpoints from `references/inquiry-checkpoints.md` (load `references/stack-detection.md` only if stack is ambiguous)
3. **Phase 3** — Populate all 8 templates from `assets/templates/` into `docs/codebase/`, using `[TODO]` for unknowns and `[ASK USER]` for intent-dependent gaps
4. **Phase 4** — Validate every doc against checkpoints, confirm evidence references, repair and re-validate until all 8 pass

## scan.py Design

- Single-file Python script, zero dependencies beyond stdlib
- Runs from the **target project root** (not the skill directory)
- Detects: 25+ language manifests, monorepo signals, CI/CD pipelines, containers, security configs, performance markers
- Collects: directory tree, entry points, lint config, env templates, TODOs (production code only), git commits, git churn, code metrics
- Configurable via constants at top (TREE_LIMIT, TODO_LIMIT, etc.)

## Editing Guidelines

- `SKILL.md` is the primary file — it contains the full workflow that Claude follows at runtime. Changes here affect how the skill behaves.
- `references/*.md` are loaded on-demand during Phase 2; keep them focused on lookup tables and checklists.
- `assets/templates/*.md` define the output contract; section structure must stay consistent with `references/inquiry-checkpoints.md`.
- `scripts/scan.py` is the only executable code; keep it self-contained with no external dependencies.
- When adding new language/framework detection, update both `scan.py` constants AND the corresponding reference tables in `references/stack-detection.md`.
