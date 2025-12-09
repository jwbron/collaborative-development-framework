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
│   IDEATION ──▶ ASSESSMENT ──▶ REINFORCEMENT ──▶ PLANNING        │
│                                                                 │
│   "What if..."  "Is this      "Let's sharpen   "Here's how      │
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
- **Reinforcement**: This document sharpens the concept into seven technical foundations
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

## Strategic Overview: The Seven Foundations

The Post-LLM SE vision requires seven foundational capabilities:

```
┌─────────────────────────────────────────────────────────────────────┐
│                    The Seven Foundations                            │
│                                                                     │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │              FOUNDATIONAL LAYER (Build First)                 │  │
│  │  ┌─────────────────────────┐  ┌─────────────────────────┐     │  │
│  │  │ 1. DOCUMENTATION        │  │ 2. COLLABORATIVE        │     │  │
│  │  │    INTELLIGENCE         │  │    PLANNING (CPF)       │     │  │
│  │  │    LLMs need context    │  │    Humans drive intent  │     │  │
│  │  └───────────┬─────────────┘  └─────────────┬───────────┘     │  │
│  └──────────────┼──────────────────────────────┼─────────────────┘  │
│                 │                              │                    │
│                 └──────────────┬───────────────┘                    │
│                                ▼                                    │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │           3. MULTI-AGENT FRAMEWORK                            │  │
│  │              The execution layer enabling all capabilities    │  │
│  └───────┬──────────────┬──────────────┬─────────────────────────┘  │
│          │              │              │                            │
│          ▼              ▼              ▼                            │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐                     │
│  │ 4. PR      │  │ 5. CODE    │  │ 6. SELF-   │                     │
│  │ REVIEWER   │  │ ANALYSIS   │  │ REFLECTION │                     │
│  │ System     │  │ Engine     │  │ Framework  │                     │
│  └────────────┘  └────────────┘  └────────────┘                     │
│                                                                     │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │           7. SECURITY ARCHITECTURE                            │  │
│  │              Cross-cutting concern enabling safe autonomy     │  │
│  │              Sandboxing | Permissions | Ephemerality | Audit  │  │
│  └───────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
```

> **Why Documentation Intelligence and CPF are Foundational:**
> Without proper documentation, LLMs cannot efficiently understand or navigate codebases. Without structured collaboration (CPF), human intent cannot be reliably translated into LLM action. These two capabilities are prerequisites for everything else—we use them to build and maintain all other foundations.

| Foundation | What It Does | Enables |
|------------|--------------|---------|
| **Documentation Intelligence** | Always-current, navigable documentation | LLM context, all capabilities |
| **Collaborative Planning Framework** | Structured human-LLM collaboration | Pillar 2, all planning |
| **Multi-Agent Framework** | Coordinates specialized LLM agents | All capabilities |
| **PR Reviewer System** | Automated specialized code review | Pillar 1 |
| **Codebase Analysis Engine** | Deep code understanding | All capabilities |
| **Continual Self-Reflection** | Autonomous system improvement | Pillar 3 |
| **Security Architecture** | Safe autonomous agent operation | All capabilities (cross-cutting) |

---

## Foundation 1: Documentation Intelligence

**Purpose:** Maintain always-current, navigable documentation that enables LLM efficiency.

**Strategic Intent:** Keep humans and LLMs oriented through automated index generation, drift detection, and context provision. Without proper documentation, LLMs cannot efficiently understand or navigate codebases.

### Why Documentation Intelligence is Foundational

> **Core Insight:** Documentation is not an afterthought—it's the primary interface between human intent and LLM capability.

LLMs are only as effective as the context they receive. Documentation Intelligence ensures:
- LLMs always have accurate, up-to-date context about the codebase
- Humans can navigate and understand what exists before planning new work
- All other foundations can leverage reliable documentation for their operations

### Key Capabilities

- **Automated Index Generation**: Index from directory structure
- **Cross-Reference Maintenance**: Detect and maintain links
- **Drift Detection**: Find where docs diverge from code
- **Freshness Tracking**: Flag stale documentation
- **Context Provision**: Supply relevant documentation to LLM agents

### Open Questions

- Markdown files as source of truth, or generate from code?
- Real-time vs. batch index updates?
- How do LLMs best consume documentation?
- What documentation coverage is "good enough" to start?

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

## Foundation 3: Multi-Agent Framework

**Purpose:** Provide infrastructure for coordinating multiple specialized LLM agents.

**Strategic Intent:** Enable complex tasks to be decomposed and handled by purpose-built agents working in concert. This framework leverages Documentation Intelligence for context and CPF for planning, then enables all downstream capabilities.

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

## Foundation 4: PR Reviewer System

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

## Foundation 5: Codebase Analysis Engine

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

## Foundation 6: Continual Self-Reflection Framework

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

## Foundation 7: Security Architecture

**Purpose:** Provide the architectural foundations for secure autonomous agent operation.

**Strategic Intent:** Enable LLM agents to operate with meaningful autonomy while maintaining security guarantees that don't depend on the agent's cooperation. Security must be enforced by design, not by instruction.

> **Core Principle:** Safety by design, not by instruction. Security constraints must be architectural—enforced by the system itself—not merely instructed in prompts that can be bypassed.

