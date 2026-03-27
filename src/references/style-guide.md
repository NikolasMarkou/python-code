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
