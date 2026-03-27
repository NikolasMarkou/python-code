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