### Why Security Architecture is Cross-Cutting

Security cannot be an afterthought. Every foundation depends on security guarantees:
- Documentation Intelligence must protect sensitive information
- CPF must ensure human oversight points cannot be bypassed
- Multi-Agent Framework must isolate agents and control their capabilities
- PR Reviewer must have secure access to code without exposure risks
- Codebase Analysis must handle code without executing untrusted content
- Self-Reflection must not be manipulable by adversarial input

### Key Capabilities

| Capability | What It Does | Why It Matters |
|------------|--------------|----------------|
| **Sandboxing** | Isolates agent execution environments | Limits blast radius of any compromise |
| **Permission System** | Fine-grained, time-limited access control | Enforces least privilege |
| **Ephemeral Execution** | Stateless, disposable environments | Prevents persistent compromise |
| **Data Protection** | Prevents unauthorized data access/exfiltration | Protects sensitive information |
| **Secret Management** | Secure credential handling | Prevents credential exposure |
| **Audit System** | Comprehensive logging and monitoring | Enables detection and forensics |
| **Human Oversight** | Integration points for human review | Maintains accountability |

### Core Security Principles

1. **Defense in Depth**: Multiple independent safeguards for every security-critical operation
2. **Least Privilege**: Minimum permissions necessary, scoped, time-bound, revocable
3. **Assume Compromise**: Design to limit blast radius and enable recovery
4. **Explicit Over Implicit**: Architectural enforcement, not prompt instructions
5. **Reversibility**: Prefer soft deletes, backups, and human approval for irreversible actions

### Open Questions

- How do we stay ahead of container escape vulnerabilities?
- What novel attack vectors are unique to LLM agents?
- How do we minimize security overhead while maintaining protection?
- How do we secure agent-to-agent communication in multi-agent scenarios?

---

## High-Level Implementation Plan

> **Principle:** Each phase produces working documents AND working code. Documents evolve alongside implementation.

### Phase Sequence

```
Phase 0: Strategic Foundation (THIS DOCUMENT)
    ↓
Phase 1: Documentation Intelligence ←── LLMs need context to function
    ↓
Phase 2: Collaborative Planning Framework ←── Humans drive intent, LLMs execute
    ↓
Phase 3: Multi-Agent Core ←── Execution layer (uses Phases 1 & 2)
    ↓
Phase 4: PR Review Pipeline ←── Quality gate for all subsequent work
    ↓
Phase 5: Codebase Analysis
    ↓
Phase 6: Self-Reflection
```

> **Why this order?** Documentation Intelligence and CPF are the two foundational capabilities that enable everything else. Without proper documentation, LLMs cannot efficiently navigate codebases. Without structured collaboration, human intent cannot be reliably translated into action. We use these two core functions to build and maintain all other foundations.

### Phase 0: Strategic Foundation (Current)

**Goal:** Establish the vision and methodology.

**Deliverables:**
- This document (Foundational Technical Requirements)
- Umbrella document (Pragmatic Guide)
- Pillar documents (LLM-First Reviews, Human-Driven Development, Self-Improvement)

**Exit Criteria:**
- Human approval of strategic direction
- Clear methodology (CPF) established
- Seven foundations defined at appropriate level of abstraction

### Phase 1: Documentation Intelligence

**Goal:** Establish the documentation foundation that enables LLM efficiency.

**Key Deliverables:**
- Automated index generation from directory structure
- Cross-reference and link maintenance
- Drift detection between docs and code
- Freshness tracking system

**Exit Criteria:**
- Documentation index is auto-generated and current
- LLMs can reliably find relevant context
- Stale documentation is flagged

### Phase 2: Collaborative Planning Framework

**Goal:** Enable structured human-LLM collaboration for all subsequent work.

**Key Deliverables:**
- Phase management (Ideation → Assessment → Reinforcement → Planning)
- Decision capture with rationale
- Context persistence across sessions
- Specification output generation

**Exit Criteria:**
- Can guide conversations through CPF phases
- Human decisions are captured with rationale
- Planning artifacts are versioned and machine-readable

### Phase 3: Multi-Agent Core

**Goal:** Build the execution layer that powers all capabilities.

**Key Deliverables:**
- Agent registry and lifecycle management
- Task routing infrastructure
- Execution tracing

**Exit Criteria:**
- Can register, route to, and execute agents
- At least 3 working agents (Orchestrator, Implementer, Researcher)

### Phases 4-6: To Be Planned

Detailed planning for subsequent phases will follow CPF:
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
| Metric | Direction |
|--------|-----------|
| Human cognitive load | Reduced |
| Defect escape rate | Reduced |
| Development velocity | Increased |

### Foundation-Specific
| Foundation | Metric | Direction |
|------------|--------|-----------|
| Documentation Intelligence | Context relevance | High |
| CPF | First-attempt success | High |
| Multi-Agent | Task completion rate | High |
| PR Review | Issues caught pre-human | Majority |
| Codebase Analysis | Query accuracy | High |
| Self-Reflection | Proposals accepted | Majority |
| Security Architecture | Security incidents | Zero critical |

Organizations should define specific targets based on their baseline measurements and context.

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
2. Begin Phase 1 (Documentation Intelligence) design using CPF
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

Authored-by: jib
