# Collaborative Planning Framework (CPF) Specification

**Status:** First Draft
**Foundation:** #2 of the Seven Foundations
**Purpose:** Enable structured human-LLM collaboration that transforms vague intent into implementation-ready specifications

---

## Overview

The Collaborative Planning Framework (CPF) is a structured methodology for human-LLM collaboration during software planning and development. It addresses the fundamental challenge of translating human intent—often vague, contextual, and evolving—into precise, actionable specifications that LLM agents can execute autonomously.

**The core insight:** Humans and LLMs have complementary cognitive strengths. Optimal outcomes emerge when humans drive strategic direction while LLMs provide structural rigor and exhaustive analysis.

---

## The Problem CPF Solves

### Current State

| Scenario | Failure Mode |
|----------|--------------|
| Human provides vague intent | LLM makes wrong assumptions, produces unwanted output |
| Human provides detailed spec | Human cognitive burden is too high, defeats the purpose of LLM assistance |
| LLM asks questions reactively | Disorganized, incomplete requirement gathering |
| Human corrects mid-implementation | Wasted tokens, context fragmentation, accumulated confusion |

### Desired State

```
User: "I want to add OAuth2 to the API"
                ↓
    [Structured Planning Dialogue]
                ↓
Complete specification with:
- Validated requirements
- Architecture decisions with rationale
- Phased implementation plan
- Machine-readable task breakdown
                ↓
    [Autonomous Implementation]
                ↓
Working feature with comprehensive tests and documentation
```

---

## Core Concepts

### 1. AI Drives, Human Steers

The LLM actively drives the planning conversation—asking questions, presenting options, identifying gaps. The human provides strategic direction at key decision points.

**Why this matters:** Humans shouldn't have to know what questions to ask. The LLM's role is to systematically explore the problem space and surface what needs human judgment.

| Role | Responsibilities |
|------|-----------------|
| **LLM (Driver of Process)** | Ask clarifying questions, enumerate options, identify edge cases, synthesize into artifacts |
| **Human (Strategic Authority)** | Approve direction, make trade-off decisions, provide domain context, give final approval |

### 2. Progressive Disclosure

Requirements emerge through iterative refinement, not interrogation. The conversation starts broad and narrows based on human responses.

**Pattern:**
```
LLM: "You want to add authentication. Let me understand the scope..."
     [Initial framing questions]

Human: [Provides high-level direction]

LLM: "Based on that, let me explore specific areas..."
     [Targeted deep-dive questions]

Human: [Clarifies specific aspects]

LLM: "I now understand the requirements. Here's what I captured..."
     [Summary for validation]
```

**Anti-pattern:** Presenting 20 questions upfront, overwhelming the human with decision fatigue.

### 3. Human-in-the-Loop Checkpoints

Explicit approval points prevent the LLM from going too far down wrong paths. Checkpoints create natural pause points for human reflection and course correction.

**Checkpoint properties:**
- **Summarize:** What has been accomplished so far
- **Highlight:** Key decisions made and their rationale
- **Surface:** Open questions or concerns
- **Offer:** Clear options for proceeding

### 4. Documentation as Output

The planning process produces documentation as its primary output. These artifacts are both human-readable (for review and understanding) and machine-readable (for automated execution).

**Core principle from Documents-as-Code:** The specification IS the work product. Implementation follows from documentation, not the reverse.

---

## Phase Model

CPF operates through distinct phases. There are two complementary phase sequences:

### Strategic Planning Phases (What to Build)

Use when evaluating new initiatives, exploring possibilities, or making go/no-go decisions.

```
IDEATION → ASSESSMENT → REINFORCEMENT → PLANNING
```

| Phase | Purpose | Key Activities | Output |
|-------|---------|----------------|--------|
| **Ideation** | Generate possibilities | Brainstorm, explore adjacent ideas | Raw concepts |
| **Assessment** | Evaluate value and feasibility | Analyze trade-offs, identify risks | Go/no-go decision |
| **Reinforcement** | Sharpen the concept | Clarify requirements, resolve ambiguities | Crisp problem statement |
| **Planning** | Design implementation | Break down phases, identify dependencies | Implementation-ready spec |

