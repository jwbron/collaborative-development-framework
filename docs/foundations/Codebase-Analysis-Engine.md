# Codebase Analysis Engine

**Status:** Specification Draft
**Foundation:** 5 of 7
**Purpose:** Enable deep, structured understanding of code for LLM-powered software engineering

---

## What This Document Is

This specification describes the **Codebase Analysis Engine**—a foundational capability that enables LLMs and humans to develop rich, queryable understanding of codebases. It defines patterns, concerns, and design concepts that guide implementation without prescribing specific technologies or architectures.

**Intended audience:** Humans and LLMs collaborating on implementation. This is not an ADR or detailed design document—it's a conceptual foundation for creative, context-appropriate implementation.

---

## The Core Problem

LLMs working with code face a fundamental constraint: **limited context windows**. Even as context windows grow, codebases grow faster. A codebase analysis engine solves this by:

1. **Pre-computing understanding** so LLMs don't re-analyze the same code repeatedly
2. **Enabling targeted queries** so LLMs receive only relevant context
3. **Maintaining freshness** so analysis reflects current code state
4. **Surfacing relationships** that aren't visible from reading individual files

Without this capability, LLMs waste tokens re-discovering what they've already learned, miss important connections across files, and operate on stale mental models of the codebase.

---

## Design Philosophy

### Analysis is a Service, Not a Product

The engine exists to serve other capabilities—PR review, code generation, documentation intelligence, self-reflection. Design decisions should optimize for **consumer utility**, not analysis completeness for its own sake.

### Progressive Depth Over Exhaustive Breadth

Not all code needs the same analysis depth. A function called once during initialization needs less attention than a core utility called from hundreds of locations. The engine should support multiple analysis depths and allow consumers to request "deeper" analysis on demand.

### Eventual Consistency is Acceptable

Real-time synchronization with every file change is expensive and often unnecessary. Analysis can lag slightly behind code changes as long as:
- The lag is bounded and predictable
- Consumers know when they're working with stale data
- Critical paths (like active PR files) can trigger immediate refresh

### Prefer Derivable Over Stored

When information can be derived quickly from stored primitives, derive it rather than store it. This reduces storage complexity and ensures derived information stays fresh. Store raw analysis; derive insights.

---

## Core Concepts

### The Analysis Graph

At its heart, the engine maintains a **graph of code entities and their relationships**:

```
┌─────────────────────────────────────────────────────────────────┐
│                     Analysis Graph                              │
│                                                                 │
│   Nodes (Entities)              Edges (Relationships)           │
│   ─────────────────            ──────────────────────           │
│   • Files                      • imports/exports                │
│   • Modules/Packages           • calls                          │
│   • Functions/Methods          • inherits/implements            │
│   • Classes/Types              • references                     │
│   • Variables/Constants        • contains                       │
│   • Interfaces/Protocols       • depends-on                     │
│                                • tests                          │
└─────────────────────────────────────────────────────────────────┘
```

This graph is the primitive from which all higher-level analysis derives.

### Entity Identity

Each code entity needs a stable identity that survives:
- Renames (within reason)
- Moves between files
- Refactoring

**Pattern: Content-Addressed Identity with Location Hints**

Combine structural fingerprinting (the shape of the code) with location information (where it lives). When code moves, the fingerprint helps re-identify it; when code changes slightly, the location helps track it.

### Analysis Layers

Analysis proceeds in layers, each building on the previous:

| Layer | What It Captures | Example Output |
|-------|------------------|----------------|
| **Syntactic** | Structure, tokens, AST | "This is a function with 3 parameters" |
| **Semantic** | Types, symbols, meaning | "This function takes a User and returns a Result<Order>" |
| **Relational** | Connections between entities | "This function is called by 47 other functions" |
| **Behavioral** | What code does | "This function validates input then persists to database" |
| **Quality** | Patterns, anti-patterns, metrics | "This function has cyclomatic complexity of 15" |

**Not every consumer needs every layer.** A quick search needs only syntactic; impact analysis needs relational; code review needs all five.

---

## Key Capabilities

### 1. Incremental Analysis

