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
| "Do we have time for good design?" | `decision-frameworks.md` §2 |
| "Should we adopt this new technology?" | `decision-frameworks.md` §3 |
| "Is this hot-path code worth optimizing?" | `decision-frameworks.md` §4 |
| "How do I get better at design?" | `pre-design-and-expertise.md` §7, §8 |
| "Should I write this up first?" | `pre-design-and-expertise.md` §5 |
| "How do I debug this effectively?" | `pre-design-and-expertise.md` §3 |
| "How do I test-drive this design?" | `pre-design-and-expertise.md` §4 |

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
| "Should I always generate alternatives?" | `design-heuristics.md` §2 |
| "What makes a good simple design?" | `design-heuristics.md` §3 |
| "What are red flags in a design?" | `design-heuristics.md` §4 |
| "When does data structure choice matter?" | `design-heuristics.md` §6 |
| "How do I eliminate special cases?" | `design-heuristics.md` §7 |
| "Failure modes for this service integration" | `design-heuristics.md` §10, `decision-frameworks.md` §7 |
| "Distributed transactions across services" | `design-heuristics.md` §11 |
| "Org structure affecting architecture" | `design-heuristics.md` §12 |
| "Layered architecture for this project" | `architecture-patterns.md` §2 |
| "Unit of Work / atomic transactions" | `architecture-patterns.md` §6 |
| "Class design: MRO, super(), __slots__" | `architecture-patterns.md` §13 |
| "README-driven / API-first design" | `architecture-patterns.md` §15 |
| "When to skip patterns / CRUD apps" | `architecture-patterns.md` §17, §18 |

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
| "How do I format this code?" | `style-guide.md` §2 |
| "Which data structure should I use?" | `style-guide.md` §5 |
| "Function design / guard clauses / generators" | `style-guide.md` §6 |
| "Imports and project layout" | `style-guide.md` §9 |
| "Library / API design patterns" | `style-guide.md` §13 |
| "What tooling should I use?" | `style-guide.md` §14 |

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

→ Use the 8-question diagnostic in `references/complexity-and-simplicity.md` §5. Three or more "no" answers = complexity problem worth addressing before adding patterns.

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

---

# Bundled References

---

# Python Architecture Patterns Reference
> Distilled from Cosmic Python (Percival & Gregory), Guido van Rossum, Raymond Hettinger, Wes McKinney, Ines Montani, Anthony Shaw, Kenneth Reitz

---

## 1. Architectural Foundations

### Dependency Inversion Principle

High-level modules must not depend on low-level modules. Both should depend on abstractions.

```
Wrong: BusinessLogic → imports → SQLAlchemy / Flask
Right: BusinessLogic → imports → AbstractRepository (Protocol)
       SQLAlchemy    → implements → AbstractRepository
```

**Test:** Can you unit test your entire business logic without a database, network, or filesystem? If not, dependencies are inverted.

### Ports and Adapters (Hexagonal)

```
┌─────────────────────────────────────┐
│             Adapters                │
│  ┌───────────────────────────────┐  │
│  │         Service Layer         │  │
│  │  ┌─────────────────────────┐  │  │
│  │  │  Domain (pure Python)   │  │  │
│  │  └─────────────────────────┘  │  │
│  └───────────────────────────────┘  │
│  Flask | CLI | Celery | Tests        │
└─────────────────────────────────────┘
        ↕              ↕
    Database         Redis/Queue
```

### Test Pyramid

Target: many fast unit tests (no I/O), some integration tests, few E2E tests. If most tests need a database, the architecture is wrong.

---

## 2. Layered Architecture

```
Presentation Layer  — Flask routes, CLI, Celery tasks. Thin. No business logic.
Service Layer       — Use case orchestration.
Domain Layer        — Business logic, rules, invariants. Pure Python, no I/O.
Infrastructure      — SQLAlchemy, Redis, S3. Implements domain ports.
```

```python
# Wrong — business logic + infrastructure in Flask route
@app.route('/orders', methods=['POST'])
def create_order():
    data = request.json
    if data['quantity'] <= 0:           # business rule leaking up
        return {'error': 'bad qty'}, 400
    order = Order(data['sku'], data['qty'])
    db.session.add(order)               # infrastructure leaking up
    db.session.commit()
    return {'id': order.id}, 201

# Right — thin presentation layer
@app.route('/orders', methods=['POST'])
def create_order():
    data = request.json
    try:
        order_id = services.create_order(sku=data['sku'], qty=data['qty'], uow=SqlAlchemyUnitOfWork())
        return {'id': order_id}, 201
    except InvalidQuantity as e:
        return {'error': str(e)}, 400
```

```python
# Wrong — domain model imports SQLAlchemy
from sqlalchemy import Column, Integer, String
from myapp.database import Base

class Order(Base):
    __tablename__ = 'orders'
    id = Column(Integer, primary_key=True)

# Right — pure Python domain model
from dataclasses import dataclass, field

@dataclass
class Order:
    order_id: str
    lines: list['OrderLine'] = field(default_factory=list)

    def add_line(self, line: 'OrderLine') -> None:
        if any(l.sku == line.sku for l in self.lines):
            raise DuplicateLineError(f"SKU {line.sku} already in order")
        self.lines.append(line)
```

---

## 3. Domain Modeling (DDD)

**Domain-Driven Design (DDD)** is Eric Evans' approach to software design that places the business domain at the center of architecture. The core idea: build a shared model between developers and domain experts, expressed directly in code. DDD provides tactical building blocks (Value Objects, Entities, Aggregates, Domain Services, Domain Events) and strategic patterns (Bounded Contexts, Context Maps) for managing complexity in systems with rich business rules.

### Value Objects — immutable, equality by value

```python
from dataclasses import dataclass

@dataclass(frozen=True)
class Money:
    amount: int      # store as pence/cents — no floats for money
    currency: str

    def __add__(self, other: 'Money') -> 'Money':
        if self.currency != other.currency:
            raise CurrencyMismatch(self.currency, other.currency)
        return Money(self.amount + other.amount, self.currency)

@dataclass(frozen=True)
class OrderReference:
    value: str

    def __post_init__(self):
        if not self.value.startswith('ORD-'):
            raise ValueError(f"Invalid order reference: {self.value}")

assert Money(100, 'GBP') == Money(100, 'GBP')
assert Money(100, 'GBP') + Money(50, 'GBP') == Money(150, 'GBP')
```

Use Value Object instead of a primitive when a string/int carries domain meaning (`OrderId`, `EmailAddress`, `Price`) or when you want validation at construction time.

### Entities — identity by ID

```python
from dataclasses import dataclass, field
from datetime import date

@dataclass
class Batch:
    reference: str
    sku: str
    purchased_qty: int
    eta: date | None = None
    _allocations: set['OrderLine'] = field(default_factory=set, repr=False)

    def __eq__(self, other: object) -> bool:
        if not isinstance(other, Batch): return False
        return self.reference == other.reference   # identity by reference

    def __hash__(self) -> int:
        return hash(self.reference)

    def allocate(self, line: 'OrderLine') -> None:
        if not self.can_allocate(line):
            raise OutOfStock(f"Cannot allocate {line.sku}")
        self._allocations.add(line)

    def can_allocate(self, line: 'OrderLine') -> bool:
        return self.sku == line.sku and self.available_quantity >= line.qty

    @property
    def available_quantity(self) -> int:
        return self.purchased_qty - sum(line.qty for line in self._allocations)
```

### Domain Services — stateless functions on domain objects

```python
def allocate(line: 'OrderLine', batches: list[Batch]) -> str:
    sorted_batches = sorted(batches, key=lambda b: (b.eta is None, b.eta))
    try:
        batch = next(b for b in sorted_batches if b.can_allocate(line))
    except StopIteration:
        raise OutOfStock(f"Out of stock for SKU {line.sku}")
    batch.allocate(line)
    return batch.reference
```

---

## 4. Repository Pattern

### Abstract port (Protocol — no inheritance required)

```python
from typing import Protocol

class BatchRepository(Protocol):
    def add(self, batch: Batch) -> None: ...
    def get(self, reference: str) -> Batch | None: ...
    def list(self) -> list[Batch]: ...
```

### Real adapter

```python
class SqlAlchemyBatchRepository:
    def __init__(self, session):
        self.session = session

    def add(self, batch: Batch) -> None:
        self.session.add(batch)

    def get(self, reference: str) -> Batch | None:
        return self.session.query(Batch).filter_by(reference=reference).first()

    def list(self) -> list[Batch]:
        return self.session.query(Batch).all()
```

### Fake for tests (no DB required)

```python
class FakeBatchRepository:
    def __init__(self, batches: list[Batch] | None = None):
        self._batches = list(batches or [])

    def add(self, batch: Batch) -> None:
        self._batches.append(batch)

    def get(self, reference: str) -> Batch | None:
        return next((b for b in self._batches if b.reference == reference), None)

    def list(self) -> list[Batch]:
        return list(self._batches)
```

### Classical ORM mapping — domain stays clean

> **Note (SQLAlchemy 2.0+):** The imperative mapping below still works but is legacy. SQLAlchemy 2.0+ recommends `Mapped` and `mapped_column()` with `registry.map_imperatively()` or the new declarative style. However, the *principle* remains the same: domain model stays pure, infrastructure maps to it.

```python
# orm.py — infrastructure imports domain (not the other way around)
from myapp.domain.model import Batch, OrderLine

# Legacy (SQLAlchemy 1.x / compatibility)
def start_mappers():
    mapper_registry.map_imperatively(OrderLine, order_lines)
    mapper_registry.map_imperatively(
        Batch, batches,
        properties={'_allocations': relationship(OrderLine)},
    )

# Modern (SQLAlchemy 2.0+) — same principle, new API
from sqlalchemy.orm import Mapped, mapped_column, relationship, registry

mapper_registry = registry()

@mapper_registry.mapped
class BatchDTO:
    __tablename__ = 'batches'
    id: Mapped[int] = mapped_column(primary_key=True)
    reference: Mapped[str]
    sku: Mapped[str]
    purchased_qty: Mapped[int]
    _allocations: Mapped[list["OrderLineDTO"]] = relationship()
```

---

## 5. Service Layer Pattern

Shape of every service function:
1. Fetch from repository
2. Validate pre-conditions
3. Call domain logic
4. Commit via UoW
5. Return a primitive (not a domain object)

```python
def allocate(orderid: str, sku: str, qty: int, uow) -> str:
    with uow:
        product = uow.products.get(sku)
        if product is None:
            raise InvalidSku(f"Invalid sku {sku}")
        batchref = model.allocate(OrderLine(orderid, sku, qty), product.batches)
        uow.commit()
    return batchref

def add_batch(ref: str, sku: str, qty: int, eta, uow) -> None:
    with uow:
        product = uow.products.get(sku)
        if product is None:
            uow.products.add(model.Product(sku=sku, batches=[]))
            product = uow.products.get(sku)
        product.batches.append(model.Batch(ref, sku, qty, eta))
        uow.commit()
```

Always pass primitives in, not domain objects:
```python
# Wrong — leaks domain objects to the caller
def allocate(line: OrderLine, uow): ...

# Right — primitive inputs only
def allocate(orderid: str, sku: str, qty: int, uow): ...
```