### Implementation Workflow Phases (How to Build)

Use when executing approved work through structured planning.

```
ELICITATION → DESIGN → PLANNING → HANDOFF
```

| Phase | Purpose | Key Activities | Output |
|-------|---------|----------------|--------|
| **Elicitation** | Capture requirements | Clarifying questions, constraint discovery | Requirements document |
| **Design** | Create architecture | Explore options, analyze trade-offs | Design document |
| **Planning** | Break down work | Task decomposition, dependency mapping | Phased plan with tasks |
| **Handoff** | Package for execution | Consolidate artifacts, validate completeness | Implementation package |

**Choosing between sequences:** Strategic phases for "should we do this?" Implementation phases for "here's how we'll do this."

---

## Checkpoint Design

Checkpoints are the primary mechanism for human control. Each checkpoint should:

### Present Context

```
## Checkpoint: [Phase Name] Complete

### Summary
[2-3 sentences on what was accomplished]

### Key Decisions Made
1. [Decision] — [Rationale]
2. [Decision] — [Rationale]

### Artifacts Produced
- [Artifact 1]: [Brief description]
- [Artifact 2]: [Brief description]
```

### Offer Clear Actions

```
### Next Steps

[ ] **APPROVE** — Proceed to [next phase]
[ ] **APPROVE WITH NOTES** — Proceed with the following adjustments: ___
[ ] **REVISE** — Return to this phase with feedback
[ ] **RESTART** — Significant changes needed, begin phase again
```

### Handle Feedback Gracefully

When the human provides feedback:
1. **Parse** — Identify what aspect needs change
2. **Scope** — Determine how much needs to be redone
3. **Revise** — Make targeted changes, preserving valid work
4. **Re-present** — Show the updated work at the checkpoint

---

## Output Artifacts

### Requirements Document

Captures validated requirements, constraints, and success criteria.

**Key sections:**
- Executive summary
- Goals (primary and secondary)
- Functional requirements with acceptance criteria
- Non-functional requirements with measurable targets
- Constraints and out-of-scope items
- Success criteria

**Quality attributes:**
- Each requirement is testable
- Priorities are explicit (must-have vs. nice-to-have)
- Ambiguous language is eliminated ("may", "might", "could")

### Design Document

Captures architectural decisions and technical approach.

**Key sections:**
- System overview and boundaries
- Component architecture with responsibilities
- Interface specifications
- Technical decisions with rationale and alternatives considered
- Security considerations
- Test strategy

**Quality attributes:**
- Decisions are explicit, not implicit
- Trade-offs are acknowledged
- Rationale explains "why", not just "what"

### Implementation Plan

Captures how work will be executed.

**Key sections:**
- Phased breakdown with objectives per phase
- Task list with dependencies
- Acceptance criteria per task
- Risk register with mitigations

**Quality attributes:**
- Tasks are appropriately sized (not too large, not too granular)
- Dependencies are explicit
- Each task can be started without further clarification

### Machine-Readable Tasks (Optional)

For systems that execute plans automatically, provide structured task data:

```yaml
project: "Project Name"
phases:
  - id: phase-1
    name: "Foundation"
    tasks:
      - id: task-1.1
        name: "Implement configuration system"
        dependencies: []
        acceptance_criteria:
          - "Loads from file, environment, CLI"
          - "Supports validation"
        subtasks:
          - id: task-1.1.1
            name: "Define config schema"
          # ...
```

---

## Patterns for Effective Planning

### Pattern: Explore Before Committing

Before making a decision, explore the solution space systematically.

```
┌─────────────────────────────────────────┐
│           EXPLORATION TREE              │
│                                         │
│              [Problem]                  │
│             /    |    \                 │
│      [Option A][Option B][Option C]    │
│        /  \       |       \             │
│   [Trade-off analysis per option]      │
│                                         │
│   Present options with trade-offs       │
│   Human selects preferred path          │
└─────────────────────────────────────────┘
```

