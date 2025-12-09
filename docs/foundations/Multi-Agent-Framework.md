# Multi-Agent Framework

**Status:** Draft Specification
**Foundation:** 3 of 7 (see [Foundational Technical Requirements](../Foundational-Technical-Requirements.md))
**Purpose:** A spec for coordinating multiple specialized LLM agents to accomplish complex software engineering tasks

---

## What This Document Is

This is a **specification**—a set of concepts, patterns, and design concerns that enable implementers (human or LLM) to build a multi-agent framework suited to their context. It is intentionally:

- **Conceptual, not prescriptive**: We describe patterns and trade-offs, not a single implementation path
- **Flexible**: Different organizations will implement these concepts differently
- **Foundational**: This spec sits at the execution layer, enabling capabilities described in other foundations

This is **not** an architecture document, detailed design, or implementation guide. Those artifacts emerge from this spec through the Collaborative Planning Framework.

---

## Why Multi-Agent?

Single-agent systems—one LLM handling an entire task—hit fundamental limits:

| Limitation | Description |
|------------|-------------|
| **Context saturation** | Complex tasks require more context than fits efficiently in a single prompt |
| **Cognitive overload** | Asking one agent to simultaneously plan, implement, test, and review degrades all capabilities |
| **No specialization** | A generalist prompt cannot match purpose-built specialists |
| **Brittle failure** | If the single agent fails mid-task, all progress is lost |
| **No parallelism** | Sequential execution cannot leverage concurrent work |

Multi-agent systems address these by decomposing tasks across specialized agents that can work in parallel, maintain focused contexts, and recover from partial failures.

---

## Core Concepts

### Agents

An **agent** is a specialized LLM invocation with:

- **Identity**: A name, role description, and persona that shapes behavior
- **Capabilities**: What tools and actions the agent can take
- **Constraints**: Explicit boundaries on what the agent must NOT do
- **Context Requirements**: What information the agent needs to function
- **Output Contract**: The expected format and content of the agent's output
- **Termination Criteria**: When the agent should stop (success, failure, or timeout)

**Key Insight**: An agent is not just a prompt—it's a prompt combined with tools, permissions, and clear boundaries. The boundaries matter as much as the capabilities.

### Agent Categories

Agents generally fall into three categories based on their relationship with the codebase:

| Category | Relationship to Code | Examples |
|----------|---------------------|----------|
| **Reading Agents** | Observe but don't modify | Researcher, Analyzer, Planner |
| **Writing Agents** | Create or modify artifacts | Implementer, Tester, Documenter |
| **Reviewing Agents** | Evaluate quality | Reviewer, Auditor, Validator |

This categorization informs permission models—reading agents need fewer permissions than writing agents, and reviewing agents need observation capabilities plus scoring/feedback mechanisms.

### Pipelines

A **pipeline** is a structured sequence of agent invocations that accomplish a larger goal. Pipelines define:

- **Stages**: The discrete steps, each potentially handled by a different agent
- **Data Flow**: How outputs from one stage become inputs to the next
- **Execution Order**: Sequential, parallel, or conditional paths through stages
- **State Persistence**: How progress is tracked and recovered

**Key Insight**: Pipelines make the invisible visible. When a single agent handles a task, its internal "steps" are opaque. Pipelines externalize these steps, making them observable, resumable, and improvable.

### Orchestration

An **orchestrator** manages pipeline execution:

- Dispatches work to agents
- Manages state between stages
- Handles errors and retries
- Provides observability
- Enforces resource constraints

The orchestrator is itself not an agent—it's infrastructure that coordinates agents. This separation keeps agent prompts focused on their specific tasks rather than coordination logic.

---

## Design Patterns

### Pattern 1: Sequential Specialization

**Concept**: Complex tasks decompose into phases, each handled by a specialist.

```
Plan → Implement → Test → Review → Ship
```

**When to Use**:
- Tasks with natural phase boundaries
- When each phase benefits from distinct expertise
- When checkpointing between phases is valuable

