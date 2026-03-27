---
name: python-code
description: >
  Expert Python code guidance combining architecture patterns, style best practices, and system
  design mental models (Brooks, Hickey, Fowler, Kleppmann, Evans, Nygard, McKinley, Lamport).
  Use whenever the user asks about: writing, reviewing, or structuring Python code; DDD,
  repository, service layer, CQRS, event-driven, or hexagonal architecture; Python style
  (naming, idioms, comprehensions, type hints, error handling); testability problems (tests
  need a database); dependency injection; async vs sync design; data pipelines; performance;
  "how should I structure this?"; monolith vs microservices; consistency vs availability;
  build vs buy; caching; database selection; managing complexity; "is this the right
  abstraction?"; Conway's Law; bounded contexts; failure thinking; pre-design practices.
  Also trigger when the user pastes Python code asking for review, says "is this good Python?",
  asks how to make something more Pythonic, or asks any "should I..." question about Python.
---

# Python Code: Architecture + Style + System Design

This skill unifies three layers of Python excellence:

- **Mental models** — *How to think* about problems before touching code (Brooks, Hickey, Ousterhout, Fowler, Kleppmann, Evans, Nygard, Helland, Lamport, McKinley…)
- **Architecture patterns** — *What to build and where to put it* (Cosmic Python, hexagonal, DDD, CQRS, events…)
- **Style** — *How to write it* (Guido, Langa, Hettinger, Montani, McKinney, Reitz, Shaw…)

When answering a question, identify which layer(s) apply, then pull from the relevant reference file(s).

---

## The Seven Core Principles

Every recommendation flows from these. No framework, pattern, or style rule overrides them.

