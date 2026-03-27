# Changelog

All notable changes to the Python Code project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

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