Test with fakes:
```python
def test_allocate_returns_allocation():
    uow = FakeUnitOfWork()
    services.add_batch("batch1", "SMALL-TABLE", 20, None, uow)
    result = services.allocate("o1", "SMALL-TABLE", 10, uow)
    assert result == "batch1"

def test_allocate_raises_error_for_invalid_sku():
    uow = FakeUnitOfWork()
    with pytest.raises(InvalidSku, match="Invalid sku DOESNOTEXIST"):
        services.allocate("o1", "DOESNOTEXIST", 10, uow)
```

---

## 6. Unit of Work Pattern

### Abstract

```python
from abc import ABC, abstractmethod
from typing import Self

class AbstractUnitOfWork(ABC):
    products: 'AbstractProductRepository'

    def __enter__(self) -> Self: return self
    def __exit__(self, *args) -> None: self.rollback()

    @abstractmethod
    def commit(self) -> None: ...

    @abstractmethod
    def rollback(self) -> None: ...
```

### SQLAlchemy

```python
class SqlAlchemyUnitOfWork(AbstractUnitOfWork):
    def __init__(self, session_factory=DEFAULT_SESSION_FACTORY):
        self.session_factory = session_factory

    def __enter__(self):
        self.session = self.session_factory()
        self.products = SqlAlchemyProductRepository(self.session)
        return super().__enter__()

    def __exit__(self, *args):
        super().__exit__(*args)
        self.session.close()

    def commit(self): self.session.commit()
    def rollback(self): self.session.rollback()
```

### Fake

```python
class FakeUnitOfWork(AbstractUnitOfWork):
    def __init__(self):
        self.products = FakeProductRepository([])
        self.committed = False

    def commit(self): self.committed = True
    def rollback(self): pass
```

Usage — nothing is saved until you explicitly commit:
```python
def allocate(orderid: str, sku: str, qty: int, uow) -> str:
    with uow:
        product = uow.products.get(sku)
        batchref = model.allocate(OrderLine(orderid, sku, qty), product.batches)
        uow.commit()
    return batchref
    # __exit__ calls rollback if exception prevented commit
```

---

## 7. Aggregate Pattern

All changes to objects within an aggregate go through the root.

```python
@dataclass
class Product:
    sku: str
    batches: list[Batch] = field(default_factory=list)
    version_number: int = 0        # for optimistic concurrency
    events: list = field(default_factory=list, repr=False)

    def allocate(self, line: 'OrderLine') -> str:
        try:
            batch = next(
                b for b in sorted(self.batches, key=lambda b: (b.eta is None, b.eta))
                if b.can_allocate(line)
            )
        except StopIteration:
            raise OutOfStock(f"Out of stock for SKU {self.sku}")
        batch.allocate(line)
        self.version_number += 1
        self.events.append(events.Allocated(line.orderid, self.sku, line.qty, batch.reference))
        return batch.reference
```

One repository per aggregate root (not per table):
```python
# Right — one repo for the aggregate root
class ProductRepository(Protocol):
    def get(self, sku: str) -> Product | None: ...
    def add(self, product: Product) -> None: ...

# Wrong — Batch and OrderLine are not roots
class BatchRepository(Protocol): ...
```

---

## 8. Event-Driven Architecture

### Domain events as frozen dataclasses

```python
from dataclasses import dataclass

@dataclass(frozen=True)
class Allocated:
    orderid: str
    sku: str
    qty: int
    batchref: str

@dataclass(frozen=True)
class OutOfStock:
    sku: str
```

### Commands vs Events

| | Command | Event |
|---|---|---|
| Named | Imperative: `Allocate` | Past tense: `Allocated` |
| Recipients | One handler (can reject) | Zero or more (cannot reject) |
| On failure | Raises exception | Handled in background |
| Example | `PlaceOrder(orderid, sku, qty)` | `OrderPlaced(orderid, sku, qty)` |

### Message bus

```python
class MessageBus:
    def __init__(self, uow, event_handlers, command_handlers):
        self.uow = uow
        self.event_handlers = event_handlers
        self.command_handlers = command_handlers

    def handle(self, message) -> list:
        results = []
        queue = [message]
        while queue:
            msg = queue.pop(0)
            if isinstance(msg, events.Event):
                for handler in self.event_handlers.get(type(msg), []):
                    handler(msg)
                    queue.extend(self.uow.collect_new_events())
            elif isinstance(msg, commands.Command):
                results.append(self.command_handlers[type(msg)](msg))
                queue.extend(self.uow.collect_new_events())
        return results
```

---

## 9. CQRS

**Domain models are for writing. Use simple SQL for reading.**

```python
# Wrong — ORM for reads (loads full aggregate, N+1 risk)
def get_allocations(orderid: str, uow) -> list[dict]:
    with uow:
        product = uow.products.get_by_order(orderid)
        # ... lots of ORM traversal ...

# Right — direct SQL for reads (fast, no overhead)
def get_allocations(orderid: str, session) -> list[dict]:
    rows = session.execute(
        text("""
            SELECT ol.sku, b.reference as batchref
            FROM order_lines ol
            JOIN allocations a ON a.orderline_id = ol.id
            JOIN batches b ON a.batch_id = b.id
            WHERE ol.orderid = :orderid
        """),
        {'orderid': orderid},
    )
    return [dict(row) for row in rows]
```

Maintain separate read models updated by event handlers for frequently-read denormalized data:
```python
def update_allocation_view(event: events.Allocated, session):
    session.execute(
        text("INSERT INTO allocations_view (orderid, sku, batchref) VALUES (:orderid, :sku, :batchref)"),
        {'orderid': event.orderid, 'sku': event.sku, 'batchref': event.batchref},
    )
```

---

## 10. Dependency Injection

### Constructor injection

```python
# Wrong — hidden dependency
class OrderService:
    def allocate(self, orderid, sku, qty):
        session = db.get_session()           # module-level global
        repo = SqlAlchemyRepository(session) # hard-coded adapter

# Right — explicit injection
class OrderService:
    def __init__(self, uow: AbstractUnitOfWork):
        self.uow = uow
```

### Bootstrap at the edge — wire up concretions only at entry points

```python
def bootstrap(start_orm=True, uow=None, send_mail=None, publish=None) -> MessageBus:
    if start_orm: orm.start_mappers()
    uow = uow or SqlAlchemyUnitOfWork()
    send_mail = send_mail or email.send
    publish = publish or redis_eventpublisher.publish

    return MessageBus(
        uow=uow,
        event_handlers={
            events.OutOfStock: [lambda e: handlers.send_out_of_stock_notification(e, send_mail)],
        },
        command_handlers={
            commands.Allocate: lambda c: handlers.allocate(c, uow),
        },
    )

# Test bootstrap — fakes all infrastructure
def bootstrap_test():
    return bootstrap(start_orm=False, uow=FakeUnitOfWork(), send_mail=Mock(), publish=Mock())
```

---

## 11. Pipeline Architecture (Ines Montani)

```python
from dataclasses import dataclass, field
from typing import Callable

@dataclass
class Doc:
    text: str
    tokens: list[str] = field(default_factory=list)
    entities: list[dict] = field(default_factory=list)

class Pipeline:
    def __init__(self, components: list[tuple[str, Callable]]):
        self._components = components

    def __call__(self, text: str) -> Doc:
        doc = Doc(text=text)
        for name, component in self._components:
            doc = component(doc)
        return doc
```

Registry pattern for config-driven swappable components:
```python
_registry: dict[str, Callable] = {}

def register(name: str):
    def decorator(fn):
        _registry[name] = fn
        return fn
    return decorator

@register('ner.v2')
def ner_v2(doc: Doc) -> Doc: ...

config = {'ner': 'ner.v2'}
pipeline = Pipeline([(name, _registry[fn]) for name, fn in config.items()])
```

Build bottom-up — pass instances, not config dicts:
```python
# Wrong — top-down config (buries defaults, hard to debug)
def create_model(config: dict) -> Model: ...

# Right — caller composes objects
model = create_model(layers=[create_layer(width=128) for _ in range(3)])
```

---

## 12. Composable Data Stack (Wes McKinney)

```
Query/API    → Ibis, SQL, pandas
Execution    → DuckDB, Polars, Spark (swappable)
Interchange  → Apache Arrow (zero-copy columnar)
Storage      → Parquet, Iceberg, CSV
```

Layer explicitly:
```python
# Wrong — all layers tangled together
def run_analysis(filepath: str) -> dict:
    df = pd.read_csv(filepath)
    df = df[df['revenue'] > 0]
    df['margin'] = df['profit'] / df['revenue']
    return df.groupby('region')['margin'].mean().to_dict()

# Right — explicit, independently testable layers
def load(filepath: str) -> pd.DataFrame: return pd.read_csv(filepath)
def clean(df: pd.DataFrame) -> pd.DataFrame: return df[df['revenue'] > 0].copy()
def compute_margins(df: pd.DataFrame) -> pd.DataFrame:
    df = df.copy()
    df['margin'] = df['profit'] / df['revenue']
    return df

def run_analysis(filepath: str) -> dict:
    return (
        load(filepath)
        .pipe(clean)
        .pipe(compute_margins)
        .groupby('region')['margin']
        .mean()
        .to_dict()
    )
```

Use `df.pipe()` for readable chains:
```python
result = (
    raw_df
    .pipe(remove_nulls, cols=['revenue', 'units'])
    .pipe(normalise_strings, col='region')
    .pipe(add_calculated_field)
)
```

---

## 13. Class Architecture Patterns (Raymond Hettinger)

### Template Method

```python
class DataProcessor:
    def run(self, source: str) -> list:
        raw = self.fetch(source)
        cleaned = self.clean(raw)
        return self.transform(cleaned)

    def fetch(self, source: str) -> list: raise NotImplementedError
    def clean(self, data: list) -> list: return [x for x in data if x is not None]
    def transform(self, data: list) -> list: raise NotImplementedError

class CsvProcessor(DataProcessor):
    def fetch(self, source: str) -> list:
        with open(source) as f:
            return list(csv.reader(f))
    def transform(self, data: list) -> list:
        return [{'name': row[0], 'value': int(row[1])} for row in data[1:]]
```

### Cooperative multiple inheritance — `super()` walks the MRO, not just the parent

```python
class LoggingMixin:
    def save(self, *args, **kwargs):
        print(f"Saving {self.__class__.__name__}")
        return super().save(*args, **kwargs)   # passes call along the chain

class TimestampMixin:
    def save(self, *args, **kwargs):
        self.updated_at = datetime.utcnow()
        return super().save(*args, **kwargs)

# MRO: TimestampedLoggingModel → LoggingMixin → TimestampMixin → BaseModel
class TimestampedLoggingModel(LoggingMixin, TimestampMixin, BaseModel):
    pass
```

### `__slots__` for flyweight pattern

```python
class Token:
    __slots__ = ('text', 'pos', 'ner_tag')

    def __init__(self, text: str, pos: str, ner_tag: str | None = None):
        self.text = text
        self.pos = pos
        self.ner_tag = ner_tag
```

Without `__slots__`: ~200+ bytes per instance. With: ~40-80 bytes. Critical when processing millions of objects.

---

## 14. Performance-Aware Architecture (Anthony Shaw)

### GIL and concurrency

| Workload | Tool | Why |
|---|---|---|
| CPU-bound | `ProcessPoolExecutor` | Each process has its own GIL |
| I/O-bound | `asyncio` / `ThreadPoolExecutor` | GIL released during I/O |
| Mixed | `asyncio` + `run_in_executor` for CPU | Best of both |