**Why:** Prevents premature commitment. Ensures human sees the full landscape before deciding.

### Pattern: Negative Requirements

Explicitly capture what the system should NOT do.

```markdown
## Out of Scope
- This feature will NOT support SSO/SAML (future work)
- This feature will NOT replace existing email/password auth
- This feature will NOT include admin configuration UI
```

**Why:** Prevents scope creep. Creates clear boundaries for implementation.

### Pattern: Assumption Surfacing

Make implicit assumptions explicit for validation.

```markdown
## Assumptions
- Users have GitHub accounts (verified: ~95% of target users)
- OAuth tokens can be stored in existing user session
- Rate limits from GitHub API are acceptable for our usage
```

**Why:** Prevents late-stage surprises. Gets human validation of beliefs that affect design.

### Pattern: Decision Records

Capture significant decisions with context for future reference.

```markdown
## Decision: OAuth Library Choice

**Context:** Need to implement OAuth2 flow for GitHub authentication.

**Options Considered:**
1. `oauthlib` — Full-featured, complex
2. `authlib` — Modern, well-maintained
3. Custom implementation — Maximum control, maintenance burden

**Decision:** Use `authlib`

**Rationale:** Best balance of features and maintainability. Active development, good documentation.
```

**Why:** Future maintainers understand why choices were made. Prevents "why did we do it this way?" questions.

### Pattern: Risk-First Planning

Identify risks early and build mitigation into the plan.

```markdown
## Risks

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| GitHub API changes | High | Low | Abstract OAuth provider, monitor deprecations |
| Token storage vulnerability | Critical | Medium | Encrypt at rest, audit access |
| User confusion with dual auth | Medium | Medium | Clear UX messaging, documentation |
```

**Why:** Proactive risk handling. Prevents "we didn't think of that" failures.

---

## Design Concerns

### Context Management

Planning sessions accumulate context. Without management, context windows overflow or important details get lost.

**Considerations:**
- How is planning state persisted across sessions?
- What happens when the context window fills?
- How are long sessions summarized without losing critical decisions?
- How does the system recover from interruptions?

### Scope Detection

Not all tasks need full CPF treatment. Simple changes don't warrant four-phase planning.

**Considerations:**
- How does the system detect task complexity?
- What abbreviated workflows exist for simple tasks?
- How does the user override automatic scope detection?
- What triggers escalation from simple to full planning?

### Multi-Stakeholder Planning

Some planning involves multiple humans with different perspectives.

**Considerations:**
- How are conflicting requirements surfaced and resolved?
- Can multiple humans participate in checkpoints?
- How is consensus captured?
- Who has final approval authority?

### Integration Points

CPF doesn't exist in isolation. It must integrate with surrounding systems.

**Considerations:**
- How do planning artifacts flow to task tracking systems?
- How does CPF consume existing documentation for context?
- How are planning sessions connected to implementation work?
- How do post-implementation learnings feed back to planning?

---

## Quality Dimensions

How do we know if CPF is working well?

### Planning Quality

| Dimension | Indicator |
|-----------|-----------|
| **Completeness** | Implementation rarely requires clarification after planning |
| **Accuracy** | Plans match what actually gets built |
| **Efficiency** | Planning overhead is proportionate to task complexity |

### Artifact Quality

| Dimension | Indicator |
|-----------|-----------|
| **Actionability** | Tasks can be started without further questions |
| **Clarity** | Documents contain no ambiguous language |
| **Consistency** | Artifacts don't contradict each other |

### Process Quality

| Dimension | Indicator |
|-----------|-----------|
| **Human satisfaction** | Planning feels productive, not bureaucratic |
| **Time to approval** | Checkpoints don't become bottlenecks |
| **Iteration rate** | Most work approved without revision |

---

## Anti-Patterns

### Anti-Pattern: Interrogation Mode

**Symptom:** LLM asks many questions upfront in rapid sequence.

**Problem:** Overwhelms human, causes decision fatigue, feels like filling out a form.

**Alternative:** Progressive disclosure—start broad, narrow based on responses.

