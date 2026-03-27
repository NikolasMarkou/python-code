# Python Code

[![License](https://img.shields.io/badge/License-AGPLv3-blue.svg)](LICENSE)
[![Skill](https://img.shields.io/badge/Skill-v1.1.0-green.svg)](CHANGELOG.md)
[![Sponsored by Electi](https://img.shields.io/badge/Sponsored%20by-Electi-red.svg)](https://www.electiconsulting.com)

**Stop writing Python that "works" but doesn't scale. This skill teaches you to think before you code.**

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill that provides expert Python code guidance combining architecture patterns, style best practices, and system design mental models from Brooks, Hickey, Fowler, Kleppmann, Evans, Nygard, McKinley, and Lamport.

The problem it solves: you ask an AI for Python help and get syntactically correct code with no thought given to architecture, testability, or trade-offs. Python Code applies a structured approach across three layers -- mental models (how to think about problems), architecture patterns (what to build and where to put it), and style (how to write it) -- with 7 core principles enforced at every recommendation.

---

## Get Started in 60 Seconds

**Option 1 -- Zip package (recommended)**
Download the latest zip from [Releases](https://github.com/NikolasMarkou/python-code/releases) and unzip into your local skills directory:
```bash
unzip python-code-v*.zip -d ~/.claude/skills/
```

**Option 2 -- Single file**
Download `python-code-combined.md` from [Releases](https://github.com/NikolasMarkou/python-code/releases) and add it to Claude Code's Custom Instructions (Settings > Custom Instructions).

**Option 3 -- Clone the repo**
```bash
git clone https://github.com/NikolasMarkou/python-code.git ~/.claude/skills/python-code
```

Then ask Claude about Python -- or just say: **"is this Pythonic?"** or **"how should I structure this?"**

---

## How It Works

Three layers of Python excellence, unified by seven core principles. Every recommendation grounded in mental models from the field's best thinkers.

### The Seven Core Principles

| # | Principle | Key Insight |
|---|---|---|
| 1 | **Complexity is the only real enemy** | Detect via change amplification, cognitive load, unknown unknowns |
| 2 | **Simple ≠ Easy** (Hickey) | Simple = not intertwined (objective). Easy = familiar (subjective). Prefer simple. |
| 3 | **Think before building** (Lamport, Hickey) | State the problem in writing. Identify invariants. Only then implement. |
| 4 | **Design is economic** (Fowler) | Good design pays off in weeks, not months. |
| 5 | **Accept trade-offs honestly** (Kleppmann) | Every choice trades X for Y. Never recommend without stating the cost. |
| 6 | **Boundaries are the primary design tool** | Deep modules, bounded contexts, bulkheads, aggregates, the Dependency Rule. |
| 7 | **The Dependency Rule** | Domain never imports infrastructure. Test: can you unit test without DB/network? |

### Question Classification

The skill routes questions to the right reference material:

| Question Type | Examples | Reference |
|---|---|---|
| **Pre-design** | "Should I use microservices?", "SQL or NoSQL?" | `decision-frameworks.md` |
| **Architecture** | "How do I structure this?", "Tests need a database" | `architecture-patterns.md` |
| **Style** | "Is this Pythonic?", "How do I name this?" | `style-guide.md` |
| **Complexity** | "Is this the right abstraction?" | `complexity-and-simplicity.md` |
| **Process** | "How do I start designing this?" | `pre-design-and-expertise.md` |
| **Decomposition** | "How do I decompose this domain?" | `design-heuristics.md` |

### Decision Trees

Quick-reference decision trees for common choices:
- **Pattern Cost/Benefit** -- when to add Repository, Service Layer, UoW, DDD, Aggregates, Events, CQRS, Hexagonal
- **Class vs Function** -- persistent state? immutable record? single operation?
- **Monolith vs Microservices** -- default monolith first, extract when boundaries are empirically clear
- **Python Concurrency** -- asyncio vs ProcessPoolExecutor vs multiprocessing
- **Complexity Checklist** -- 6-question diagnostic before adding any pattern

---

## When to Use This

| Use it | Skip it |
|--------|---------|
| Writing, reviewing, or structuring Python code | Non-Python languages |
| Architecture decisions (DDD, hexagonal, CQRS) | Simple scripts with no structure needs |
| Python style and idiom questions | Non-coding questions |
| Testability problems ("tests need a database") | |
| Trade-off analysis ("should I use X or Y?") | |
| Managing complexity and abstractions | |

Trigger phrases: *"is this Pythonic?"*, *"how should I structure this?"*, *"review this Python code"*, *"should I use X or Y?"*, *"is this the right abstraction?"*, *"how do I test without a database?"*

---

## Contributing

### Build and Package

```bash
# Windows (PowerShell)
.\build.ps1 package          # Create zip package
.\build.ps1 package-combined # Create single-file skill
.\build.ps1 validate         # Validate structure
.\build.ps1 clean            # Clean build artifacts

# Unix / Linux / macOS
make package                 # Create zip package
make package-combined        # Create single-file skill
make validate                # Validate structure
make clean                   # Clean build artifacts
```

### Project Structure

```
python-code/
├── README.md                 # This file
├── CLAUDE.md                 # AI assistant guidance for contributing
├── CHANGELOG.md              # Version history
├── LICENSE                   # GNU GPLv3
├── VERSION                   # Single source of truth for version number
├── Makefile                  # Unix/Linux/macOS build
├── build.ps1                 # Windows PowerShell build
└── src/
    ├── SKILL.md              # Core skill -- 7 principles, question routing, decision trees
    └── references/
        ├── architecture-patterns.md  # Python-specific pattern implementations (18 sections)
        ├── complexity-and-simplicity.md # Theory of complexity (Brooks, Hickey, Ousterhout, Blow)
        ├── decision-frameworks.md    # Trade-off analysis (10 decision frameworks)
        ├── design-heuristics.md      # Daily rules-of-thumb (12 heuristics)
        ├── pre-design-and-expertise.md # Thinking before code (8 sections)
        └── style-guide.md            # Idiomatic Python (15 sections, 20 anti-patterns)
```

---

## Sponsored by

This project is sponsored by **[Electi Consulting](https://www.electiconsulting.com)** -- a technology consultancy specializing in AI, blockchain, cryptography, and data science. Founded in 2017 and headquartered in Limassol, Cyprus, with a London presence, Electi combines academic rigor with enterprise-grade delivery across clients including the European Central Bank, US Navy, and Cyprus Securities and Exchange Commission.

---

## License

[GNU Affero General Public License v3.0](LICENSE)