```python
# I/O-bound — asyncio
async def fetch_all(urls: list[str]) -> list:
    async with httpx.AsyncClient() as client:
        return await asyncio.gather(*[client.get(url) for url in urls])

# CPU-bound — ProcessPoolExecutor
def parallel_compute(all_data: list) -> list:
    chunks = [all_data[i::4] for i in range(4)]
    with ProcessPoolExecutor(max_workers=4) as pool:
        results = list(pool.map(compute_heavy, chunks))
    return [item for chunk in results for item in chunk]
```

asyncio pitfalls:
```python
# Wrong — blocking call inside async (freezes all coroutines)
async def get_user(user_id: int):
    response = requests.get(f'/users/{user_id}')  # BLOCKS event loop

# Right
async def get_user(user_id: int):
    async with httpx.AsyncClient() as client:
        return (await client.get(f'/users/{user_id}')).json()

# Wrong — sequential when should be concurrent
async def fetch_dashboard():
    users = await get_users()
    orders = await get_orders()

# Right
async def fetch_dashboard():
    users, orders = await asyncio.gather(get_users(), get_orders())
```

### Structural pattern matching for dispatch (faster than isinstance chains)

```python
def handle_event(event):
    match event:
        case UserCreated(user_id=uid, email=email):
            on_user_created(uid, email)
        case OrderPlaced(order_id=oid):
            on_order_placed(oid)
        case _:
            raise UnknownEventError(type(event))
```

---

## 15. API-First Architecture (Kenneth Reitz)

Write the call site before the implementation. If you can't express it simply, the design isn't ready.

- Simple case = one line
- Advanced case = one explicit object

```python
# Simple (90% of use cases)
response = requests.get('https://api.example.com/users')

# Advanced (explicit session, full control)
with requests.Session() as session:
    session.headers.update({'Authorization': f'Bearer {token}'})
    session.mount('https://', HTTPAdapter(max_retries=3))
    response = session.get(url)
```

---

## 16. Project Structure

```
my_project/
├── pyproject.toml
├── src/
│   └── allocation/
│       ├── domain/             # pure Python — zero infrastructure imports
│       │   ├── model.py        # Entities, Value Objects, Aggregates
│       │   ├── events.py       # frozen dataclasses
│       │   ├── commands.py     # frozen dataclasses
│       │   └── exceptions.py
│       ├── service_layer/
│       │   ├── handlers.py
│       │   ├── messagebus.py
│       │   └── unit_of_work.py
│       ├── adapters/
│       │   ├── orm.py          # SQLAlchemy mappers (imports domain)
│       │   ├── repository.py
│       │   └── redis_pub.py
│       └── entrypoints/
│           ├── flask_app.py
│           └── redis_consumer.py
└── tests/
    ├── unit/           # no I/O, uses FakeUoW
    ├── integration/    # real infrastructure
    └── e2e/
```

Import discipline — enforce with `import-linter` in CI:
```
Legal:
  entrypoints   → service_layer, adapters
  adapters      → domain, service_layer
  service_layer → domain
  domain        → (nothing from this project)

Illegal:
  domain ← service_layer  (domain must not know about services)
  domain ← adapters       (domain must not know about SQLAlchemy)
  service_layer ← entrypoints
```

Config from environment:
```python
def get_postgres_uri() -> str:
    host = os.environ.get('DB_HOST', 'localhost')
    port = os.environ.get('DB_PORT', '5432')
    name = os.environ.get('DB_NAME', 'allocations')
    user = os.environ.get('DB_USER', 'allocation')
    password = os.environ.get('DB_PASSWORD', 'abc123')
    return f'postgresql://{user}:{password}@{host}:{port}/{name}'
```

---

## 17. When NOT to Apply These Patterns

Apply when:
- Non-trivial business logic that changes frequently
- Multiple teams on same codebase
- Need to swap infrastructure
- Test speed matters (slow CI from DB calls)

Skip for CRUD APIs, data science scripts, prototypes, thin API wrappers:
```python
# This IS correct architecture for a simple CRUD app
@app.route('/users', methods=['POST'])
def create_user():
    data = request.json
    user = User(**data)
    db.session.add(user)
    db.session.commit()
    return {'id': user.id}, 201
```

---

## 18. Pattern Decision Matrix

| Situation | Pattern to reach for |
|---|---|
| Business logic growing complex | Domain Model |
| Tests need a database | Repository + Fake |
| Use cases scattered across views | Service Layer |
| Need atomic multi-step operations | Unit of Work |
| Cross-aggregate consistency | Domain Events + Message Bus |
| Slow/complex read queries | CQRS — direct SQL reads |
| Hard-to-test constructors | DI + Bootstrap |
| Multiple infrastructure backends | Ports and Adapters |
| Sequence of data transforms | Pipeline |
| I/O-bound concurrency | asyncio + gather |
| CPU-bound parallelism | ProcessPoolExecutor |
| Config-driven behaviour | Registry pattern |
| Large in-memory datasets | Arrow columnar format |
| API too complex | README-Driven Development |
| Simple CRUD | Don't add patterns — use ORM directly |

---

# Complexity and Simplicity

Deep reference material for complexity-related mental models.

## Table of Contents
1. Essential vs Accidental Complexity (Brooks)
2. Simple vs Easy (Hickey)
3. Three Symptoms of Complexity (Ousterhout)
4. Knowledge Degradation (Blow)
5. Complexity Detection Checklist

---

## 1. Essential vs Accidental Complexity (Brooks, 1986)

From "No Silver Bullet — Essence and Accident in Software Engineering."

**Essential complexity** is inherent in the problem domain. If users need 30 features, if the
business rules are intricate, if the physics of the domain are hard — that's essential. You can
partition it (divide it into pieces), but you cannot eliminate it.

**Accidental complexity** is self-inflicted through tools, poor decisions, premature abstraction,
wrong technology choices, organizational dysfunction. This is your target.

**Your job as an architect:** reduce accidental complexity ruthlessly. When you encounter
complexity, always ask: "Is this inherent in the problem, or did we create it?"

```python
# Accidental complexity — ORM ceremony for a simple lookup
class UserRepository:
    def __init__(self, session_factory):
        self._session_factory = session_factory

    def get_active_users(self):
        with self._session_factory() as session:
            return session.query(UserModel).filter(UserModel.active == True).all()

# Essential complexity only — if you don't need the abstraction yet
def get_active_users(db) -> list[dict]:
    return db.execute(text("SELECT * FROM users WHERE active")).mappings().all()
```

Brooks' four essential difficulties of software:
- **Complexity** — no two parts are alike (unlike physics, no repeating patterns to exploit)
- **Conformity** — must conform to human institutions that follow no pattern
- **Changeability** — software is pressured to change because it CAN be changed
- **Invisibility** — software has no natural geometric representation

Brooks' conclusion: there is no single technology that will deliver an order-of-magnitude
improvement. Progress comes from attacking accidental complexity on multiple fronts.

---

## 2. Simple vs Easy (Hickey, 2011)

From "Simple Made Easy," Strange Loop 2011. One of the most influential software talks ever given.

### Definitions

**Simple** (Latin: *simplex*, one fold/braid):
- Not intertwined
- One role, one task, one concept, one dimension
- Objective and structural — things are either braided together or they aren't
- The opposite of complex (many folds)

**Easy** (Latin: *adjacens*, lying nearby):
- Familiar, convenient, at hand
- Subjective and personal — depends on your background
- The opposite of hard

These are orthogonal dimensions. Something can be simple AND hard. Something can be easy AND complex.

### The Complecting Diagnosis

For any construct, ask: *what does it braid together?*

| Construct | What It Complects |
|-----------|------------------|
| State | Everything it touches |
| Objects | State + identity + value |
| Variables | Value + time |
| Inheritance | Types |
| Switch/matching | Who + what, closed |
| Syntax | Meaning + order |
| Loops | What + how |
| Actors | What + who |
| ORM | In-memory + on-disk |
| Conditionals | Flow + context |

### Hickey's Simpler Alternatives

| Complex Construct | Simpler Alternative |
|-------------------|-------------------|
| State, objects | Values (immutable) |
| Methods | Functions, namespaces |
| Variables | Managed references |
| Inheritance | Maps, polymorphism via protocols |
| Switch/matching | Polymorphism, pattern dispatch |
| Syntax | Data |
| Imperative loops | Set functions, reduce, iterators |
| Actors | Queues |
| ORM | Declarative data manipulation |
| Conditionals | Rules, pattern matching |

### The Speed Trap

Hickey's running metaphor: a sprinter who runs maximum speed from the start and "solves" fatigue
by calling every 100 yards a new sprint. Complexity debt works the same way — you feel fast early,
then pay compounding interest. Ignoring complexity guarantees slowdown. There is no amount of
testing or type checking that compensates for a system where things are complected.

```python
# Complex — state braided with identity, value, and time
class Order:
    _all_orders = {}  # global mutable state — complects everything

    def __init__(self, order_id, items):
        self.order_id = order_id
        self.items = items  # mutable — complects value with time
        Order._all_orders[order_id] = self  # complects identity with global state

# Simple — values separated from identity and state
@dataclass(frozen=True)
class OrderLine:
    sku: str
    qty: int

@dataclass(frozen=True)
class Order:
    order_id: str
    lines: tuple[OrderLine, ...]  # immutable value — no hidden state
```

### Key Quotes
- "Simplicity is a prerequisite for reliability." (Dijkstra, quoted by Hickey)
- "Programming is not about typing, it's about thinking."
- "We can only consider a few things at a time. Intertwined things must be considered together.
  Every intertwining adds burden."
- "Simplicity is a choice. It requires vigilance and work."

---

## 3. Three Symptoms of Complexity (Ousterhout, 2018)

From "A Philosophy of Software Design."

Detect complexity through its symptoms, not its causes:

### Symptom 1: Change Amplification
A simple change requires touching many places. If adding a new status to an order requires
changing 12 files, the abstraction is wrong. The fix is usually a better information-hiding
boundary.

### Symptom 2: Cognitive Load
Developers must hold too much context to make a change safely. This is NOT the same as "lines of
code." A 300-line function with a simple signature and clear flow has lower cognitive load than
10 tiny functions with complex interactions between them.

```python
# High cognitive load — must understand 4 modules and their interactions
def process_order(order_id):
    order = _cache.get(order_id) or _repo.find(order_id)
    _validator.validate(order, _config.rules)  # what rules? side effects?
    _enricher.enrich(order, _external_api)      # mutates order? can fail?
    _repo.save(order)
    _cache.invalidate(order_id)

# Lower cognitive load — explicit, self-contained
def process_order(order_id: str, repo: OrderRepository) -> Order:
    order = repo.get(order_id)
    validated = validate_order(order)  # pure function, returns new value
    repo.save(validated)
    return validated
```

### Symptom 3: Unknown Unknowns
You don't know what you need to change. This is the worst symptom because you can't even estimate
the work. The antidote is making dependencies explicit — through documentation, clear interfaces,
or type systems.

### Ousterhout's Mathematical Definition
A system's complexity = sum of (complexity of each part × how often developers interact with it).
This means complexity in rarely-touched code is less costly than complexity in hot paths.

### The Incremental Accumulation Principle
"Complexity isn't caused by a single catastrophic error; it accumulates in lots of small chunks."
Each shortcut adds a tiny amount. Individually harmless. Collectively lethal. This is why
discipline on small decisions matters more than grand architectural visions.