### Anti-Pattern: Rubber-Stamp Checkpoints

**Symptom:** Checkpoints become formalities that humans approve without reading.

**Problem:** Defeats the purpose of human oversight. Errors propagate.

**Alternative:** Make checkpoints substantive. If humans are rubber-stamping, either the checkpoints are too granular or the process needs trust-building.

### Anti-Pattern: Over-Planning

**Symptom:** Simple tasks get full four-phase treatment.

**Problem:** Overhead exceeds value. Users avoid the process.

**Alternative:** Match planning depth to task complexity. Simple tasks need simple processes.

### Anti-Pattern: Planning Theater

**Symptom:** Documents are produced but not actually used during implementation.

**Problem:** Waste of effort. Planning and implementation diverge.

**Alternative:** Planning artifacts should be living documents referenced during implementation, not artifacts filed and forgotten.

### Anti-Pattern: Infinite Refinement

**Symptom:** Planning never completes because there's always more to specify.

**Problem:** Analysis paralysis. Implementation never starts.

**Alternative:** Set explicit completion criteria. "Good enough" is good enough. Perfection is the enemy of shipped software.

---

## Security Considerations

Planning involves discussing system architecture, security controls, and potentially sensitive requirements.

**Considerations:**
- What information should not be captured in planning artifacts?
- How are planning artifacts protected during creation and storage?
- What happens when planning involves security-sensitive systems?
- How is access to planning sessions controlled?

---

## Relationship to Other Foundations

| Foundation | Relationship to CPF |
|------------|-------------------|
| **Documentation Intelligence** | CPF produces documentation that Documentation Intelligence indexes and maintains |
| **Multi-Agent Framework** | CPF may be implemented as specialized agents (Elicitation Agent, Design Agent, etc.) |
| **PR Reviewer System** | PR Reviewer validates that implementation matches approved plan |
| **Codebase Analysis Engine** | Provides context to CPF about existing patterns and dependencies |
| **Continual Self-Reflection** | Analyzes CPF effectiveness, proposes process improvements |
| **Security Architecture** | Enforces security constraints on planning sessions and artifacts |

---

## Open Questions

These questions should be resolved during implementation:

1. **Checkpoint granularity:** What's the right number of checkpoints? Too many creates fatigue, too few risks going far down wrong paths.

2. **Context persistence:** How should planning state survive across sessions? File-based? Database? Integrated with task tracking?

3. **Scope thresholds:** What signals indicate a task needs full planning vs. abbreviated process?

4. **Artifact versioning:** How are planning artifacts versioned as they evolve? How is history preserved?

5. **Feedback loops:** How do implementation experiences improve future planning? What data should be captured?

6. **Tool integration:** How does CPF integrate with existing tools (GitHub issues, project boards, documentation systems)?

7. **Multi-LLM scenarios:** Can different phases use different models? How is context preserved across model boundaries?

---

## Success Criteria for Implementations

An implementation of CPF is successful when:

- [ ] Humans can express vague intent and receive clarifying questions
- [ ] Planning produces artifacts sufficient for autonomous implementation
- [ ] Checkpoints create genuine human decision points
- [ ] Planning depth scales appropriately with task complexity
- [ ] Artifacts are both human-readable and machine-consumable
- [ ] The process feels collaborative, not bureaucratic
- [ ] Implementation revisions due to planning gaps are rare

---

## Related Documents

| Document | Relationship |
|----------|--------------|
| [Foundational Technical Requirements](../docs/Foundational-Technical-Requirements.md) | Defines CPF as Foundation #2; establishes the seven foundations context |
| [Human-Driven, LLM-Navigated Development](../docs/Human-Driven-LLM-Navigated-Software-Development.md) | Philosophy underlying CPF; details the driver/navigator model |
| [Pragmatic Guide](../docs/Pragmatic-Guide-Software-Engineering-Post-LLM-World.md) | Strategic umbrella connecting CPF to the three pillars |

---

*This specification is intentionally abstract. Implementations should adapt these concepts to their specific context, tools, and organizational needs.*