**Pattern: Dependency-Aware Invalidation**

When a file changes, determine what analysis is invalidated:
- Direct: The file's own analysis
- Transitive: Analysis of files that depend on changed exports

This requires tracking not just what entities exist, but what other entities' analysis depends on them.

**Concern: Invalidation Cascades**

A change to a widely-used utility could theoretically invalidate analysis across the entire codebase. Strategies to mitigate:
- **Signature-based invalidation**: Only invalidate if the public interface changed
- **Lazy re-analysis**: Mark as stale but don't re-analyze until requested
- **Bounded staleness**: Accept some staleness in exchange for stability

### 2. Multi-Language Support

**Pattern: Language Adapters**

Each language provides an adapter that maps language-specific syntax to universal analysis concepts:

```
┌──────────────────────────────────────────────────────────────┐
│                    Universal Analysis Model                  │
│  (Functions, Classes, Types, Imports, Calls, etc.)          │
└──────────────────────────────────────────────────────────────┘
                              ▲
           ┌──────────────────┼──────────────────┐
           │                  │                  │
    ┌──────┴──────┐    ┌──────┴──────┐    ┌──────┴──────┐
    │   Python    │    │  TypeScript │    │     Go      │
    │   Adapter   │    │   Adapter   │    │   Adapter   │
    └─────────────┘    └─────────────┘    └─────────────┘
```

**Concern: Semantic Mismatch**

Not all language concepts map cleanly:
- Python's duck typing vs. TypeScript's structural typing
- Go's implicit interfaces vs. Java's explicit interfaces
- JavaScript's prototypes vs. class-based inheritance

**Design Principle:** Capture language-specific nuance in adapter metadata, but provide a universal query interface. Consumers can ignore language differences for most queries, but access specifics when needed.

### 3. Change Impact Analysis

Given a proposed change, answer: "What else might be affected?"

**Pattern: Ripple Analysis**

1. Identify directly changed entities
2. Find all entities that reference changed entities
3. Assess likelihood and severity of impact
4. Recurse to configurable depth

**Output should include:**
- Definitely affected (direct callers of changed function)
- Possibly affected (callers of callers, depending on change type)
- Test coverage of affected areas
- Suggested review focus areas

### 4. Pattern Detection

Identify both positive patterns (to reinforce) and anti-patterns (to flag).

**Concern: False Positives**

Pattern detection can be noisy. Mitigate by:
- **Confidence scores**: Not "this IS an anti-pattern" but "73% confidence this matches anti-pattern X"
- **Context awareness**: Some patterns are anti-patterns in some contexts but appropriate in others
- **Learning from feedback**: When humans override pattern findings, learn from it

**Pattern: Configurable Pattern Library**

Patterns should be defined declaratively and customizable per-codebase. A pattern that's an anti-pattern in one organization may be standard practice in another.

### 5. Natural Language Queries

Enable queries like:
- "Where is user authentication handled?"
- "What functions modify the shopping cart?"
- "Show me all error handling for database operations"

**Pattern: Query Translation Pipeline**

```
Natural Language → Intent Classification → Structured Query → Graph Traversal → Results
```

This isn't magic—it requires:
- Good entity naming (relies on Documentation Intelligence)
- Semantic understanding of code purpose (from behavioral analysis)
- Query refinement when initial results are too broad/narrow

**Concern: Ambiguity**

"Where is authentication handled?" could mean:
- The login flow
- Token validation middleware
- Session management
- All of the above

**Design Principle:** When ambiguous, return multiple interpretations with confidence scores rather than guessing. Let the consumer (human or LLM) disambiguate.

---

## Integration Points

### With Documentation Intelligence (Foundation 1)

- Documentation provides human-readable context for code entities
- Analysis engine provides code-level detail that docs reference
- Drift detection: When code changes but docs don't (or vice versa)

### With PR Reviewer (Foundation 4)

- Analysis enables focused review (only analyze changed code + impact radius)
- Pattern detection feeds directly into review findings
- Change impact informs which reviewers should examine which parts

### With Multi-Agent Framework (Foundation 3)