### Strategic vs Tactical Programming
- **Tactical:** "Just make it work." Produces working code fast. Accumulates complexity.
- **Strategic:** "Produce a great design that also works." Invests 10-20% extra time upfront.
  Pays back within weeks (connects to Fowler's Design Stamina).

---

## 4. Knowledge Degradation (Blow, 2019)

Jonathan Blow's argument: technology does not automatically improve. It degrades by default.

**The mechanism:**
- Every abstraction layer adds weight
- Knowledge transfer between generations is lossy
- People build on top of systems they don't fully understand
- The systems beneath accumulate cruft nobody can maintain
- Eventually, the tower collapses or must be rewritten from scratch

**The implication:**
The amount of complexity a system can sustain long-term is LESS than what an individual can
build today. Build only what you can: maintain, explain to someone else, hand off cleanly.

**Connection to Brooks:** This is accidental complexity accumulating across time, not just within
a project but across generations of engineers.

**Connection to Hickey:** Every "easy" library you adopt without understanding its internals
adds to the degradation debt.

---

## 5. Complexity Detection Checklist

When reviewing any system or design, run through these questions:

```
□ Can a new team member make a change without asking someone?        (unknown unknowns)
□ Does a single-concept change touch ≤ 3 files?                      (change amplification)
□ Can you explain the module's contract in one sentence?              (cognitive load)
□ Is the interface smaller than the implementation?                   (deep vs shallow)
□ Are the dependencies explicit and visible?                          (unknown unknowns)
□ Does each module hide a design decision?                            (information hiding)
□ Could you replace this module without changing its consumers?       (coupling)
□ Is the naming accurate to what the thing actually does?             (cognitive load)
```

If you answer "no" to 3+ of these, the system has a complexity problem worth addressing.

---

## Anti-Patterns: Complexity Traps

Common pitfalls that introduce accidental complexity:

- **Premature abstraction** — abstracting before you have three concrete instances; wrong abstractions are worse than duplication
- **Leaky abstraction** — hiding complexity that consumers must still understand to use correctly
- **Accidental coupling** — sharing code between unrelated concerns because it looks similar
- **Over-engineering** — adding patterns (DI, events, CQRS) before feeling the pain they solve
- **Configuration over convention** — making everything configurable instead of picking good defaults
- **Indirection for indirection's sake** — layers that pass through without adding value

```python
# Over-engineering — unnecessary abstraction layers
class AbstractUserFetcher(ABC): ...
class UserFetcher(AbstractUserFetcher): ...
class UserFetcherFactory:
    def create(self) -> AbstractUserFetcher: return UserFetcher()
class UserService:
    def __init__(self, factory: UserFetcherFactory):
        self._fetcher = factory.create()

# Just write the function
def get_user(user_id: int, db) -> User | None:
    return db.execute(text("SELECT * FROM users WHERE id = :id"), {"id": user_id}).first()
```

---

# Decision Frameworks

Deep reference for trade-off analysis and specific architectural decisions.

## Table of Contents
1. Kleppmann's Trade-off Analysis
2. Design Stamina Hypothesis (Fowler)
3. Innovation Tokens (McKinley)
4. Performance vs Readability (Muratori)
5. Monolith vs Microservices (detailed)
6. Consistency vs Availability (detailed)
7. Sync vs Async (detailed)
8. Build vs Buy (detailed)
9. Caching Decisions
10. Database Selection

---

## 1. Kleppmann's Trade-off Analysis

From "Designing Data-Intensive Applications" (2017) and interviews.

### The Universal Framework

For any technology or design choice:

```
1. IDENTIFY what it optimizes for (the benefit)
2. IDENTIFY what it sacrifices (the cost)
3. PHRASE it as: "X at the cost of Y"
4. ASK: in this specific context, is Y acceptable?
5. ASK: are there alternatives that trade differently?
```

### Worked Examples

**LSM-trees (LevelDB, RocksDB, Cassandra):**
Faster writes by converting random writes to sequential, at the cost of more complex reads and
background compaction CPU usage.

**B-trees (PostgreSQL, MySQL InnoDB):**
Predictable read performance and in-place updates, at the cost of write amplification and
random I/O on writes.

**Event sourcing:**
Complete audit trail and ability to replay/recompute, at the cost of eventual consistency for
read models and storage growth.

### Kleppmann's CAP Corrective
Stop using CAP as a decision framework. It's a theoretical result about network partitions, not
a practical design guide. Most real systems are neither strictly consistent nor strictly available.

Instead, specify precisely:
- WHICH consistency model? (linearizable? causal? eventual? read-your-writes?)
- WHICH availability guarantee? (five nines? best-effort?)
- WHAT latency characteristics? (p50? p99? tail latency?)

The real trade-off is usually **latency vs consistency**, not "C vs A."

---

## 2. Design Stamina Hypothesis (Fowler)

Design quality is an economic investment, not an aesthetic preference.

The graph: cumulative functionality delivered over time. Good design starts slightly slower
(upfront investment), but the curves cross within WEEKS, not months. After the crossover, good
design delivers features faster and the gap widens indefinitely.

**The wrong question:** "Do we have time for good design?"
**The right question:** "Can we afford NOT to invest in design?" (The answer is almost always no,
unless the project has a shelf life of < 2 weeks.)

**Connection to Ousterhout's "strategic programming":** invest 10-20% of development time in
design improvement. This is not a luxury; it's the fastest path to shipping.

**Fowler's corollary — Sacrificial Architecture:**
Accept that the best architecture today won't serve at 100x scale. Design for ~10x growth.
Plan to throw it away before ~100x. The question isn't "will we rewrite?" but "when?" Design
boundaries today that make future replacement of parts possible.

---

## 3. Innovation Tokens (McKinley, 2015)

From "Choose Boring Technology."

**Rule:** Every company gets about three innovation tokens. Each novel technology choice spends one.

**The process:**
```
1. LIST your current technology choices
2. COUNT how many are non-boring (novel, immature, unfamiliar to team)
3. If > 3 → you are overspent, consolidate
4. For any proposed new technology, WRITE DOWN what makes the current stack
   prohibitively expensive for this problem
5. If you can't articulate it → you don't understand the problem well enough
```

**Why boring wins:**
- Boring technology has well-understood failure modes
- Novel technology has unknown unknowns
- The magnitude of unknown unknowns is always larger than you think
- The operations burden of novel tech is paid every day forever
- "The long-term costs of keeping a system working reliably vastly exceed any inconveniences
  you encounter while building it"

**When to spend a token:**
When the problem genuinely cannot be solved with existing boring tools, AND you can write a
clear document explaining why. Not because the new thing is exciting. Not because a blog post
was compelling. Because the problem demands it.

---

## 4. Performance vs Readability (Muratori)

Casey Muratori's argument: the gap between "clean" polymorphic code and hardware-aware code
spans 1-2 orders of magnitude. This is not theoretical.

**Decision process:**
```
1. Is this code on a hot path? (profiling, not guessing)
   NO  → optimize for readability. Clean code wins.
   YES →
2. Measure the actual cost of abstraction
3. Does the performance difference matter for this product?
   NO  → keep it clean
   YES → write performance-oriented code for this specific path
```

The answer isn't "always optimize" or "always abstract." It's "know the cost, decide deliberately."
The vast majority of code is NOT on a hot path and should be optimized for human readability.

---

## 5. Monolith vs Microservices (Detailed)

### Decision Sequence

**Step 1: What problem are you actually solving?**
- "Teams stepping on each other" → organizational problem. Microservices may help.
- "One component needs 100x the resources" → extract that specific component.
- "The codebase is a mess" → microservices amplify mess. Clean the monolith first.
- "Everyone else is doing microservices" → not a reason.

**Step 2: Organizational readiness**
- Can teams deploy independently? (CI/CD, no shared release trains)
- Can teams own on-call for their services?
- Do you have operational maturity? (Monitoring, tracing, log aggregation)
- If NO to any → monolith with strong module boundaries

**Step 3: Domain understanding**
- Do you know where the domain boundaries are?
- Have they stabilized? (Not still shifting week to week)
- If NO → premature extraction will create wrong boundaries that are expensive to fix

**Default path (Fowler):**
Monolith first. Design for ~10x growth. Plan to rewrite/extract before ~100x. Extract services
when you understand domain boundaries empirically, not theoretically.

**The distributed monolith trap (Hightower):**
If your "microservices" must be deployed together, share a database, or require synchronized
changes across multiple services, you have a distributed monolith. This is strictly worse than
a regular monolith: all the coupling, plus network unreliability.

---

## 6. Consistency vs Availability (Detailed)

### Per-Subsystem Segmentation

Don't make one global choice. Different parts of the system have different tolerance for
inconsistency.

**High cost of inconsistency (prioritize consistency):**
- Financial transactions
- Inventory at checkout (avoid overselling)
- Booking systems (avoid double-booking)
- Authentication and authorization
- Billing and metering

**Low cost of inconsistency (prioritize availability):**
- Social feeds and timelines
- Dashboards and analytics
- Search result ranking
- Recommendation engines
- Notification counts

**CQRS pattern:**
Write path → strong consistency (one source of truth)
Read path → eventual consistency (materialized views, caches, read replicas)

**Consistency models to choose from (not just "strong" or "eventual"):**
- Linearizable: strongest, most expensive
- Sequential consistency: total order, but not real-time
- Causal consistency: respects causality, good enough for most use cases
- Read-your-writes: you see your own updates immediately
- Eventual: cheapest, weakest guarantee

---

## 7. Sync vs Async (Detailed)

### Synchronous Communication
- Simple mental model
- Immediate feedback (success/failure)
- Tight coupling: both sides must be up
- Chained calls multiply failure probability (5 services × 99% = 95.1%)
- Latency adds up linearly through the chain
- Always use: timeouts, circuit breakers, retries with backoff

### Asynchronous Communication (Messaging)
- Decouples in time (producer and consumer don't need to be up simultaneously)
- Absorbs load spikes (queue acts as buffer)
- Enables event-driven architecture
- Costs: ordering guarantees, idempotence requirement, eventual consistency, harder debugging

### Decision Heuristic
```
→ Request/response where user waits?         → sync (with circuit breakers)
→ Fire-and-forget, background processing?    → async
→ Cross-service data propagation?            → async (events)
→ Long-running operations?                   → async with polling or callbacks
→ Fan-out to many consumers?                 → async (pub/sub)
```

### Nygard's Stability Patterns for Sync
- **Timeouts:** on every external call, no exceptions
- **Circuit breakers:** stop calling a failing service
- **Bulkheads:** isolate failure to one pool of resources
- **Fail-fast:** check preconditions before starting work

---

## 8. Build vs Buy (Detailed)

```
1. Is this core to our competitive advantage?
   YES → build (you need to own the evolution and differentiation)
   NO  → buy or adopt

2. Does a boring, well-understood solution exist?
   YES → use it (McKinley's innovation tokens)
   NO  → evaluate carefully, budget an innovation token

3. Can you articulate IN WRITING why the existing stack can't solve this?
   NO  → you don't understand the problem yet. Stop.
   YES → proceed

4. What's the total cost of ownership?
   Build: initial dev + maintenance + on-call + hiring for the skill set
   Buy: licensing + integration + vendor lock-in + feature gaps
   Often buying is cheaper even when the sticker price seems high.
```

---

## 9. Caching Decisions

### When to Cache
- Read-heavy workloads (read:write ratio > 10:1)
- Computation is expensive and result doesn't change frequently
- Latency requirement cannot be met without caching
- Upstream service is fragile (cache as resilience mechanism)

### When NOT to Cache
- Write-heavy workloads (cache invalidation dominates)
- Data changes too frequently for acceptable staleness
- You can't tolerate ANY staleness (financial, auth)
- The system is simple enough without it

### Cache Invalidation Patterns
- **TTL-based:** simple, eventual staleness bounded. Good default.
- **Write-through:** update cache on write. Consistent but slower writes.
- **Write-behind:** async cache update. Fast writes, risk of loss.
- **Event-driven invalidation:** publish changes, consumers invalidate. Precise but complex.

"There are only two hard things in Computer Science: cache invalidation and naming things."
— Phil Karlton

---

## 10. Database Selection

### Relational (PostgreSQL, MySQL)
**Optimizes for:** data integrity, complex queries, ACID, schema enforcement
**Costs:** vertical scaling limits, rigid schema changes, join performance at scale
**Default choice** for most applications. PostgreSQL specifically covers an enormous range.

### Document (MongoDB, DynamoDB)
**Optimizes for:** schema flexibility, horizontal scaling, locality of data access
**Costs:** weak join support, eventual consistency (in distributed mode), data duplication
**Use when:** data is naturally hierarchical, schema varies across records, access pattern is
"fetch one document by key"

### Wide-Column (Cassandra, HBase)
**Optimizes for:** write throughput at massive scale, geographic distribution
**Costs:** limited query flexibility, eventual consistency, operational complexity
**Use when:** write volume is enormous, reads are by known partition key

### Graph (Neo4j)
**Optimizes for:** relationship-heavy queries (friend-of-friend, shortest path)
**Costs:** limited ecosystem, doesn't scale well for non-graph queries
**Use when:** relationships ARE the data, not just joins between entities

### The Default
If you don't have a specific, articulable reason to choose something else, use PostgreSQL.
It handles more use cases than most people realize. Spending an innovation token on a database
is one of the most expensive choices you can make.

---

# Design Heuristics

Daily rules-of-thumb, decomposition strategies, and failure thinking.

## Table of Contents
1. Deep Modules (Ousterhout)
2. Design It Twice (Ousterhout)
3. Four Rules of Simple Design (Beck)
4. Red Flags (Ousterhout)
5. Abstraction Lifecycle Warning (Abramov)
6. Data Dominates (Pike/Thompson)
7. Good Taste (Torvalds)
8. Problem Decomposition
9. Bounded Contexts (Evans)
10. Failure Thinking (Nygard)
11. Designing Around Impossibility (Helland)
12. Conway's Law as Design Constraint

---

## 1. Deep Modules (Ousterhout)

Visualize every module as a rectangle: width = interface complexity, height = functionality depth.

```
DEEP MODULE (good):          SHALLOW MODULE (bad):
┌──────┐                     ┌──────────────────────────┐
│      │                     │                          │
│      │                     └──────────────────────────┘
│      │
│      │
│      │
└──────┘
Narrow interface,             Wide interface,
lots of functionality         little functionality
```

**Gold standard:** Unix file I/O — 5 calls (open, read, write, seek, close) hiding enormous
complexity (buffering, caching, device drivers, permissions, locking, journaling).

**Ousterhout's counterintuitive claim:** "Methods containing hundreds of lines of code are fine
if they have a simple signature and are easy to read. These methods are deep."

**The classitis antipattern:** breaking everything into tiny classes/methods doesn't reduce
complexity. It increases it by spreading logic across many shallow modules with complex
interactions. The goal is not small units — it's deep units with simple interfaces.

```python
# Shallow module — interface as complex as implementation
class StringValidator:
    def validate_length(self, s: str, min_len: int, max_len: int) -> bool:
        return min_len <= len(s) <= max_len
    def validate_chars(self, s: str, allowed: set[str]) -> bool:
        return all(c in allowed for c in s)

# Deep module — simple interface hiding real complexity
class EmailAddress:
    """Construct with a string. If it's invalid, you get a ValueError."""
    def __init__(self, value: str):
        self._validate(value)
        self._local, self._domain = value.rsplit("@", 1)
        self._value = value

    def _validate(self, value: str) -> None:
        # 20+ lines of RFC 5322 validation hidden behind a one-arg constructor
        ...
```

**Information hiding:** the mechanism for achieving depth. Each module encapsulates design
decisions that other modules don't need to know. If two modules share knowledge about the same
design decision, that's information leakage.

---

## 2. Design It Twice (Ousterhout)

Always generate at least two alternative designs before committing. This is not optional overhead;
it's a forcing function for deeper understanding.

The act of producing alternatives:
- Forces you to articulate what you're optimizing for
- Reveals hidden assumptions in your first design
- Often leads to a synthesis that's better than either alternative
- Prevents anchoring on the first idea that seems workable

Even if you end up with your first design, you'll understand it better for having considered
alternatives.

```python
# Design A: Flat function — simple, but couples parsing + validation + storage
def ingest_csv(path: str, db) -> int:
    rows = csv.DictReader(open(path))
    for row in rows:
        if is_valid(row):
            db.insert(row)

# Design B: Pipeline — more structure, but each step is independently testable
def ingest_csv(path: str, db) -> int:
    raw = parse_csv(path)           # pure: Path → list[dict]
    valid = filter_valid(raw)       # pure: list[dict] → list[dict]
    return persist(valid, db)       # I/O: list[dict] → int

# Compare: B costs one more function call but gains testability and reusability.
# In a script? A is fine. In a system? B pays off.
```

---

## 3. Four Rules of Simple Design (Beck)

In priority order:
1. **Passes the tests** — it works correctly
2. **Reveals intention** — a reader understands WHY, not just WHAT
3. **No duplication** — DRY (but see Abramov's warning below)
4. **Fewest elements** — remove anything not serving rules 1-3

When rules 2 and 3 conflict: **readability wins over the technical metric.** Empathy for the
reader is more important than satisfying a code metric.

---

## 4. Red Flags (Ousterhout)

Stop and reconsider when you see:
- **Shallow modules** — interface is as complex as implementation
- **Information leakage** — two modules share knowledge about a design decision
- **Temporal decomposition** — structured by execution order, not by knowledge
- **Pass-through methods** — methods that just delegate, adding no value
- **Vague or generic names** — can't tell what the module does from its name
- **Hard-to-describe interfaces** — if you can't explain it simply, it's too complex
- **Conjoined methods** — can't understand one without reading the other

**Ousterhout's social rule:** "If a reviewer tells you something is not obvious, don't argue
with them." If someone doesn't understand your code, the code is the problem, not the person.

```python
# Red flag: pass-through method adding no value
class UserService:
    def __init__(self, repo): self._repo = repo
    def get_user(self, user_id):
        return self._repo.get_user(user_id)  # why does this class exist?

# Red flag: temporal decomposition — structured by execution order, not knowledge
def step1_load(): ...
def step2_validate(): ...
def step3_transform(): ...
def step4_save(): ...

# Better: structured by what each module knows
def load_orders(source: str) -> list[RawOrder]: ...
def validate_orders(orders: list[RawOrder]) -> list[ValidOrder]: ...
def persist_orders(orders: list[ValidOrder], db) -> int: ...
```

---

## 5. Abstraction Lifecycle Warning (Abramov)

Dan Abramov's "Goodbye, Clean Code" — the death spiral of premature abstraction:

```
Two modules share similar code
  → Extract abstraction
    → Third use case needs something slightly different
      → Add parameter/flag
        → Bug found → add special case
          → Make it "generic" with configuration
            → Nobody understands it
              → Stagnation → full rewrite
```

**The fix:** At the first sign of divergence between use cases, **inline the abstraction back.**
Duplication is cheaper than wrong abstraction.

**Abramov's principle:** "Let clean code guide you. Then let it go." Don't worship DRY to the
point where you create an incomprehensible shared abstraction. Two simple copies that diverge
over time are better than one complex abstraction that tries to serve all masters.

**The rule of three:** Don't abstract until you have three genuine, similar use cases. Two
similarities might be coincidence.

---

## 6. Data Dominates (Pike, Thompson)

"If you've chosen the right data structures and organized things well, the algorithms will
almost always be self-evident." — Rob Pike, paraphrasing Fred Brooks

**Process when stuck:**
1. Stop thinking about algorithms and control flow
2. Redesign the data representation
3. The algorithm usually becomes obvious

**The deeper principle:** Data structures are the skeleton of a program. Algorithms are the
muscles. If the skeleton is wrong, no amount of muscular effort fixes it.

**Pike's Rule 5 (from "Notes on Programming in C"):**
"Data dominates. If you've chosen the right data structures and organized things well, the
algorithms will almost always be self-evident. Data structures, not algorithms, are central
to programming."

```python
# Wrong data structure → complex algorithm
def find_duplicates(items: list[str]) -> list[str]:
    dupes = []
    for i, item in enumerate(items):
        for j in range(i + 1, len(items)):
            if items[j] == item and item not in dupes:
                dupes.append(item)
    return dupes  # O(n²), hard to read

# Right data structure → algorithm is obvious
def find_duplicates(items: list[str]) -> set[str]:
    seen: set[str] = set()
    return {item for item in items if item in seen or seen.add(item)}  # O(n)
```

---

## 7. Good Taste (Torvalds)

"Sometimes you can see a problem in a different way and rewrite it so that a special case goes
away and becomes the normal case."

**Heuristic:** Make the happy path the only path whenever you can. Eliminate edge cases through
better abstractions, not more if-statements.

**Torvalds' linked list example:** Instead of special-casing the head of the list, use a pointer
to pointer. The head deletion case disappears — it becomes the same code as middle deletion.
One algorithm, zero special cases.

**The meta-lesson:** When you find yourself writing special-case handling, step back and ask
whether a different data structure or abstraction would eliminate the special case entirely.

```python
# Special cases everywhere
def format_name(first: str, middle: str | None, last: str) -> str:
    if middle is None:
        return f"{first} {last}"
    elif middle == "":
        return f"{first} {last}"
    else:
        return f"{first} {middle} {last}"

# Eliminate special cases — filter empty parts
def format_name(first: str, middle: str | None, last: str) -> str:
    parts = [first, middle, last]
    return " ".join(p for p in parts if p)  # one path, zero special cases
```

---

## 8. Problem Decomposition

### Top-Down Process (from research on expert vs student engineers)

The critical difference: experts start with the whole problem, break it into pieces, then
reassemble. Students jump straight to subsystem details.

```
1. UNDERSTAND the whole problem first (resist diving into details)
2. IDENTIFY natural boundaries in the problem space
3. MINIMIZE dependencies between parts
4. CREATE clear interfaces between components
5. START with the hardest/riskiest part (most unknowns)
6. INTEGRATE incrementally — never wait until all parts are done
```

### Ousterhout's Decomposition Principles
- Design modules around **knowledge needed**, not execution order
- Each abstraction layer should offer a DIFFERENT abstraction, not a passthrough
- Resist excessive decomposition — "loads of tiny classes that increase overall system
  complexity" is a real antipattern

### When to Split, When to Join
**Split when:**
- Two concerns change for different reasons
- Two concerns have different consumers
- The module has grown too complex to hold in one person's head

**Join when:**
- Split pieces always change together
- Split pieces share most of their context
- The split creates more interface complexity than it removes implementation complexity

---

## 9. Bounded Contexts (Evans, DDD)

A model is only valid within its bounded context. Different contexts can use the same term with
different meanings — intentionally.

**Example:** "Customer"
- In Billing: payment method, invoice history, credit limit
- In Support: ticket history, satisfaction score, SLA tier
- In Marketing: segments, campaign history, engagement score

These are NOT the same entity. They share an ID. That's it. Trying to create a universal
Customer model creates an unmaintainable God Object.

**Decomposition question:** Where do the natural semantic boundaries fall in the domain?

**Evans' trade-off:** Duplication between contexts is acceptable if it buys autonomy and clarity.
A little data copying between services is far cheaper than coupling them through a shared model.

**Anti-corruption layer:** When you must integrate with a context that has a different model,
build a translation layer at the boundary. Don't let a foreign model leak into your context.

---

## 10. Failure Thinking (Nygard)

From "Release It!" — Design for how systems break, not just how they work.

### Failure Mode Analysis

Every integration point is a potential failure source. For each external dependency:

```
1. WHAT happens if it's slow? (slow is worse than down — it holds resources open)
2. WHAT happens if it returns garbage?
3. WHAT happens if it's down entirely?
4. WHAT is the blast radius?
```

### Stability Patterns

**Timeouts:** On every external call. No call without a timeout. Ever. Default to short
timeouts and tune up, not the other way around.

**Circuit Breakers:** After N failures in a window, stop calling the failing service. Probe
periodically. Resume when it recovers. Prevents cascading failure.

**Bulkheads:** Isolate failure domains. One failing integration shouldn't consume all your
thread pool / connection pool / memory. Separate pools per dependency.

**Fail-Fast:** When preconditions aren't met, fail immediately instead of queuing up work
that will fail later. Protects downstream resources.

### Nygard's Math
Chained synchronous calls multiply failure probability:
- 5 services × 99% availability = 95.1% end-to-end
- 5 services × 99.9% availability = 99.5% end-to-end
- 10 services × 99% availability = 90.4% end-to-end

This is why async decoupling and circuit breakers aren't optional in service-oriented architectures.

---

## 11. Designing Around Impossibility (Helland)

Pat Helland's approach to distributed systems: accept the constraints, then design within them.

**The constraint:** At scale, distributed transactions are impractical. They are too fragile and
too slow.

**The acceptance:** You cannot atomically update across entities.

**Design consequences:**
- Define "entities" as the unit of atomicity (one entity = one transactional boundary)
- Use messaging to coordinate between entities
- Embrace uncertainty and design for it
- Require idempotence everywhere (messages may be delivered more than once)
- Tolerate message reordering
- Use activities/workflows to track multi-entity operations

**Helland's principle:** "The absence of distributed transactions means the acceptance of
uncertainty. It is unavoidable."

**From "Immutability Changes Everything" (Helland):**
When storage is cheap (a changed constraint), append-only architectures become viable at every
level. "Accountants don't use erasers, or they go to jail." Immutable data eliminates entire
classes of coordination problems.

---

## 12. Conway's Law as Design Constraint

"Organizations which design systems are constrained to produce designs which are copies of the
communication structures of these organizations." — Melvin Conway, 1967

This is not a suggestion. It's a law. Systems mirror org structure whether you want them to or not.

**Implication:** Before choosing architecture, ask: what does our org structure look like?
That's what the system architecture will converge toward.

**Two strategies:**
1. **Inverse Conway Maneuver:** Change the org to match desired architecture
2. **Accept reality:** Design the architecture the org can actually produce and maintain

**Examples:**
- A company with 4 backend teams will produce a system with 4 backend services
- A team split across timezones will produce a system with async boundaries at timezone lines
- A company where frontend and backend are separate orgs will produce systems with thick API
  layers between them

Don't fight Conway's Law. Use it.

```python
# If your org has separate "payments" and "orders" teams,
# your codebase will naturally develop separate modules:
#
# payments/          ← owned by payments team
#   service.py
#   models.py
# orders/            ← owned by orders team
#   service.py
#   models.py
#
# Don't fight this. Formalize the boundary with explicit interfaces:
# payments/api.py — the contract other teams import
# orders/api.py   — the contract other teams import
#
# Attempting a shared "core" module owned by nobody will create friction.
```

---

# Pre-Design Thinking and Building Expertise

Thinking processes, writing as design, and building architectural intuition.

## Table of Contents
1. Hammock-Driven Development (Hickey)
2. Think Above the Code (Lamport)
3. Mental Model Debugging (Pike/Thompson)
4. Test-Driven Design (Beck)
5. Writing as Thinking
6. Constraint Identification (Hohpe)
7. Building Design Intuition (Norvig, Majors)
8. The Expertise Progression

---

## 1. Hammock-Driven Development (Hickey, 2010)

Rich Hickey's process for solving non-trivial problems:

```
1. STATE the problem — out loud and in writing. Be specific.
2. UNDERSTAND it — what do you know? what don't you know? what are the constraints?
3. RESEARCH — prior art, adjacent solutions, domain knowledge
4. LOAD your waking mind — saturate yourself with the information
5. FEED your background mind — sleep on it, walk, do something else
6. WAIT for an idea to surface
7. CRITICALLY ANALYZE the idea before acting on it
```

**Why this works:** The brain's background processing (diffuse mode) solves problems that focused
thinking cannot. But it only works if you deliberately feed it. Step 4 is essential — you must
have deeply engaged with the problem first.

**Key insight:** Most critical bugs come from misunderstanding the problem, not from implementation
errors. Spending a day thinking saves a week debugging.

**When to use this:** Before any project that will take more than a week. Before any architectural
decision that's hard to reverse. Before any technology adoption.

```python
# What Hickey's process looks like in practice:
# Instead of jumping to code, write a problem statement first:

"""
PROBLEM: Order processing takes 45s for large orders (500+ items).
WHAT I KNOW: Bottleneck is N+1 queries in allocation loop.
WHAT I DON'T KNOW: Whether batch allocation or CQRS read model is better.
CONSTRAINTS: Must remain compatible with existing API. Max 2 weeks.
RESEARCH: Cosmic Python ch.9 (CQRS), SQLAlchemy bulk operations docs.
DECISION: Try batch allocation first (simpler). CQRS only if insufficient.
"""
```

**The anti-pattern:** Jumping to implementation because "we don't have time to think." This is
Fowler's design stamina problem — skipping thinking feels fast but is net slower.

---

## 2. Think Above the Code (Lamport)

Leslie Lamport's core principle: formal thinking prevents bugs that testing cannot catch.

```
1. SPECIFY what the system does (behavior) — separate from how
2. WRITE the specification in precise language (formal or structured prose)
3. IDENTIFY invariants — what must always be true?
4. IDENTIFY edge cases — what happens at boundaries?
5. Only then: decide implementation
```

**Lamport's analogy:** Architects draw detailed blueprints before a brick is laid. Programmers
almost never do. Yet software is far more complex than a building.

**Practical application (you don't need TLA+):**
- Write a one-page document describing what the system does, not how
- List every invariant you can think of (e.g., "total money in the system never changes")
- List every edge case (empty inputs, max capacity, concurrent operations, failure during
  operation)
- Have someone else read it and find holes
- THEN design the implementation

**The payoff:** Catching a design error in a spec costs minutes. Catching it in production costs
days to weeks. The ROI of specification is enormous.

```python
# Lamport's approach in Python: specify invariants BEFORE implementation

# Step 1: Write the invariants as assertions
def test_transfer_invariants():
    """Money is never created or destroyed during transfer."""
    account_a = Account(balance=1000)
    account_b = Account(balance=500)
    total_before = account_a.balance + account_b.balance

    transfer(account_a, account_b, amount=200)

    total_after = account_a.balance + account_b.balance
    assert total_after == total_before, "Invariant violated: money created/destroyed"
    assert account_a.balance >= 0, "Invariant violated: negative balance"

# Step 2: NOW write the implementation that satisfies them
def transfer(source: Account, target: Account, amount: int) -> None:
    if source.balance < amount:
        raise InsufficientFunds(source.id, amount)
    source.balance -= amount
    target.balance += amount
```

---

## 3. Mental Model Debugging (Pike, from Thompson)

Ken Thompson's debugging process, as observed by Rob Pike:

```
1. STOP — resist the urge to look at code immediately
2. BUILD a mental model of how the system should work
3. ASK: how could this symptom occur given the model?
4. IDENTIFY where the model must be wrong
5. FIX the model error (this is usually a design-level fix, not a line-level fix)
```

**Pike's observation:** Thompson would stand and think while Pike dug through stack traces.
Thompson consistently found the root cause first because he was reasoning at the model level,
not the code level.

**Implication for architecture:** When a system misbehaves, the bug is often in your mental
model, not in a single line of code. If your model is wrong, you'll "fix" symptoms endlessly
without addressing the cause.

```python
# Symptom: "Orders sometimes have wrong totals"
# Typical reaction: add more validation, more logging, more checks
# Thompson's approach: what's the MENTAL MODEL of how totals work?

# Wrong model: "total is updated whenever a line is added"
# (fails when lines are removed, modified, or added concurrently)

# Right model: "total is always computed from current lines"
@property
def total(self) -> int:
    return sum(line.price * line.qty for line in self.lines)
    # Derived value — can never be out of sync. Bug class eliminated.
```

---

## 4. Test-Driven Design (Beck)

Kent Beck's process is the opposite of Hickey's in style (tight loops vs. extended contemplation)
but compatible in principle: force yourself to think about structure deliberately.

```
1. WRITE a test for the next small behavior
2. MAKE it pass (even by hardcoding or copying)
3. REMOVE duplication immediately
4. IMPROVE names and structure
5. REPEAT — design emerges from the cycle
```

**The critical rule:** Never change behavior and structure simultaneously. Separate "make it work"
from "make it right" into distinct, alternating steps.

**Beck's preparatory refactoring heuristic:** "For each desired change, make the change easy
(warning: this may be hard), then make the easy change." If a change feels hard, you're missing
a preparatory refactoring step.

```python
# Task: add email notification when order is placed
# Feels hard because business logic is inside the Flask route

# Step 1: Make the change easy (extract service function)
def place_order(orderid: str, sku: str, qty: int, uow) -> str:
    with uow:
        batchref = allocate(orderid, sku, qty, uow)
        uow.commit()
    return batchref

# Step 2: Make the easy change (add notification)
def place_order(orderid: str, sku: str, qty: int, uow, notify) -> str:
    with uow:
        batchref = allocate(orderid, sku, qty, uow)
        uow.commit()
    notify(orderid, batchref)  # easy — one line added
    return batchref
```

**When TDD helps most:**
- When the design space is well-understood
- When requirements can be expressed as small, discrete behaviors
- When the feedback loop needs to be tight

**When TDD helps less:**
- Exploratory work where you don't know what you're building yet
- Performance-critical code where the structure must follow hardware patterns
- UI code where visual feedback matters more than unit tests

---

## 5. Writing as Thinking

Top engineers use writing to make decisions, not document them.

### When to Write
- **Before** starting design (not after)
- When the same discussion has happened 3+ times (Larson)
- When evaluating any new technology (McKinley)
- When a decision is hard to reverse

### Decision Document Template

```
1. CONTEXT — what situation are we in?
2. PROBLEM — what specifically needs solving?
3. CONSTRAINTS — what can't change?
4. OPTIONS — at least two alternatives (Ousterhout's "design it twice")
5. TRADE-OFFS — explicit costs and benefits of each option
6. DECISION — what we chose and WHY
7. CONSEQUENCES — what we accept as a result
```

```python
# Example ADR (Architecture Decision Record) as Python docstring / comment:

"""
ADR-003: Use PostgreSQL instead of MongoDB for order storage

CONTEXT: Orders have relational structure (order → lines → allocations).
PROBLEM: Need ACID transactions for inventory allocation.
CONSTRAINTS: Team has PostgreSQL experience. Budget for one managed DB.
OPTIONS:
  A) PostgreSQL — strong consistency, relational model, team knows it
  B) MongoDB — schema flexibility, horizontal scaling
TRADE-OFFS:
  A gives: ACID, complex queries, familiar tooling
     costs: vertical scaling, schema migrations
  B gives: schema flex, easy horizontal scale
     costs: no multi-document transactions, new learning curve
DECISION: PostgreSQL (option A).
WHY: ACID is non-negotiable for inventory. Team expertise. Don't spend
     an innovation token on the database.
CONSEQUENCES: Must plan for vertical scaling. Accept schema migrations.
"""
```

### Document Formats
- **RFC / Design Doc:** for proposed changes needing input before commitment
- **ADR (Architecture Decision Record):** for recording decisions + context after the fact
- **PR/FAQ (Amazon style):** for product/feature proposals, working backwards from the customer

### Ousterhout on Comments
"Write the comments first. This makes documentation part of the design process." If you can't
write a clear comment explaining what a module does and why, the design isn't clear yet.

### Larson's Strategy Ladder
"Synthesize 5 design docs into a strategy. Extrapolate 5 strategies into a vision." Strategy
is not invented top-down; it emerges from patterns in tactical decisions.

---

## 6. Constraint Identification (Hohpe)

Gregor Hohpe's process for identifying what actually constrains the design:

```
1. LIST hard constraints:
   - Physics (latency, bandwidth, storage capacity)
   - Organization (team size, team structure, Conway's Law)
   - Business (budget, timeline, compliance, regulatory)
   - Existing systems (what's deployed and can't be rewritten)

2. LIST soft constraints (preferences, conventions, team skills)

3. IDENTIFY ghost constraints:
   - Past constraints baked into current systems that NO LONGER APPLY
   - "We do it this way because..." — is the original reason still valid?

4. SEPARATE constraints from preferences — be honest about which is which

5. DESIGN within the constraint space, not against it
```

**Hohpe's key insight:** "Without a constraint, the recommended way of working can become the
exact opposite." When hardware provisioning was slow, you kept servers running forever. When it
became instant (cloud), disposable infrastructure emerged. But only if you *recognized* the
constraint had changed.

**Ghost constraints are the biggest opportunity.** Most organizations are full of practices that
exist because of a constraint that disappeared years ago. Finding and removing ghost constraints
can unlock architectural options that nobody thought were available.

---

## 7. Building Design Intuition

### Norvig's Deliberative Practice

Peter Norvig's estimate: expertise requires ~10 years of sustained, intentional practice.

```
- Program constantly
- Work on projects with others
  - Be the best on some teams (you lead and teach)
  - Be the worst on others (you learn and absorb)
- Work on projects AFTER other programmers (learn what maintainability really means)
- Learn at least 6 languages (different paradigms, not just syntax)
  - This is about learning to think differently, not collecting syntax
- Talk to other programmers — "more important than any book or training course"
```

**Key distinction:** Not just repetition, but "challenging yourself with a task just beyond your
current ability, trying it, analyzing your performance, and correcting mistakes." This is
Ericsson's deliberate practice applied to software.

### Majors' Production Immersion

Charity Majors' argument: production is the only real feedback loop.

```
1. Deploy your own code within 15 minutes of writing it
2. Watch it run in production
3. Instrument as you code, not after
4. Experience the flakiness and weirdness firsthand
5. Every moment in a staging environment is not neutral — it builds wrong instincts
```

**Majors' definition of seniority:** "When I think of senior engineers, I think of engineers
whose instincts I trust." Instincts come from production feedback, not from reading about
best practices.

---

## 8. The Expertise Progression

```
JUNIOR     → Focus: making code work
             Skills: syntax, debugging, basic patterns
             Failure mode: clever solutions nobody else can read

MID-LEVEL  → Focus: seeing patterns across problems
             Skills: refactoring, testing, code review
             Failure mode: over-engineering, premature abstraction

SENIOR     → Focus: thinking in systems, understanding second-order effects
             Skills: trade-off analysis, constraint identification, failure modes
             Failure mode: analysis paralysis, gold-plating

STAFF+     → Focus: organizational-level synthesis, choosing problems to solve
             Skills: writing, influence, cross-team coordination, strategic thinking
             Failure mode: disconnection from implementation reality
```

### Key Mental Shifts Along the Way
- From "making it work" → "managing complexity"
- From "solving the problem" → "choosing the right problem"
- From "clever solutions" → "obvious solutions"
- From "building features" → "building abstractions"
- From "my code" → "our system"
- From "technical excellence" → "organizational effectiveness"

### The AI Era Addendum (from the thread discussion)

AI collapses implementation cost, which makes **decision quality the bottleneck**. The engineers
who understand WHY a system needs eventual consistency vs strong consistency will direct the AI
that writes the implementation. Architectural judgment and trade-off reasoning become MORE
valuable, not less, as code generation becomes cheaper.

"AI is good at typing. It is bad at thinking through tradeoffs." — Phuong Le

Key risk: junior engineers learn system design by watching their designs fail in production. If
AI generates the implementations, you lose that feedback loop unless you're intentional about
learning from the failures.

---

# Python Elite Style Guide
> Distilled from: Guido van Rossum, Lukasz Langa, Raymond Hettinger, Irit Katriel, Pradyun Gedam, Wes McKinney, Kenneth Reitz, Ines Montani, Anthony Shaw

---

## 1. Foundational Mindset

**Code is read far more than it is written.** Every name, every abstraction — optimize for the next reader, not the writer. If you need a comment to explain *what* code does (not *why*), rewrite the code.

**There should be one obvious way.** When you find yourself reaching for a clever solution, stop.

```python
# Wrong
result = (lambda x: x * 2)(value)
names = []
for i in range(len(source)):
    names.append(source[i].name)

# Right
result = value * 2
names = [item.name for item in source]
```

**Explicit is better than implicit.** Every dependency, transformation, assumption should be visible at the call site.

```python
# Wrong — mutable default: shared across calls
def process(data, results=[]):
    results.append(transform(data))
    return results

# Right — explicit
def process(data, results=None):
    if results is None:
        results = []
    results.append(transform(data))
    return results
```

---

## 2. Formatting & Layout

### Core rules

- **4 spaces** per indentation level. No tabs.
- **88 characters** max line length (Black's default).
- Use implicit line continuation inside `()`, `[]`, `{}`. Avoid backslash.
- **2 blank lines** before/after top-level functions and classes.
- **1 blank line** between methods inside a class.

```python
# Wrong — backslash continuation
result = some_function(arg1) + \
         another_function(arg2)

# Right — implicit continuation
result = (
    some_function(arg1)
    + another_function(arg2)
)
```

### Black formatting (automate, don't debate)

- Double quotes everywhere.
- Binary operators wrap *before* the operator.
- Trailing comma in multi-line collections forces expansion.

```python
# Wrong — operator after line break
income = (gross_wages +
          taxable_interest)

# Right
income = (
    gross_wages
    + taxable_interest
)
```

### Import order (three groups, blank line between each)

```python
# 1. Standard library
import os
import sys
from pathlib import Path
from typing import Optional

# 2. Third-party
import numpy as np
import pandas as pd
from requests import Session

# 3. Local / project
from myproject.models import User
from myproject.utils import slugify
```

Rules: one import per line; absolute over relative; never `from module import *`.

---

## 3. Naming Conventions

| Construct | Convention | Example |
|---|---|---|
| Variable | `snake_case` | `user_count` |
| Function | `snake_case` | `get_active_users()` |
| Class | `PascalCase` | `UserRepository` |
| Constant | `UPPER_SNAKE_CASE` | `MAX_RETRIES = 3` |
| Module | `lowercase` | `user_service.py` |
| Private | `_leading_underscore` | `_internal_cache` |
| Type variable | Short `PascalCase` | `T`, `KT`, `VT` |

**Names should be pronounceable and descriptive:**
```python
# Wrong
def calc(u, t): return u * t

# Right
def calculate_distance(velocity: float, time: float) -> float:
    return velocity * time
```

**Boolean names should read as assertions:**
```python
# Wrong
user_active = True
check = validate_email(email)

# Right
is_active = True
is_valid_email = validate_email(email)
```

**Avoid abbreviations** unless universally understood (`df`, `np`, `pd` are canonical and fine).

```python
# Wrong
usr = get_usr(uid)
resp = fetch_resp(url)

# Right
user = get_user(user_id)
response = fetch_response(url)
```

---

## 4. Iteration & Loops

*Primary source: Raymond Hettinger*

Never iterate with manual indices:
```python
# Wrong
for i in range(len(colors)):
    print(colors[i])

# Right
for color in colors:
    print(color)
```

Use `enumerate()` when you need the index:
```python
for i, color in enumerate(colors):
    print(i, '-->', color)
```

Use `zip()` for multiple sequences:
```python
for name, score in zip(names, scores):
    print(name, score)
```

Simultaneous state updates eliminate ordering bugs:
```python
# Wrong — uses temp variable, error-prone
t = y; y = x + y; x = t

# Right — simultaneous update
x, y = y, x + y
```

Comprehensions are declarative — use them:
```python
# Wrong — imperative
squares = []
for x in range(10):
    if x % 2 == 0:
        squares.append(x ** 2)

# Right — declarative
squares = [x ** 2 for x in range(10) if x % 2 == 0]

# Dict, set, generator
word_lengths = {word: len(word) for word in words}
unique_lengths = {len(word) for word in words}
total = sum(x ** 2 for x in range(1000))   # generator — lazy
```

**Rule:** If a comprehension exceeds 88 chars, break it into a loop.

Use `for...else` for search patterns:
```python
for item in collection:
    if predicate(item):
        break
else:
    handle_not_found()   # runs only if loop completed without break
```

---

## 5. Data Structures

*Primary source: Raymond Hettinger, Wes McKinney*

### Dictionaries

```python
# Use .get() with default
value = d.get(key, 'default')

# Use defaultdict for grouping
from collections import defaultdict
groups = defaultdict(list)
for item in items:
    groups[item.category].append(item)

# Use ChainMap for layered configs
from collections import ChainMap
config = ChainMap(cli_args, env_vars, defaults)

# Iterate key-value pairs
for key, value in d.items():
    print(key, value)
```

### Use `deque` for queues — not `list`

`list.pop(0)` is O(n). `deque.popleft()` is O(1).

```python
from collections import deque
queue = deque()
queue.append(item)
first = queue.popleft()  # O(1)
```

### Use `dataclass` over raw tuples

```python
# Wrong — positional indexing is fragile
point = (3.0, 4.0)
x = point[0]

# Right — named, self-documenting
from dataclasses import dataclass

@dataclass
class Point:
    x: float
    y: float

    def distance_from_origin(self) -> float:
        return (self.x ** 2 + self.y ** 2) ** 0.5
```

### Vectorized operations for tabular data (Wes McKinney)

```python
# Wrong — Python loop over rows (extremely slow)
results = []
for _, row in df.iterrows():
    results.append(row['price'] * row['quantity'])
df['total'] = results

# Right — vectorized
df['total'] = df['price'] * df['quantity']

# For conditional ops — np.where or pd.cut
import numpy as np
df['category'] = np.where(df['score'] > 80, 'high', 'low')
```

---

## 6. Functions & APIs

One logical step = one sentence. If you need "and" to describe what a function does, split it:
```python
# Wrong — does two things
def validate_and_save_user(data): ...

# Right
def validate_user(data): ...
def save_user(validated_data): ...
```

Use keyword arguments for clarity at the call site:
```python
# Wrong — what does True mean?
create_user('alice', True, False)

# Right — self-documenting
create_user('alice', is_admin=True, send_email=False)
```

Force keyword-only arguments with `*`:
```python
def create_report(title: str, *, include_summary: bool = True, max_rows: int = 100):
    ...
```

Return early — avoid deep nesting:
```python
# Wrong — pyramid of doom
def process(user):
    if user:
        if user.is_active:
            if user.has_permission('write'):
                do_work(user)

# Right — guard clauses
def process(user):
    if not user: return
    if not user.is_active: return
    if not user.has_permission('write'): return
    do_work(user)
```

Use generators for large sequences:
```python
def get_all_records(db):
    for row in db.fetchall():
        yield process(row)
```

Use `lru_cache` for pure functions:
```python
from functools import cache

@cache
def fibonacci(n: int) -> int:
    if n < 2: return n
    return fibonacci(n - 1) + fibonacci(n - 2)
```

---

## 7. Classes & OOP

Always use `super()` without arguments (supports cooperative MRO):
```python
# Wrong — hardcodes parent
class Child(Parent):
    def __init__(self):
        Parent.__init__(self)

# Right
class Child(Parent):
    def __init__(self):
        super().__init__()
```

Use `@dataclass` for data containers:
```python
from dataclasses import dataclass, field

@dataclass
class Order:
    order_id: str
    customer_name: str
    items: list[str] = field(default_factory=list)
    is_fulfilled: bool = False
```

Use `@property` for computed attributes:
```python
class Circle:
    def __init__(self, radius: float):
        self.radius = radius

    @property
    def area(self) -> float:
        return 3.14159 * self.radius ** 2
```

Don't create classes just to group functions:
```python
# Wrong — class with only __init__ + one method
class EmailSender:
    def __init__(self, address): self.address = address
    def send(self, message): ...

# Right — just a function
def send_email(address: str, message: str) -> None: ...
```

Use `__slots__` for memory-critical classes:
```python
class Coordinate:
    __slots__ = ('x', 'y')

    def __init__(self, x: float, y: float):
        self.x = x
        self.y = y
```

---

## 8. Error Handling

*Primary source: Irit Katriel*

Errors should never pass silently. Catch specific exceptions only:
```python
# Wrong
try:
    result = do_something()
except Exception:
    pass

# Right
try:
    result = do_something()
except ValueError as e:
    log_invalid_input(e)
    raise
except ConnectionError as e:
    log_network_failure(e)
    raise
```

Use exception chaining to preserve context:
```python
# Wrong — original traceback lost
try:
    data = fetch(url)
except RequestError:
    raise ServiceUnavailableError("Upstream failed")

# Right — chain the exception
try:
    data = fetch(url)
except RequestError as e:
    raise ServiceUnavailableError("Upstream failed") from e
```

Use `ExceptionGroup` for multiple concurrent errors (Python 3.11+):
```python
try:
    async with asyncio.TaskGroup() as tg:
        tg.create_task(fetch_users())
        tg.create_task(fetch_orders())
except* ValueError as eg:
    for exc in eg.exceptions:
        log_validation_error(exc)
```

Use context managers for resources:
```python
# Wrong — close() may never run
f = open('data.txt')
data = f.read()
f.close()

# Right
with open('data.txt') as f:
    data = f.read()

# Custom context manager
from contextlib import contextmanager

@contextmanager
def database_transaction(conn):
    try:
        yield conn
        conn.commit()
    except Exception:
        conn.rollback()
        raise
```

---

## 9. Imports & Project Layout

`pyproject.toml` is the single source of truth:
```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "my-package"
version = "1.0.0"
requires-python = ">=3.11"
dependencies = ["requests>=2.28", "pandas>=2.0"]

[project.optional-dependencies]
dev = ["pytest>=7.0", "black>=24.0", "mypy>=1.0"]
```

Keep `__init__.py` minimal and intentional:
```python
# Only expose the public API
from ._core import Session
from .models import User, Order
from .exceptions import MyPackageError

__all__ = ["Session", "User", "Order", "MyPackageError"]
```

---

## 10. Type Annotations

Annotate all public function signatures. Use modern syntax (Python 3.10+):
```python
# Old
from typing import Optional, Union, List
def process(value: Optional[int]) -> Union[str, int]: ...

# Modern (3.10+)
def process(value: int | None) -> str | int: ...

# Built-in generics (3.9+)
def get_names(users: list[User]) -> dict[str, int]: ...
```

Use `TypeAlias` for complex repeated types:
```python
from typing import TypeAlias

Matrix: TypeAlias = list[list[float]]
JsonDict: TypeAlias = dict[str, "JsonValue"]
```

Use `Protocol` for structural typing:
```python
from typing import Protocol

class Serializable(Protocol):
    def to_dict(self) -> dict: ...

def save(obj: Serializable, path: str) -> None:
    data = obj.to_dict()
    ...
```

Run `mypy --strict` in CI:
```bash
mypy src/ --strict --ignore-missing-imports
```

---

## 11. Performance Patterns

*Primary source: Anthony Shaw, Wes McKinney*

Know what is O(1) vs O(n):

| Operation | Structure | Complexity |
|---|---|---|
| Lookup by key | `dict`, `set` | O(1) |
| Lookup by value | `list` | O(n) |
| `in` operator | `set` | O(1) |
| `in` operator | `list` | O(n) |
| Append | `list`, `deque` | O(1) |
| Prepend | `deque` | O(1) |
| Prepend | `list` | O(n) |

```python
# Wrong — O(n) lookup in a loop = O(n²)
banned_words = ['spam', 'ads', 'click']
filtered = [w for w in text.split() if w not in banned_words]

# Right — O(1) lookup = O(n) total
banned_words = {'spam', 'ads', 'click'}
filtered = [w for w in text.split() if w not in banned_words]
```

Profile before optimising:
```python
import cProfile, pstats

with cProfile.Profile() as pr:
    run_my_code()

stats = pstats.Stats(pr)
stats.sort_stats(pstats.SortKey.CUMULATIVE)
stats.print_stats(20)
```

Prefer built-ins and `itertools` over hand-rolled loops:
```python
import itertools

total = sum(item.price for item in cart)           # built-in C implementation
all_items = list(itertools.chain(list_a, list_b))  # no intermediate list
pairs = list(itertools.pairwise(sequence))          # Python 3.10+
```

Avoid unnecessary list comprehensions:
```python
# Wrong — creates list only to iterate
for item in [transform(x) for x in data]:
    process(item)

# Right — generator
for item in (transform(x) for x in data):
    process(item)

# Wrong — creates list for membership check
if value in [x.id for x in users]:
    ...

# Right — any()
if any(x.id == value for x in users):
    ...
```

---

## 12. Testing

Structure tests to mirror source:
```
src/my_package/models.py     →  tests/test_models.py
src/my_package/utils.py      →  tests/test_utils.py
```

Use `pytest` — never `unittest` for new code:
```python
# conftest.py
import pytest
from my_package.db import Session

@pytest.fixture
def db_session():
    with Session() as session:
        yield session
        session.rollback()
```

Test behaviour, not implementation:
```python
# Wrong — tests internal state
def test_cache_populated():
    service = UserService()
    service.get_user(1)
    assert 1 in service._cache   # fragile

# Right — tests observable behaviour
def test_repeated_call_returns_same_user():
    service = UserService()
    assert service.get_user(1) == service.get_user(1)
```

Use `parametrize` for multiple inputs:
```python
@pytest.mark.parametrize("email,is_valid", [
    ("user@example.com", True),
    ("not-an-email", False),
    ("", False),
])
def test_email_validation(email: str, is_valid: bool):
    assert validate_email(email) == is_valid
```

Mock external calls:
```python
from unittest.mock import patch

def test_fetch_user_handles_network_error():
    with patch('my_package.api.requests.get') as mock_get:
        mock_get.side_effect = ConnectionError("Network failure")
        with pytest.raises(ServiceUnavailableError):
            fetch_user(user_id=1)
```

---

## 13. Library & API Design

*Primary source: Kenneth Reitz, Ines Montani*

Write the README before you write the code. If you can't explain it in 5 lines, the API isn't ready.

The most common operation should be the shortest:
```python
# Wrong — always forces object construction
client = APIClient(base_url='https://api.example.com')
req = client.build_request('GET', '/users')
resp = client.send(req)

# Right — simple default, advanced escape hatch
data = api.get('/users').json()   # simple (90% of use cases)

with api.Session(base_url='...', timeout=30) as session:
    data = session.get('/users').json()  # advanced
```

No hidden state:
```python
# Wrong — surprising global state
import my_lib
my_lib.configure(api_key='secret')
my_lib.fetch_data()

# Right — explicit
client = my_lib.Client(api_key='secret')
client.fetch_data()
```

Opinionated defaults with escape hatches:
```python
# Wrong — too many required arguments
nlp = Pipeline(tokenizer='whitespace', tagger=True, parser=True, ner=True)

# Right — works immediately; override only when needed
nlp = spacy.load('en_core_web_sm')
```

---

## 14. Toolchain

| Tool | Purpose |
|---|---|
| **Black** | Auto-formatter |
| **isort** | Import sorting |
| **mypy** | Static type checking |
| **pytest** | Testing |
| **ruff** | Fast linting (replaces flake8) |
| **pre-commit** | Run checks before commit |
| **uv** | Package & env management |

Minimal `pyproject.toml` config:
```toml
[tool.black]
line-length = 88
target-version = ["py311"]

[tool.isort]
profile = "black"

[tool.mypy]
strict = true
python_version = "3.11"

[tool.ruff]
line-length = 88
select = ["E", "F", "W", "I", "N", "UP", "B", "C4", "PERF"]

[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = "--tb=short -q"
```

---

## 15. Anti-Patterns Catalogue

| Anti-pattern | Fix |
|---|---|
| `for i in range(len(x))` | `for item in x` or `enumerate(x)` |
| String concat in loop `x = x + s` | `''.join(parts)` |
| `except Exception: pass` | Catch specific exception, log, re-raise |
| Mutable default `def f(x=[])` | `def f(x=None): if x is None: x = []` |
| `from module import *` | Explicit imports only |
| Class with only `__init__` + one method | Refactor to a plain function |
| `if x == True` | `if x` |
| `if x == None` | `if x is None` |
| `len(x) == 0` | `if not x` |
| `list(x.keys())` for iteration | `for k in x` |
| `try/except` around everything | Narrow try block to one operation |
| God class with 20+ methods | Split into focused classes |
| Hardcoded magic numbers | Named constants: `MAX_RETRIES = 3` |
| Deeply nested code (>3 levels) | Early returns, guard clauses |
| Comments explaining *what* code does | Rename variables/functions |
| `print()` debugging in committed code | Use `logging` module |
| `list.pop(0)` for queue | `collections.deque` with `popleft()` |
| `df.iterrows()` in pandas | Vectorized operations |
| Relative imports across packages | Absolute imports |
| No type annotations on public API | Annotate all public signatures |
