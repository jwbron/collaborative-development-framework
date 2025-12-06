# Foundational Technical Requirements for Post-LLM Software Engineering

**Status:** Draft
**Author:** James Wiesebron, james-in-a-box
**Created:** December 2025
**Purpose:** Strategic technical vision for implementing Post-LLM Software Engineering

---

> **This document bootstraps itself.** It defines the Collaborative Planning Framework, then uses that framework to plan its own implementation. Once approved, we build using the methodology established here.

---

## The Collaborative Planning Framework

Before diving into technical requirements, we establish the methodology for developing with LLMs. This framework governs how all subsequent planning—including the rest of this document—should proceed.

### The Four Phases

> **Note on Phase Terminology:** CPF has two complementary phase sequences depending on context. This document uses the **strategic planning** phases (IDEATION → ASSESSMENT → REINFORCEMENT → PLANNING) for evaluating and shaping new initiatives. The [Human-Driven Development](Human-Driven-LLM-Navigated-Software-Development.md) document describes the **implementation workflow** phases (ELICITATION → DESIGN → PLANNING → HANDOFF) for executing approved work. Both are part of CPF—one for deciding *what* to build, the other for *how* to build it.

```
┌─────────────────────────────────────────────────────────────────┐
│         Collaborative Planning Framework (CPF)                  │
│              Strategic Planning Phases                          │
│                                                                 │
│   IDEATION ──▶ ASSESSMENT ──▶ REINFORCEMENT ──▶ PLANNING       │
│                                                                 │
│   "What if..."  "Is this      "Let's sharpen   "Here's how     │
│                  valuable?"    this concept"    we build it"    │
└─────────────────────────────────────────────────────────────────┘
```

| Phase | Purpose | Key Activities | Output |
|-------|---------|----------------|--------|
| **Ideation** | Generate possibilities | Brainstorm, explore adjacent ideas, identify opportunities | Raw concepts and directions |
| **Assessment** | Evaluate value and feasibility | Analyze trade-offs, estimate effort, identify risks | Go/no-go decision with rationale |
| **Reinforcement** | Sharpen the concept | Clarify requirements, resolve ambiguities, build shared understanding | Crisp problem statement and success criteria |
| **Planning** | Design implementation | Break down into phases, identify dependencies, create actionable spec | Implementation-ready specification |

### How This Framework Applies to This Document

This document is the first artifact built using CPF:

- **Ideation**: The vision for Post-LLM SE emerged from observing LLM development patterns
- **Assessment**: We determined this vision is both valuable and achievable
- **Reinforcement**: This document sharpens the concept into six technical foundations
- **Planning**: The high-level plan below will guide implementation

**Once this document is approved**, detailed design documents for each foundation will follow the same CPF process.

---

## The Documents-as-Code Paradigm

> **Core Insight:** In post-LLM software engineering, **documents ARE the code**.

| Traditional | Documents-as-Code |
|-------------|-------------------|
| Code first, docs later | Docs first, code follows |
| Docs describe implementation | Docs specify intent |
| Docs drift from reality | Docs are source of truth |
| Humans read, code | LLMs read docs, generate code |

**Why this matters:** A well-structured document is directly executable by an LLM. The specification *is* the program. This document will drive LLM agents to build the system it describes.

---

## Strategic Overview: The Six Foundations

The Post-LLM SE vision requires six foundational capabilities:

```
┌─────────────────────────────────────────────────────────────────────┐
│                    The Six Foundations                              │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐    │
│  │           1. MULTI-AGENT FRAMEWORK                          │    │
│  │              The execution layer powering everything        │    │
│  └───────┬──────────────┬──────────────┬──────────────┬────────┘    │
│          │              │              │              │             │
│          ▼              ▼              ▼              ▼             │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐  ┌────────────┐     │
│  │ 2. CPF     │  │ 3. PR      │  │ 4. CODE    │  │ 5. SELF-   │     │
│  │ Collab.    │  │ REVIEWER   │  │ ANALYSIS   │  │ REFLECTION │     │
│  │ Planning   │  │ System     │  │ Engine     │  │ Framework  │     │
│  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘     │
│         │               │               │               │           │
│         └───────────────┴───────┬───────┴───────────────┘           │
│                                 │                                   │
│                                 ▼                                   │
│         ┌───────────────────────────────────────────────┐           │
│         │      6. INDEX-BASED DOCUMENTATION             │           │
│         │         The navigation layer                  │           │
│         └───────────────────────────────────────────────┘           │
└─────────────────────────────────────────────────────────────────────┘
```

| Foundation | What It Does | Enables |
|------------|--------------|---------|
| **Multi-Agent Framework** | Coordinates specialized LLM agents | All capabilities |
| **Collaborative Planning Framework** | Structured human-LLM collaboration | Pillar 2 |
| **PR Reviewer System** | Automated specialized code review | Pillar 1 |
| **Codebase Analysis Engine** | Deep code understanding | All capabilities |
| **Continual Self-Reflection** | Autonomous system improvement | Pillar 3 |
| **Index-Based Documentation** | Always-current navigation | Pillars 1, 2 |

