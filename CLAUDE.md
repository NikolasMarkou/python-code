# CLAUDE.md

Guidance for working with the Python Code codebase.

## Project Purpose

Claude Code skill -- expert Python code guidance combining architecture patterns, style best practices, and system design mental models (Brooks, Hickey, Fowler, Kleppmann, Evans, Nygard, McKinley, Lamport).

Use cases: writing, reviewing, or structuring Python code; DDD, repository, service layer, CQRS, event-driven, or hexagonal architecture; Python style; testability problems; dependency injection; async vs sync design; data pipelines; performance; managing complexity.

## Repository Structure

```
python-code/
├── README.md                         # User documentation
├── LICENSE                           # GNU GPLv3
├── VERSION                           # Single source of truth for version number
├── CHANGELOG.md                      # Version history
├── CLAUDE.md                         # This file
├── Makefile                          # Unix/Linux/macOS build script (reads VERSION)
├── build.ps1                         # Windows PowerShell build script (reads VERSION)
└── src/
    ├── SKILL.md                      # Core skill (7 principles, question routing, decision trees) - the main instruction set
    └── references/                   # Knowledge base documents (loaded on-demand)
        ├── architecture-patterns.md  # Python-specific pattern implementations (DIP, hexagonal, DDD, repository, service layer, UoW, aggregates, events, CQRS, DI, pipelines)
        ├── complexity-and-simplicity.md # Theory of complexity (Brooks, Hickey, Ousterhout, Blow)
        ├── decision-frameworks.md    # Trade-off analysis (Kleppmann, Fowler, McKinley, monolith vs microservices, consistency, caching, DB selection)
        ├── design-heuristics.md      # Daily rules-of-thumb (deep modules, simple design, red flags, bounded contexts, failure thinking, Conway's Law)
        ├── pre-design-and-expertise.md # Thinking before code (hammock-driven, TDD, writing as thinking, constraint identification, expertise progression)
        └── style-guide.md            # Idiomatic Python (naming, iteration, data structures, functions, classes, error handling, type annotations, testing, toolchain, anti-patterns)
```

## Activation Triggers

Python code questions, or: "is this Pythonic?", "how should I structure this?", "review this Python code", "should I use X or Y?", "is this the right abstraction?", "how do I test this without a database?".

## Skill Reference

Complete spec in **src/SKILL.md**. Key sections:

- **7 Core Principles**: src/SKILL.md "The Seven Core Principles" section
- **Question Classification**: src/SKILL.md "Question Classification → Reference Routing" (pre-design, architecture, style)
- **Decision Trees**: src/SKILL.md "Quick-Reference Decision Trees" (pattern cost/benefit, class vs function, monolith vs microservices, concurrency)
- **Response Templates**: src/SKILL.md "Response Templates" (architecture questions, code review)
- **Architecture Patterns**: `src/references/architecture-patterns.md` (18 sections)
- **Style Guide**: `src/references/style-guide.md` (15 sections, 20 anti-patterns)
- **Decision Frameworks**: `src/references/decision-frameworks.md` (10 frameworks)
- **Design Heuristics**: `src/references/design-heuristics.md` (12 heuristics)

Do not duplicate skill content here. Read src/SKILL.md directly.

## Working with This Codebase

### File Modification Guidelines

- **src/SKILL.md** -- core skill. Changes affect all Python code guidance behavior. Keep focused on principles and routing; detailed implementation goes in reference files.
- **src/references/** -- supplementary knowledge, read on-demand. Add new files for expanded guidance. Don't duplicate content across files.
- **VERSION** -- single source of truth. `Makefile` + `build.ps1` read from it. Bump only `VERSION` + `CHANGELOG.md`.

### Content Guidelines

- All reference material must be actionable -- code examples, decision tables, concrete patterns. No vague guidance.
- Use tables for decision matrices and comparisons.
- Use "When to use / When NOT to use" sections for every pattern and framework.
- Include failure modes and mitigations, not just happy paths.
- Keep code templates minimal but functional -- enough to copy-paste and adapt.

### Build Commands

```bash
# Windows (PowerShell)
.\build.ps1 build            # Build skill package structure
.\build.ps1 build-combined   # Build single-file skill with inlined references
.\build.ps1 package          # Create zip package
.\build.ps1 package-combined # Create single-file skill in dist/
.\build.ps1 package-tar      # Create tarball package
.\build.ps1 validate         # Validate skill structure
.\build.ps1 clean            # Remove build artifacts
.\build.ps1 list             # Show package contents
.\build.ps1 help             # Show available commands

# Unix/Linux/macOS
make build                   # Build skill package structure
make build-combined          # Build single-file skill with inlined references
make package                 # Create zip package (default)
make package-combined        # Create single-file skill package
make package-tar             # Create tarball package
make validate                # Validate skill structure
make clean                   # Remove build artifacts
make list                    # Show package contents
make help                    # Show available targets
```

### Validation Checklist

- [ ] `.\build.ps1 validate` passes (or `make validate`)
- [ ] src/SKILL.md has `name:` and `description:` in YAML frontmatter
- [ ] All cross-references in src/SKILL.md point to existing files in `src/references/`
- [ ] README.md project structure lists all files in `src/references/`
- [ ] CLAUDE.md repository structure lists all files in `src/references/`

### Testing Changes

Validate skill changes by testing with prompts like:
- "Is this Pythonic?" (should trigger style guide routing)
- "How should I structure this project?" (should consult architecture patterns)
- "Should I use microservices or monolith?" (should reference decision frameworks)
- "How do I test without a database?" (should reference repository pattern)

## Updating Local Skill

When asked to "update local skill", copy **everything** from the repo to `~/.claude/skills/python-code/` -- no exceptions, no partial copies:

```bash
# Full sync -- mirrors repo structure exactly
cp src/SKILL.md ~/.claude/skills/python-code/SKILL.md
cp -r src/references/ ~/.claude/skills/python-code/references/
cp README.md LICENSE CHANGELOG.md ~/.claude/skills/python-code/
```

Always verify with `diff -rq` after copying. Every file, every time.
