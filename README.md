# project-analysis

A Claude Code skill plugin that produces comprehensive codebase documentation — 8 documents covering stack, structure, architecture, conventions, integrations, testing, concerns, and workflow blueprints.

## Usage

Invoke the skill in Claude Code when you need to understand a codebase:

```
/project-analysis
```

The skill runs a 4-phase workflow:

1. **Scan** — Runs `scripts/scan.py` from the target project root to detect languages, CI/CD, containers, security configs, and code metrics
2. **Investigate** — Uses reference checkpoints to probe each documentation area
3. **Populate** — Fills 8 templates into `docs/codebase/`, marking unknowns as `[TODO]` and intent-dependent gaps as `[ASK USER]`
4. **Validate** — Cross-checks every claim against evidence, repairs gaps, re-validates until all 8 docs pass

### Focus Areas

Pass an optional focus argument to prioritize specific documents:

- `architecture only`
- `testing and concerns`

## Output

After completion, the target project has these files in `docs/codebase/`:

| Document | Covers |
|----------|--------|
| `STACK.md` | Language, runtime, frameworks, all dependencies |
| `STRUCTURE.md` | Directory layout, entry points, key files |
| `ARCHITECTURE.md` | Layers, patterns, data flow |
| `CONVENTIONS.md` | Naming, formatting, error handling, imports |
| `INTEGRATIONS.md` | External APIs, databases, auth, monitoring |
| `TESTING.md` | Frameworks, file organization, mocking strategy |
| `CONCERNS.md` | Tech debt, bugs, security risks, perf bottlenecks |
| `WORKFLOWS.md` | E2E traces, layer implementation patterns, templates |

## Requirements

- Python 3.8+
- git
- No other dependencies

## Project Structure

```
SKILL.md                       # Skill definition and workflow instructions
plugin.json                    # Plugin metadata
scripts/scan.py                # Phase 1 automated scanner
references/
  inquiry-checkpoints.md       # Per-template investigation questions
  stack-detection.md           # Manifest-to-ecosystem mappings
assets/templates/              # Output document templates
  STACK.md, STRUCTURE.md, ARCHITECTURE.md, CONVENTIONS.md,
  INTEGRATIONS.md, TESTING.md, CONCERNS.md, WORKFLOWS.md
```

### scan.py

Single-file Python script with zero dependencies beyond stdlib. Run from the target project root:

```bash
python3 /path/to/project-analysis/scripts/scan.py --output docs/codebase/.codebase-scan.txt
```

Detects 25+ language manifests, 10+ CI/CD platforms, container/orchestration configs, security policies, and performance markers.

## License

MIT