---

## Foundation 1: Multi-Agent Framework

**Purpose:** Provide infrastructure for coordinating multiple specialized LLM agents.

**Strategic Intent:** Enable complex tasks to be decomposed and handled by purpose-built agents working in concert.

### Key Capabilities

- **Agent Registry**: Catalog of agents with capabilities and constraints
- **Task Routing**: Match tasks to appropriate agents
- **Agent Communication**: Enable handoffs and context sharing
- **Observability**: Full execution tracing and token tracking

### Initial Agent Types

| Agent | Role |
|-------|------|
| Orchestrator | Task decomposition and delegation |
| Researcher | Information gathering |
| Implementer | Code generation |
| Reviewer | Code analysis |
| Documenter | Documentation maintenance |

### Open Questions

- How much context should agents share?
- When should agents run in parallel vs. sequential?
- How do agents recover from failures?

---

## Foundation 2: Collaborative Planning Framework (CPF)

**Purpose:** Enable rigorous human-LLM collaboration through structured dialogue and documentation-driven development.

**Strategic Intent:** Transform vague human intent into precise, executable specifications through the four-phase process (Ideation → Assessment → Reinforcement → Planning).

### Documentation-Driven Development

> **Core Philosophy:** Documentation isn't created after development—documentation IS development.

In the CPF model, **documentation drives development**, not the reverse:

| Traditional Development | Documentation-Driven Development |
|------------------------|----------------------------------|
| Write code, then document | Write spec document, code follows |
| Documentation is overhead | Documentation is the work product |
| Docs get stale | Docs are source of truth |
| Implementation defines behavior | Documents define behavior |

**Why this matters for CPF:**
- The **Planning** phase produces a document that IS the implementation spec
- LLM agents read the document and generate code from it
- Human approval of the document = approval to build
- Changes to behavior start with changes to documents

### Key Capabilities

- **Phase Management**: Guide conversations through CPF phases
- **Decision Capture**: Record human decisions with rationale
- **Context Persistence**: Maintain state across sessions
- **Specification Output**: Generate machine-readable task specs
- **Document Generation**: Output approved plans as versioned specification documents

### Human Checkpoints

| Checkpoint | When | Human Action |
|------------|------|--------------|
| Phase Transition | End of each phase | Approve to proceed |
| Design Decision | Multiple valid options | Choose direction |
| Risk Escalation | Uncertainty detected | Provide guidance |
| Document Approval | Planning phase complete | Sign off on spec |

### Open Questions

- How structured should the dialogue be?
- How are planning artifacts versioned?
- How does CPF integrate with task tracking?
- How do we ensure document quality is sufficient for LLM consumption?

---

## Foundation 3: PR Reviewer System

**Purpose:** Automated, specialized code review that catches issues before human review.

**Strategic Intent:** Implement "LLM-first, human-last" review where LLMs handle comprehensive analysis and humans focus on critical paths.

### Specialized Reviewers

| Reviewer | Focus |
|----------|-------|
| Security | OWASP Top 10, secrets, injection |
| Infrastructure | Resource limits, deployment safety |
| Product | Business logic, requirement alignment |
| Architecture | Patterns, coupling, tech debt |
| Nitpicker | Style, naming, documentation |

### Key Capabilities

- **Review Synthesis**: Aggregate and deduplicate findings
- **Severity Classification**: Critical, warning, suggestion
- **Feedback Learning**: Turn recurring human feedback into automated checks
- **GitHub Integration**: Native review API integration

### Open Questions

- Should reviewers share context or review independently?
- What's the maximum PR size for automated review?
- How do we minimize false positives?

---

## Foundation 4: Codebase Analysis Engine

**Purpose:** Deep, structured understanding of code.

**Strategic Intent:** Power code-aware operations across all foundations by maintaining rich knowledge of codebase structure, patterns, and relationships.

### Key Capabilities

- **Syntax Analysis**: AST parsing across languages
- **Semantic Analysis**: Types, symbols, relationships
- **Dependency Mapping**: Module relationships
- **Pattern Detection**: Common patterns and anti-patterns
- **Change Impact Analysis**: What's affected by changes

### Language Priorities

| Priority | Languages |
|----------|-----------|
| P0 | Python, TypeScript/JavaScript |
| P1 | Go |
| P2 | Java, others |

### Open Questions

- In-memory, filesystem, or database storage?
- How to efficiently support incremental analysis?
- How to handle monorepos?

---

## Foundation 5: Continual Self-Reflection Framework

**Purpose:** Enable the system to observe and improve itself.

**Strategic Intent:** Implement Pillar 3 (Radical Self-Improvement) where the system detects inefficiencies and proposes improvements.

### Key Capabilities