**Trade-offs**:
- (+) Clear separation of concerns
- (+) Each agent can be optimized for its phase
- (+) Failures are localized to specific phases
- (-) Sequential execution means no parallelism
- (-) Context must be explicitly passed between phases

**Design Considerations**:
- How much context does each phase need from previous phases?
- What's the contract between phases (structured data, natural language, artifacts)?
- At which phases are human checkpoints most valuable?

### Pattern 2: Parallel Analysis

**Concept**: Independent analyses run concurrently, then synthesize.

```
┌─ Security Scan ──┐
│                  │
├─ Code Review ────┼─→ Synthesis
│                  │
└─ Test Coverage ──┘
```

**When to Use**:
- Multiple independent perspectives are valuable
- Latency is a concern
- Analyses don't depend on each other's results

**Trade-offs**:
- (+) Faster than sequential for independent work
- (+) Multiple perspectives catch different issues
- (-) Synthesis can be challenging
- (-) Resource usage spikes during parallel execution

**Design Considerations**:
- How are conflicting findings resolved?
- What happens if one parallel branch fails?
- How is the synthesis weighted across sources?

### Pattern 3: Iterative Refinement

**Concept**: An agent's output is evaluated, and if insufficient, the agent (or a fixer agent) tries again.

```
Generate → Evaluate → (if insufficient) → Fix → Evaluate → ...
```

**When to Use**:
- Tasks where "good enough" varies by context
- When evaluation is cheaper than generation
- When incremental improvement is possible

**Trade-offs**:
- (+) Graceful handling of imperfect first attempts
- (+) Quality improves through iteration
- (-) Unbounded iteration risk (must cap attempts)
- (-) Token usage increases with iterations

**Design Considerations**:
- What defines "good enough"?
- How many iterations before escalating or failing?
- Should the same agent retry, or hand off to a specialist fixer?

### Pattern 4: Hierarchical Delegation

**Concept**: A lead agent decomposes work and delegates to specialists.

```
Lead Agent
    ├─→ Specialist A (subtask 1)
    ├─→ Specialist B (subtask 2)
    └─→ Specialist C (subtask 3)
         └─→ Sub-specialist (sub-subtask)
```

**When to Use**:
- Complex tasks requiring decomposition
- When specialists have deep domain expertise
- When the decomposition itself requires judgment

**Trade-offs**:
- (+) Scales to complex tasks
- (+) Specialists can be highly optimized
- (-) Lead agent becomes a coordination bottleneck
- (-) Deep hierarchies increase latency and token usage

**Design Considerations**:
- How deep can hierarchies go before coordination overhead dominates?
- How does the lead agent decide what to delegate?
- What visibility does the lead have into specialist progress?

### Pattern 5: Consensus Building

**Concept**: Multiple agents independently address the same question, then compare.

```
Agent A proposes solution ──┐
Agent B proposes solution ──┼─→ Evaluate & Select
Agent C proposes solution ──┘
```

**When to Use**:
- High-stakes decisions
- Ambiguous requirements with multiple valid approaches
- When reducing bias matters

**Trade-offs**:
- (+) Higher quality through diversity
- (+) Reduces over-reliance on single perspective
- (-) Expensive (N times the work)
- (-) Selection/synthesis adds complexity

**Design Considerations**:
- How many perspectives are enough?
- How is the "best" selected (voting, scoring, synthesis)?
- What if all proposals are poor?

---

## Critical Design Concerns

### Context Engineering

**The Problem**: Agents can only act on context they receive. Too little context leads to poor decisions; too much overwhelms the model and wastes tokens.

**Design Considerations**:

- **Context hierarchy**: Distinguish between permanent context (identity, constraints), workflow context (pipeline goals, prior outputs), and task context (specific files, requirements)
- **Progressive disclosure**: Load detail incrementally as needed rather than upfront
- **Summarization**: Previous stage outputs may need compression before passing forward
- **Explicit boundaries**: Tell agents what they're NOT supposed to consider

**Key Insight**: Context engineering is as important as prompt engineering. A brilliant prompt with wrong context produces wrong results.

### State Management

