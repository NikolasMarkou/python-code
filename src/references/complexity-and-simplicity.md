# Complexity and Simplicity

Deep reference material for complexity-related mental models.

## Table of Contents
1. Essential vs Accidental Complexity (Brooks)
2. Simple vs Easy (Hickey)
3. Three Symptoms of Complexity (Ousterhout)
4. Knowledge Degradation (Blow)
5. Hickey's Complecting Table

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
