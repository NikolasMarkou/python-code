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

```python
# orm.py — infrastructure imports domain (not the other way around)
from myapp.domain.model import Batch, OrderLine

def start_mappers():
    mapper_registry.map_imperatively(OrderLine, order_lines)
    mapper_registry.map_imperatively(
        Batch, batches,
        properties={'_allocations': relationship(OrderLine)},
    )
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