- **Metrics Collection**: Token usage, success rates, error patterns
- **Inefficiency Detection**: Identify wasted effort patterns
- **Improvement Proposals**: Generate hypotheses with estimated impact
- **Experiment Tracking**: Record what works and what doesn't

### Analysis Components

| Component | Purpose |
|-----------|---------|
| Process Analyzer | Detect workflow inefficiencies |
| LLM Inefficiency Analyzer | Identify token waste, unnecessary rework |
| PR Review Reviewer | Learn from human review feedback |
| Documentation Analyzer | Find doc-code drift |

### Open Questions

- What changes require human approval?
- How do we rollback improvements that don't work?
- How quickly can we measure if changes help?

---

## Foundation 6: Index-Based Documentation Strategy

**Purpose:** Maintain always-current, navigable documentation.

**Strategic Intent:** Keep humans and LLMs oriented through automated index generation and drift detection.

### Key Capabilities

- **Automated Index Generation**: Index from directory structure
- **Cross-Reference Maintenance**: Detect and maintain links
- **Drift Detection**: Find where docs diverge from code
- **Freshness Tracking**: Flag stale documentation

### Open Questions

- Markdown files as source of truth, or generate from code?
- Real-time vs. batch index updates?
- How do LLMs best consume documentation?

---

## High-Level Implementation Plan

> **Principle:** Each phase produces working documents AND working code. Documents evolve alongside implementation.

### Phase Sequence

```
Phase 0: Strategic Foundation (THIS DOCUMENT)
    ↓
Phase 1: Multi-Agent Core
    ↓
Phase 2: Collaborative Planning Framework ←── Enables structured development
    ↓
Phase 3: PR Review Pipeline ←── Quality gate for all subsequent work
    ↓
Phase 4: Codebase Analysis
    ↓
Phase 5: Self-Reflection
    ↓
Phase 6: Documentation Intelligence
```

### Phase 0: Strategic Foundation (Current)

**Goal:** Establish the vision and methodology.

**Deliverables:**
- This document (Foundational Technical Requirements)
- Umbrella document (Pragmatic Guide)
- Pillar documents (LLM-First Reviews, Human-Driven Development, Self-Improvement)

**Exit Criteria:**
- Human approval of strategic direction
- Clear methodology (CPF) established
- Six foundations defined at appropriate level of abstraction

### Phase 1: Multi-Agent Core

**Goal:** Build the execution layer that powers everything.

**Key Deliverables:**
- Agent registry and lifecycle management
- Task routing infrastructure
- Execution tracing

**Exit Criteria:**
- Can register, route to, and execute agents
- At least 3 working agents (Orchestrator, Implementer, Researcher)

### Phases 2-6: To Be Planned

Detailed planning for subsequent phases will follow IPF:
1. Each phase gets its own Ideation → Assessment → Reinforcement → Planning cycle
2. Phase planning happens just-in-time, informed by learnings from prior phases
3. Human checkpoints at each phase transition

---

## Cross-Cutting Concerns

### Security
- Never expose secrets
- Sandbox code execution
- Authenticate all API calls

### Scalability
- Support large codebases through incremental analysis
- Manage token costs through caching and budgets

### Reliability
- Graceful degradation on agent failures
- Full audit trail for accountability

---

## Success Metrics

### North Star
| Metric | Target |
|--------|--------|
| Human cognitive load reduction | -50% |
| Defect escape rate | -30% |
| Development velocity | +25% |

### Foundation-Specific
| Foundation | Metric | Target |
|------------|--------|--------|
| Multi-Agent | Task completion rate | >95% |
| CPF | First-attempt success | >80% |
| PR Review | Issues caught pre-human | >70% |
| Self-Reflection | Proposals accepted | >60% |

---

## Open Strategic Questions

1. **Build vs. Buy**: Which components warrant custom development?
2. **LLM Provider**: How dependent on a single provider?
3. **Open Source**: Which components should be public?
4. **Rollout**: How do we introduce capabilities incrementally?

---

## What's Next

**Immediate actions upon approval:**
1. Merge this document
2. Begin Phase 1 (Multi-Agent Core) design using IPF
3. Establish document review cadence

**This document will evolve** as implementation reveals gaps and new insights emerge. That's intentional—documents and code evolve together.

---

## Related Documents

| Document | Purpose |
|----------|---------|
| [Pragmatic Guide](Pragmatic-Guide-Software-Engineering-Post-LLM-World.md) | Philosophy umbrella |
| [LLM-First Code Reviews](LLM-Assisted-Code-Review.md) | Pillar 1 |
| [Human-Driven Development](Human-Driven-LLM-Navigated-Software-Development.md) | Pillar 2 |
| [Radical Self-Improvement](Radical-Self-Improvement-for-LLMs.md) | Pillar 3 |

---

**Last Updated:** 2025-12-06
**Next Review:** After human approval

Authored-by: jib
