# Changelog

All notable changes to the Python Code project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [1.1.0] - 2026-03-27

### Fixed
- **License badge** -- README badge now correctly shows AGPLv3 (was incorrectly labeled GPLv3).
- **TOC error** -- Fixed `complexity-and-simplicity.md` Table of Contents §5 title mismatch ("Hickey's Complecting Table" → "Complexity Detection Checklist").
- **Duplicated content** -- Removed inline complexity checklist from SKILL.md; replaced with cross-reference to `complexity-and-simplicity.md` §5.
- **SQLAlchemy 2.0+** -- Added modern `Mapped`/`mapped_column` example alongside legacy imperative mapping in `architecture-patterns.md`.

### Added
- **27 new routing entries** -- Expanded question classification tables in SKILL.md to cover previously unrouted sections (design-heuristics §2-§4/§6-§7/§11-§12, decision-frameworks §2-§4, pre-design §3-§5/§7-§8, architecture-patterns §2/§6/§13/§15/§17-§18, style-guide §2/§5-§6/§9/§13-§14).
- **Python code examples in theory files** -- Added runnable Python examples to `complexity-and-simplicity.md` (+4 examples), `design-heuristics.md` (+6 examples), `pre-design-and-expertise.md` (+5 examples). All theory files now have concrete code.
- **DDD definition** -- Added introductory definition of Domain-Driven Design in `architecture-patterns.md` §3.
- **Freshness checks** -- Build validation now warns about Python version references below 3.10 and stale dist/ artifacts. Added to both Makefile and build.ps1.

## [1.0.0] - 2026-03-27

### Added
- **Initial release** -- expert Python code guidance combining architecture patterns, style best practices, and system design mental models.
- **SKILL.md** -- core skill definition with 7 core principles, question classification routing, decision trees, and response templates.
- **Architecture patterns** (`references/architecture-patterns.md`) -- Python-specific pattern implementations: DIP, hexagonal, DDD, repository, service layer, UoW, aggregates, events, CQRS, DI, pipelines, data stacks, class architecture, performance, API-first, project structure.
- **Style guide** (`references/style-guide.md`) -- idiomatic Python with before/after examples: naming, iteration, data structures, functions, classes, error handling, type annotations, testing, toolchain, 20-item anti-patterns checklist.
- **Decision frameworks** (`references/decision-frameworks.md`) -- trade-off analysis: Kleppmann's framework, design stamina, innovation tokens, monolith vs microservices, consistency vs availability, sync vs async, build vs buy, caching, database selection.
- **Design heuristics** (`references/design-heuristics.md`) -- daily rules-of-thumb: deep modules, design it twice, simple design rules, red flags, abstraction lifecycle, data dominates, bounded contexts, failure thinking, Conway's Law.
- **Complexity and simplicity** (`references/complexity-and-simplicity.md`) -- theory of complexity: essential vs accidental, simple vs easy, three symptoms, knowledge degradation, detection checklist.
- **Pre-design and expertise** (`references/pre-design-and-expertise.md`) -- thinking processes before code: hammock-driven development, think above the code, mental model debugging, TDD, writing as thinking, constraint identification, expertise progression.
- **Build system** -- Makefile (Unix/Linux/macOS) and build.ps1 (Windows PowerShell) for packaging and validation.
