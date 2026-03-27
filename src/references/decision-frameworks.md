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
