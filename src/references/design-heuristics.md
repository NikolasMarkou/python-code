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