### 1. Complexity Is the Only Real Enemy
Every master engineer identifies complexity — not bad frameworks, not wrong languages — as what kills systems. It accumulates incrementally: one shortcut, one leaking abstraction, one mutable global at a time. Sweat the small stuff (Ousterhout). Detect it through three symptoms: **change amplification** (touching 12 files for one change), **cognitive load** (too much context needed to work safely), **unknown unknowns** (you don't know what to change). → See `references/complexity-and-simplicity.md`

### 2. Simple ≠ Easy (Hickey)
**Simple** = not intertwined (objective, structural). **Easy** = familiar, nearby (subjective). These are orthogonal. A global mutable singleton is *easy* but deeply *complex*. A pure function with explicit arguments is *simple* but may feel *harder* to write. Prefer simple. Every intertwining you add is complexity debt that compounds. → See `references/complexity-and-simplicity.md`

### 3. Think Before Building (Lamport, Hickey)
State the problem in writing. Identify invariants and edge cases. Only then decide implementation. "To think, you have to write. If you're thinking without writing, you only think you're thinking." The best engineers spend disproportionate time *not coding*. → See `references/pre-design-and-expertise.md`

### 4. Design Is Economic (Fowler)
Good design pays off in weeks, not months (Design Stamina Hypothesis). Sacrificing internal quality slows you down almost immediately. The question "Do we have time for good design?" is always wrong. → See `references/decision-frameworks.md`

### 5. Accept Trade-offs Honestly (Kleppmann)
No technology is universally superior. Every choice trades X for Y. For any design decision: identify what it optimizes for, identify what it sacrifices, phrase it as "X at the cost of Y", then ask: in this context, is Y acceptable? Never recommend without stating the cost. → See `references/decision-frameworks.md`

### 6. Boundaries Are the Primary Design Tool
Deep modules with narrow interfaces (Ousterhout). Bounded contexts where a model is valid only within its boundary (Evans). Bulkheads that isolate failure domains (Nygard). Aggregates that enforce consistency boundaries (Cosmic Python). The Dependency Rule — domain never imports infrastructure. The primary act of architecture is drawing lines. → See `references/design-heuristics.md` and `references/architecture-patterns.md`

### 7. The Dependency Rule (Python-specific application of DIP)
High-level modules must not depend on low-level modules. In Python: domain / business logic **never imports** SQLAlchemy, Flask, Redis, or requests. Infrastructure **implements** interfaces the domain defines via `Protocol` or ABC. **Practical test:** Can you unit test your entire business logic without touching a database, network, or filesystem? If no, dependencies are inverted. → See `references/architecture-patterns.md`

---

## Question Classification → Reference Routing

### Pre-design / "Should I..." questions

| Question | Reference |
|---|---|
| "Should I use microservices or monolith?" | `decision-frameworks.md` §5 |
| "Should I use SQL or NoSQL?" | `decision-frameworks.md` §10 |
| "Should we add caching?" | `decision-frameworks.md` §9 |
| "Should I build or buy X?" | `decision-frameworks.md` §8 |
| "Sync or async for this?" | `decision-frameworks.md` §7 |
| "Strong or eventual consistency here?" | `decision-frameworks.md` §6 |
| "Is this accidental complexity?" | `complexity-and-simplicity.md` §1, §3 |
| "Is this the right abstraction?" | `complexity-and-simplicity.md` §2, `design-heuristics.md` §5 |
| "How do I decompose this domain?" | `design-heuristics.md` §8, §9 |
| "How do I start designing this?" | `pre-design-and-expertise.md` §1, §2 |
| "What constraints am I working within?" | `pre-design-and-expertise.md` §6 |

### Python architecture / structure questions

| Question | Reference |
|---|---|
| "How do I structure this project?" | `architecture-patterns.md` §16 |
| "Tests need a database to run" | `architecture-patterns.md` §4 (Repository + Fake) |
| "Business logic spread across routes" | `architecture-patterns.md` §5 (Service Layer) |
| "How do I handle domain events?" | `architecture-patterns.md` §8 |
| "Read queries are slow / complex" | `architecture-patterns.md` §9 (CQRS) |
| "Multiple infrastructure backends" | `architecture-patterns.md` §1 (Ports and Adapters) |
| "How do I inject dependencies in Python?" | `architecture-patterns.md` §10 |
| "How do I model this domain?" | `architecture-patterns.md` §3 (DDD) |
| "What consistency boundary do I need?" | `architecture-patterns.md` §7 (Aggregates) |
| "Data pipeline design" | `architecture-patterns.md` §11, §12 |
| "What's a deep module vs shallow module?" | `design-heuristics.md` §1 |
| "Failure modes for this service integration" | `design-heuristics.md` §10, `decision-frameworks.md` §7 |

### Python style / code review questions

| Question | Reference |
|---|---|
| "Is this Pythonic?" | `style-guide.md` §1 + anti-patterns §15 |
| "How do I name this?" | `style-guide.md` §3 |
| "Loop / comprehension style" | `style-guide.md` §4 |
| "Class vs function decision" | `style-guide.md` §7 |
| "Type annotation style" | `style-guide.md` §10 |
| "Error handling pattern" | `style-guide.md` §8 |
| "Performance bottleneck" | `style-guide.md` §11, `architecture-patterns.md` §14 |
| "Testing approach" | `style-guide.md` §12 |
| "Code review — is this good Python?" | `style-guide.md` §15 (anti-patterns checklist) |

---

## Quick-Reference Decision Trees

### Pattern Cost/Benefit

| Pattern | Pain it solves | Cost it adds |
|---|---|---|
| Repository + Fake | DB-free unit tests | Extra indirection |
| Service Layer | Testable use cases, thin views | More files, boilerplate |
| Unit of Work | Atomic transactions | Abstraction overhead |
| Domain Model (DDD) | Complex rules in one place | Classical ORM mapping |
| Aggregates | Consistency boundaries | One repo per root |
| Domain Events + Bus | Decoupled side effects | Eventual consistency, harder debugging |
| CQRS | Fast reads, no ORM overhead | Separate read/write paths |
| Hexagonal | Full unit coverage, swappable infra | Significant upfront structure |

**Rule:** Add a pattern when you feel the pain it solves, not before.

### When to Add an Abstraction Layer

```
Tests require a database?
  YES → Repository Pattern + Fake repository

Business logic scattered across routes?
  YES → Service Layer (one function per use case)

Hard to swap infrastructure (DB, queue, email)?
  YES → Ports and Adapters (Abstract classes / Protocols)

Changes in one area require changes in unrelated areas?
  YES → Domain Events + Message Bus

The same discussion has happened 3+ times?
  YES → Write it down first (pre-design-and-expertise.md §5)

Otherwise → Don't add the pattern yet
```

### Class vs Function

```
Has persistent state that evolves? → Class (Entity)
Immutable record of values?        → frozen dataclass (Value Object)
Single operation, no state?        → Function
Only __init__ + one method?        → Just a function (Diederich rule)
Related utilities, no shared state? → Module, not class
```

### Monolith vs Microservices (fast path)

```
"Teams stepping on each other"  → organizational problem, not technical
"One component needs 100x scale" → extract THAT component only
"Our codebase is a mess"        → microservices amplify mess, clean the monolith first
"Everyone else does it"         → not a reason

Default: monolith first. Extract when you understand boundaries empirically.
Distributed monolith (Hightower) = strictly worse than regular monolith.
```

→ Full analysis in `references/decision-frameworks.md` §5

### Python Concurrency

```
I/O-bound (network, disk) → asyncio + httpx/aiofiles
CPU-bound computation     → ProcessPoolExecutor (bypasses GIL)
Mixed CPU + I/O           → asyncio with run_in_executor for CPU parts
Parallel data processing  → multiprocessing.Pool with chunked data
```

→ Full implementation patterns in `references/architecture-patterns.md` §14

### Complexity Checklist (run before recommending any pattern)

```
□ Can a new engineer make this change without asking someone?    (unknown unknowns)
□ Does a single-concept change touch ≤ 3 files?                 (change amplification)
□ Can you explain the module's contract in one sentence?         (cognitive load)
□ Are the dependencies explicit and visible?                     (DIP compliance)
□ Could you replace this module without changing its consumers?  (coupling)
□ Is the naming accurate to what the thing actually does?        (cognitive load)
```

Three or more "no" answers = complexity problem worth addressing before adding patterns.

---

## Response Templates

### For architecture questions

1. **Restate the real problem** — What pain are they actually experiencing?
2. **Apply the relevant mental model(s)** — Which models from the Seven Pillars apply? Name them.
3. **State the trade-off explicitly** — "Option A gives X at the cost of Y. In your context, Y is acceptable because..."
4. **Show a concrete Python example** — Minimal, runnable, idiomatic
5. **State what NOT to do here** — Which patterns would be over-engineering for this context?
6. **Flag risks** — What would make this the wrong call? What would you monitor?

### For code review / style questions

1. **Identify the specific anti-pattern(s)** — Name them, cite the catalogue
2. **Explain WHY it's a problem** — Don't just say "wrong"; say what breaks
3. **Show the idiomatic replacement** — Side-by-side before/after
4. **Apply the foundational mindset** — Does the fix serve readability?

### Never say "it depends" without immediately saying what it depends ON and how to evaluate it.

---

## The "Don't Over-Architect" Rule

Always verify context before recommending a pattern. A CRUD app with no business logic doesn't need a service layer.

Apply full layered architecture when:
- Non-trivial business logic that changes frequently
- Multiple teams on the same codebase
- Need to swap infrastructure (migrate databases)
- Test speed matters — CI running 200+ tests against a real DB

Skip patterns for: CRUD APIs with minimal logic, data science scripts, prototypes, thin API wrappers.

```python
# This IS correct architecture for a simple CRUD app. Don't add layers.
@app.route('/users', methods=['POST'])
def create_user():
    data = request.json
    user = User(**data)
    db.session.add(user)
    db.session.commit()
    return {'id': user.id}, 201
```

---

## Reference Files Index

### `references/complexity-and-simplicity.md`
The theory of *why* systems get hard to change. Load for any question about abstraction, over-engineering, or "is this too complex?".
- §1 Essential vs Accidental Complexity (Brooks) — what complexity you can eliminate vs what you must partition
- §2 Simple vs Easy (Hickey) — the most important distinction in software design
- §3 Three Symptoms of Complexity (Ousterhout) — change amplification, cognitive load, unknown unknowns
- §4 Knowledge Degradation (Blow) — why technology degrades by default; build only what you can hand off
- §5 Complexity Detection Checklist — 8-question diagnostic for any system

### `references/decision-frameworks.md`
Trade-off analysis and specific architectural decisions. Load for any "should I use X or Y?" question.
- §1 Kleppmann's Trade-off Analysis — the universal framework: "X at the cost of Y"
- §2 Design Stamina Hypothesis (Fowler) — good design pays off in weeks; sacrificial architecture
- §3 Innovation Tokens (McKinley) — ~3 tokens per company; boring technology wins long-term
- §4 Performance vs Readability (Muratori) — profile first; hot paths only
- §5 Monolith vs Microservices — step-by-step decision, distributed monolith trap
- §6 Consistency vs Availability — per-subsystem segmentation; consistency models
- §7 Sync vs Async — heuristics + Nygard's stability patterns
- §8 Build vs Buy — 4-question framework
- §9 Caching Decisions — when to cache, invalidation patterns
- §10 Database Selection — PostgreSQL default, when to deviate

### `references/design-heuristics.md`
Daily rules-of-thumb for decomposition and failure thinking. Load when designing modules, splitting responsibilities, or thinking about failures.
- §1 Deep Modules (Ousterhout) — narrow interface, deep functionality; Unix file I/O as gold standard
- §2 Design It Twice — always generate two alternatives before committing
- §3 Four Rules of Simple Design (Beck) — passes tests → reveals intention → no duplication → fewest elements
- §4 Red Flags (Ousterhout) — shallow modules, information leakage, pass-through methods, temporal decomposition
- §5 Abstraction Lifecycle Warning (Abramov) — duplication is cheaper than wrong abstraction; rule of three
- §6 Data Dominates (Pike/Thompson) — redesign the data; algorithm becomes obvious
- §7 Good Taste (Torvalds) — eliminate special cases through better abstractions
- §8 Problem Decomposition — expert top-down process; when to split vs join
- §9 Bounded Contexts (Evans) — "Customer" means different things; anti-corruption layers
- §10 Failure Thinking (Nygard) — timeout every call; circuit breakers; bulkheads; failure math
- §11 Designing Around Impossibility (Helland) — no distributed transactions at scale; idempotence; immutability
- §12 Conway's Law — systems mirror org structure; inverse Conway maneuver

### `references/pre-design-and-expertise.md`
Thinking processes before writing code. Load for any question about how to start, how to spec, or how to build judgment.
- §1 Hammock-Driven Development (Hickey) — state → understand → research → load → background → analyze
- §2 Think Above the Code (Lamport) — specify behavior separately from implementation; identify invariants first
- §3 Mental Model Debugging (Pike/Thompson) — fix the model, not the symptom
- §4 Test-Driven Design (Beck) — never change behavior and structure simultaneously
- §5 Writing as Thinking — RFC, ADR, decision doc template; write before, not after
- §6 Constraint Identification (Hohpe) — hard vs soft vs ghost constraints; ghost constraints are the opportunity
- §7 Building Design Intuition (Norvig, Majors) — deliberate practice; production is the feedback loop
- §8 Expertise Progression — junior→mid→senior→staff mental shifts; AI era implications

### `references/architecture-patterns.md`
Python-specific pattern implementations with full code. Load for any "how do I implement X in Python?" question.
- §1–2 Foundations: DIP, Hexagonal, Layered Architecture (with before/after code)
- §3 Domain Modeling: Value Objects, Entities, Domain Services
- §4 Repository Pattern: Protocol, SQLAlchemy adapter, Fake (the testability payoff)
- §5 Service Layer: structure, primitives in/out, test examples
- §6 Unit of Work: abstract, SQLAlchemy, fake, context manager usage
- §7 Aggregate Pattern: consistency boundaries, one repo per root
- §8 Event-Driven Architecture: events, commands, message bus
- §9 CQRS: direct SQL reads, separate read models
- §10 Dependency Injection: constructor injection, bootstrap pattern
- §11 Pipeline Architecture (Montani): stateless callables, registry pattern
- §12 Composable Data Stack (McKinney): layered pandas, df.pipe(), Arrow
- §13 Class Architecture (Hettinger): template method, cooperative super(), __slots__
- §14 Performance-Aware Architecture (Shaw): GIL, asyncio pitfalls, pattern matching
- §15 API-First Architecture (Reitz): README-driven, simple defaults
- §16 Project Structure: canonical layout, import discipline, config from env
- §17–18 When NOT to apply patterns + Decision Matrix

### `references/style-guide.md`
Idiomatic Python with before/after examples. Load for code review, style questions, or "is this Pythonic?".
- §1 Foundational Mindset — readability first; explicit over implicit; one obvious way
- §2 Formatting: Black rules, imports, blank lines
- §3 Naming: all constructs, boolean names, no abbreviations
- §4 Iteration: enumerate, zip, comprehensions, for...else, simultaneous updates
- §5 Data Structures: dict patterns, deque, dataclass, vectorization
- §6 Functions: guard clauses, keyword args, generators, lru_cache
- §7 Classes: super(), dataclass, property, __slots__
- §8 Error Handling: specific exceptions, chaining, ExceptionGroup, context managers
- §9 Imports: pyproject.toml, __init__.py discipline
- §10 Type Annotations: modern syntax (3.10+), Protocol, TypeAlias, mypy --strict
- §11 Performance: O(1) vs O(n) table, match statements, itertools, no unnecessary list comps
- §12 Testing: pytest, parametrize, mock external calls, test behaviour not implementation
- §13 Library & API Design: README-driven, opinionated defaults, no hidden state
- §14 Toolchain: Black, isort, ruff, mypy, pytest, pre-commit, uv
- §15 Anti-Patterns Catalogue: 20-item quick-reference checklist