**The Problem**: Pipelines may be interrupted (container restart, timeout, failure). Progress must not be lost.

**Design Considerations**:

- **Checkpointing**: After each stage, persist state to durable storage
- **Resumability**: Given checkpointed state, pipelines should resume from the last completed stage
- **State schema**: Define what constitutes pipeline state (stage outputs, metadata, error logs)
- **Conflict resolution**: If multiple executions touch the same state, how are conflicts resolved?

**Key Insight**: State management transforms pipelines from fragile single-shot attempts into robust, resumable workflows.

### Error Handling

**The Problem**: Agents fail. Networks fail. Timeouts happen. Outputs are malformed. The system must handle all of this gracefully.

**Design Considerations**:

- **Retry strategies**: Which errors are retryable? How many times? With what backoff?
- **Fallback paths**: When an agent fails, is there a simpler fallback?
- **Partial failure in parallel stages**: If 2/3 parallel analyses succeed, proceed or fail?
- **Error propagation**: Which errors should halt the pipeline vs. be noted and continued?
- **Human escalation**: Which failures require human intervention?

**Key Insight**: Error handling is not an afterthought—it's a core design dimension. The system's reliability is defined by how it handles failures.

### Observability

**The Problem**: When multi-agent pipelines go wrong (or right), humans need to understand what happened.

**Design Considerations**:

- **Tracing**: Every stage invocation should be traceable with inputs, outputs, duration, and token usage
- **Logging**: Structured logs enable analysis and debugging
- **Metrics**: Aggregate measures (success rates, latencies, token costs) inform optimization
- **Auditability**: For compliance and accountability, full execution history must be reconstructible

**Key Insight**: Observability enables improvement. Without visibility into what's happening, optimization is guesswork.

### Token Economics

**The Problem**: Multi-agent systems use more tokens than single-agent systems—potentially 10-20x more. This must be justified by proportional value.

**Design Considerations**:

- **Stage-specific context**: Only load context needed for each stage
- **Model selection**: Use smaller/cheaper models for simpler stages
- **Caching**: Reuse computed results where possible
- **Budgeting**: Set token budgets per pipeline, stage, or time period
- **ROI analysis**: Track value delivered vs. tokens consumed

**Key Insight**: Token usage is a first-class concern, not an afterthought. The economic viability of multi-agent systems depends on delivering value that justifies their cost.

### Security and Isolation

**The Problem**: Agents with write capabilities can cause harm. Agents processing untrusted input may be manipulated.

**Design Considerations**:

- **Least privilege**: Agents should have minimum necessary permissions
- **Sandboxing**: Execution environments should be isolated
- **Input validation**: Filter malicious or manipulated inputs
- **Output validation**: Verify agent outputs before acting on them
- **Audit trails**: All actions should be logged for forensics

**Key Insight**: Security constraints must be architectural, not instructional. Telling an agent "don't do X" is not the same as making X impossible.

---

## Agent Definition Concerns

When defining an agent, consider:

### Identity
- What is this agent's role?
- What persona should shape its behavior?
- What expertise does it bring?

### Capabilities
- What tools can this agent use?
- What actions can it take?
- What resources can it access?

### Constraints
- What must this agent NOT do?
- What topics are out of scope?
- What permissions does it lack?

### Context Requirements
- What information does this agent need to function?
- What's the minimum viable context?
- What context would be nice-to-have vs. essential?

### Output Contract
- What format should outputs take?
- What fields are required vs. optional?
- How will outputs be validated?

### Termination Criteria
- What constitutes successful completion?
- What constitutes failure?
- What timeouts apply?
- How many retries before giving up?

---

## Pipeline Definition Concerns

When defining a pipeline, consider:

### Stage Design
- What are the natural phase boundaries?
- Which stages can run in parallel?
- Where are human checkpoints needed?

### Data Flow
- What does each stage need from previous stages?
- How should large outputs be summarized?
- What's the contract between stages?

### Execution Strategy
- Sequential, parallel, or conditional?
- What's the timeout for each stage?
- What's the total pipeline timeout?