- Analysis results feed into agent context
- Agents can request deeper analysis on specific areas
- Analysis engine may itself be implemented as agents

### With Self-Reflection (Foundation 6)

- Analysis quality metrics feed improvement proposals
- Pattern detection results inform coding standards evolution
- Query patterns reveal what information LLMs need most

---

## Operational Concerns

### Storage Strategy

**Options with trade-offs:**

| Strategy | Pros | Cons |
|----------|------|------|
| In-memory | Fast queries | Limited scale, cold start cost |
| File-based | Simple, portable | Query complexity |
| Database | Rich queries, scales | Infrastructure overhead |
| Hybrid | Balanced | Implementation complexity |

**Design Principle:** Start simple (file-based), measure actual query patterns, evolve storage based on evidence.

### Performance Budgets

Analysis has costs:
- **Time**: Initial analysis of large codebase
- **Storage**: Persisting analysis results
- **Compute**: Ongoing incremental analysis

Set explicit budgets:
- Initial analysis: proportional to codebase size, bounded
- Incremental update: sub-second for typical changes
- Query response: fast enough that LLMs don't timeout

### Freshness Guarantees

| Scenario | Freshness Target |
|----------|------------------|
| Active PR files | Real-time (seconds) |
| Recently modified | Near-real-time (minutes) |
| Stable code | Eventual (hours acceptable) |

**Pattern: Tiered Freshness**

Maintain different update frequencies for different code areas based on activity patterns.

---

## Anti-Patterns to Avoid

### Analysis Hoarding

Building comprehensive analysis "because we might need it" without clear consumers. Analysis exists to serve; unneeded analysis is waste.

### Precision Fetishism

Pursuing perfect analysis at the cost of utility. A fast, 90% accurate analysis that's always available beats a 99% accurate analysis that takes hours.

### Language Lock-in

Building deep integration with one language's tooling at the expense of the universal model. Language-specific depth is good; language-specific interfaces are problematic.

### Stale is Broken

Treating stale analysis as unusable. Stale analysis with a timestamp is often more useful than no analysis while awaiting refresh.

---

## Open Design Questions

These questions are intentionally left open for implementers to resolve based on their context:

1. **Analysis Granularity**: Should the smallest unit be functions, or go finer (expressions, statements)?

2. **Cross-Repository Analysis**: How to handle codebases spanning multiple repositories?

3. **Generated Code**: Should generated code be analyzed? Indexed but not analyzed? Ignored entirely?

4. **Test Code**: Same analysis depth as production code, or specialized test-aware analysis?

5. **Historical Analysis**: Is analysis of past code states valuable? Git integration depth?

6. **Caching Strategy**: What's the right balance between storage cost and re-computation cost?

7. **Failure Modes**: How should partial analysis failures be handled and communicated?

---

## Success Indicators

Implementation is succeeding when:

- **LLMs request less raw code**: They're getting sufficient context from analysis
- **Impact predictions match reality**: Changed code correlates with predicted impact areas
- **Pattern detection has high signal-to-noise**: Findings are actionable, not ignored
- **Query response times stay bounded**: Performance doesn't degrade with codebase growth
- **Analysis freshness meets targets**: Tiered freshness guarantees are maintained

---

## Getting Started

For implementers beginning work on the Codebase Analysis Engine:

1. **Start with the graph primitives**: Get entity extraction and relationship mapping working for one language
2. **Add incremental updates early**: Don't build batch-only analysis that you'll have to retrofit
3. **Build query interface alongside storage**: Let query needs inform storage decisions
4. **Integrate with one consumer first**: PR Reviewer is a good first integration—concrete, bounded needs
5. **Measure everything**: You can't optimize what you don't measure

---

## Related Documents

| Document | Relationship |
|----------|--------------|
| [Foundational Technical Requirements](Foundational-Technical-Requirements.md) | Parent document defining all seven foundations |
| [LLM-Assisted Code Review](LLM-Assisted-Code-Review.md) | Primary consumer of analysis capabilities |
| [Radical Self-Improvement](Radical-Self-Improvement-for-LLMs.md) | Feedback loop for analysis quality |

---

Authored-by: jib