### Error Strategy
- Which stages are retryable?
- What fallbacks exist?
- When should humans be escalated to?

### State Strategy
- Where is state persisted?
- How often is state checkpointed?
- How is state recovered on failure?

---

## Interaction with Other Foundations

The Multi-Agent Framework is the execution layer that enables other foundations:

| Foundation | How Multi-Agent Supports It |
|------------|----------------------------|
| **Documentation Intelligence** | Agents maintain indexes, detect drift, provide context |
| **Collaborative Planning** | Pipelines implement CPF phases; agents assist in each phase |
| **PR Reviewer System** | Specialized reviewer agents run in parallel pipelines |
| **Codebase Analysis Engine** | Analysis agents decompose codebase understanding |
| **Self-Reflection Framework** | Meta-agents analyze pipeline performance and propose improvements |
| **Security Architecture** | Enforced through agent permissions and sandbox design |

**Key Insight**: The Multi-Agent Framework doesn't do the work of other foundations—it provides the coordination infrastructure they need to function.

---

## Evaluation Criteria

An implementation of this spec should be evaluated on:

| Criterion | Question |
|-----------|----------|
| **Composability** | Can new agents and pipelines be defined easily? |
| **Observability** | Can humans understand what happened and why? |
| **Reliability** | Does the system handle failures gracefully? |
| **Efficiency** | Are tokens used proportional to value delivered? |
| **Security** | Are agents properly isolated and constrained? |
| **Extensibility** | Can new patterns and agents be added without rewriting? |
| **Testability** | Can pipelines and agents be tested in isolation? |

---

## Open Questions for Implementers

These questions don't have universal answers—they depend on context:

1. **Agent granularity**: How specialized should agents be? Many small agents or fewer larger ones?

2. **Context sharing**: How much context should agents share vs. maintain independently?

3. **Coordination overhead**: At what point does coordination cost exceed parallelism benefits?

4. **Human touchpoints**: Where in pipelines should humans be involved? Every stage? Only at boundaries?

5. **Recovery scope**: When resuming from failure, replay from the beginning or from the failed stage?

6. **Model mixing**: Should different stages use different models (e.g., expensive models for planning, cheaper for execution)?

7. **Learning integration**: How should pipeline performance inform future pipeline design?

---

## Getting Started

For implementers ready to build a multi-agent system:

1. **Start with one pipeline**: Pick a concrete workflow (e.g., PR review, feature implementation) and design a pipeline for it

2. **Define 2-3 agents**: Create minimal agent definitions with clear identities, capabilities, and constraints

3. **Build the orchestrator**: Implement the simplest orchestrator that can run your pipeline

4. **Add observability**: Before scaling, ensure you can see what's happening

5. **Iterate**: Learn from running the system, refine agent definitions and pipeline design

**Key Insight**: Don't try to build a complete multi-agent framework first. Build the minimum needed for one valuable workflow, then generalize.

---

## Summary

The Multi-Agent Framework provides the execution infrastructure for complex, coordinated LLM work. Its core concepts—agents, pipelines, and orchestration—enable sophisticated workflows while maintaining observability and reliability.

Success requires attention to:
- **Context engineering**: Right information to right agents
- **State management**: Resilience to interruption
- **Error handling**: Graceful degradation
- **Observability**: Understanding what happened
- **Token economics**: Value proportional to cost
- **Security**: Constraints enforced architecturally

This spec provides the conceptual foundation. Implementation requires adapting these concepts to specific organizational context, tooling, and requirements.

---

## Related Documents

| Document | Relationship |
|----------|--------------|
| [Foundational Technical Requirements](../Foundational-Technical-Requirements.md) | Parent document defining all seven foundations |
| [Human-Driven Development](../Human-Driven-LLM-Navigated-Software-Development.md) | The collaboration model agents operate within |
| [LLM-First Code Reviews](../LLM-Assisted-Code-Review.md) | A primary consumer of multi-agent pipelines |
| [Radical Self-Improvement](../Radical-Self-Improvement-for-LLMs.md) | How multi-agent systems improve over time |

---

Authored-by: jib
