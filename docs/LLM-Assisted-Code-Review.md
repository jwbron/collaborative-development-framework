# LLM-First Code Reviews

**Status:** Draft
**Author:** James Wiesebron, james-in-a-box
**Created:** December 2025
**Purpose:** A philosophy and framework for code review where LLMs handle mechanical validation and humans focus on strategic judgment
**Guiding Value:** Intentionality

---

> **Part of:** [A Pragmatic Guide for Software Engineering in a Post-LLM World](Pragmatic-Guide-Software-Engineering-Post-LLM-World.md)

---

This document articulates a fundamental shift in how we think about code review: **LLM-first, human-last**. The core insight is that traditional code review has become misaligned with how software is actually developed in the LLM era.

**The problem:** LLMs can now generate code faster than humans can review it. Traditional code review—where humans catch everything—becomes a bottleneck, not a quality gate.

**The solution:** Invert the review model. Let LLMs handle mechanical validation while humans focus on strategic judgment. The result is faster feedback, higher consistency, and human attention directed where it matters most.

**The goal:** Free human reviewers from repetitive feedback so they can focus on critical paths—the high-stakes decisions where human judgment, organizational context, and accountability matter most.

**The guiding value—intentionality:** Be deliberate about what deserves human cognitive investment. Every moment spent reviewing formatting is a moment not spent on architecture, security, or mentorship. This document is about reclaiming human attention for the work that only humans can do.

---

## Table of Contents

- [The Core Philosophy](#the-core-philosophy)
- [The Transformation](#the-transformation)
- [Division of Review Responsibilities](#division-of-review-responsibilities)
- [The Review Stack](#the-review-stack)
- [Benefits for Reviewers](#benefits-for-reviewers)
- [Benefits for Teams](#benefits-for-teams)
- [The Self-Improving System](#the-self-improving-system)
- [Implementation Patterns](#implementation-patterns)
- [Anti-Patterns to Avoid](#anti-patterns-to-avoid)
- [Getting Started](#getting-started)
- [Related Documents](#related-documents)

---

## The Core Philosophy

### The Key Insight

> If you find yourself giving the same feedback twice, stop and automate it.

Every piece of recurring feedback represents a process failure. It should either become an automated check or be questioned as not worth giving. This simple principle drives everything that follows.

This is intentionality in action: treating human attention as the scarce resource it is, and investing it only where it creates irreplaceable value.

### Why Traditional Review Fails at Scale

Traditional code review places an enormous burden on human reviewers:

```
┌────────────────────────────────────────────────────────────────┐
│              TRADITIONAL REVIEW COGNITIVE LOAD                 │
│                                                                │
│  Strategic Concerns        │  Mechanical Concerns              │
│  ─────────────────         │  ──────────────────               │
│  • Does this solve the     │  • Is this formatted correctly?   │
│    right problem?          │  • Are type hints present?        │
│  • Is the architecture     │  • Is naming consistent?          │
│    sound?                  │  • Are there obvious bugs?        │
│  • Are there security      │  • Is documentation current?      │
│    implications?           │  • Are tests comprehensive?       │
│  • Is this the right       │  • Are patterns followed?         │
│    direction?              │  • Are imports organized?         │
│                            │                                   │
└────────────────────────────────────────────────────────────────┘
```

A significant portion of reviewer attention goes toward mechanical concerns—tasks that require tedious attention to detail rather than strategic insight.

### The LLM-First Model

Modern LLMs can assess far more than mechanical concerns. They can evaluate architecture decisions, identify security vulnerabilities, analyze business logic coherence, and suggest improvements across nearly every dimension of code quality. The question isn't "what can LLMs review?" but "where should humans focus?"

```
┌────────────────────────────────────────────────────────────────┐
│              LLM-FIRST REVIEW RESPONSIBILITY                   │
│                                                                │
│     HUMAN FOCUS            │      LLM REVIEWER                 │
│     (Critical Paths)       │      (Comprehensive Analysis)     │
│     ───────────────        │      ─────────────────────────    │
│  • Final go/no-go on       │  • Format and style validation    │
│    high-risk changes       │  • Type safety verification       │
│  • Organizational context  │  • Pattern consistency checks     │
│    LLMs can't access       │  • Anti-pattern detection         │
│  • Accountability for      │  • Architecture assessment        │
│    critical decisions      │  • Security vulnerability scan    │
│  • Novel strategic         │  • Business logic coherence       │
│    trade-offs              │  • Documentation completeness     │
│  • Relationship & trust    │  • Test coverage analysis         │
│    building with team      │  • Performance implications       │
│                            │  • Suggested improvements         │
│                            │                                   │
│  High-stakes, Accountable  │  Comprehensive, Tireless          │
└────────────────────────────────────────────────────────────────┘
```

Human attention is concentrated on critical paths—not because LLMs can't assess other areas, but because these paths require accountability, organizational context, and the kind of judgment that carries weight with stakeholders. This is the heart of intentionality: knowing where your attention belongs, and protecting that focus.

---

## The Transformation

### The Old Way

```
Developer writes code
        ↓
Developer opens PR
        ↓
Human reviewer checks EVERYTHING
  • Formatting ← tedious
  • Types ← automatable
  • Patterns ← automatable
  • Architecture ← requires judgment
  • Business logic ← requires judgment
        ↓
Developer fixes feedback
        ↓
Reviewer re-reviews
        ↓
Repeat until approved
        ↓
Eventually merges
```

### The New Way

```
Developer writes code
        ↓
Pre-commit hooks catch formatting, linting
        ↓
Developer opens PR
        ↓
LLM reviewer analyzes automatically
  • Patterns, naming, anti-patterns
  • Suggests or applies fixes
        ↓
Human reviewer focuses on:
  • Business requirements
  • Architecture decisions
  • Security implications
  • Strategic direction
        ↓
Approval
        ↓
Merge
```

### Why This Matters Now

Traditional review accumulated problems that LLMs can solve:

| Problem | Traditional Impact | LLM-First Solution |
|---------|-------------------|-------------------|
| Same feedback given repeatedly | Reviewer fatigue, inconsistency | Automated once, enforced forever |
| Different reviewers catch different things | Inconsistent quality | Uniform rule application |
| Feedback comes late | Wasted developer time | Issues caught at commit time |
| Review doesn't scale | Bottleneck when PRs increase | Parallel automated review |
| Nitpicks dominate discussion | Important feedback gets lost | Mechanical issues pre-resolved |

---

## Division of Review Responsibilities

### What LLMs Can Review (Nearly Everything)

Modern LLMs are capable reviewers across almost all dimensions of code quality:

| Domain | LLM Capability | Effectiveness |
|--------|----------------|---------------|
| **Formatting & Style** | Perfect pattern matching | Excellent |
| **Type Safety** | Exhaustive verification | Excellent |
| **Patterns & Conventions** | Tireless consistency | Excellent |
| **Common Bugs** | Known anti-pattern detection | Excellent |
| **Documentation** | Completeness checking | Excellent |
| **Test Coverage** | Systematic enumeration | Excellent |
| **Architecture** | Structural analysis, coupling detection | Strong |
| **Security** | Vulnerability patterns, input validation | Strong |
| **Performance** | Algorithmic complexity, resource usage | Strong |
| **Business Logic** | Coherence, edge cases, spec alignment | Moderate-Strong |
| **Design Trade-offs** | Pattern selection, scalability concerns | Moderate |

### Where Humans Should Focus (Critical Paths)

The question isn't whether LLMs *can* assess these areas—they can. The question is where human attention provides irreplaceable value:

| Critical Path | Focus | Why Human |
|---------------|-------|-----------|
| **Go/No-Go Decisions** | Should we proceed with this change? | Accountability—someone must own the decision |
| **Organizational Context** | How does this fit our roadmap, politics, constraints? | LLMs lack access to internal dynamics |
| **Novel Strategic Trade-offs** | Unprecedented situations requiring judgment | Creative problem-solving in new territory |
| **Stakeholder Trust** | Demonstrating human oversight to partners, regulators | Some contexts require human accountability |
| **Team Dynamics** | Mentorship, relationship building, morale | Human-to-human interaction matters |

### The Overlap Zone

Many review concerns fall into an overlap zone where both LLMs and humans can contribute:

```
┌─────────────────────────────────────────────────────────────────┐
│                    THE OVERLAP ZONE                             │
│                                                                 │
│  LLM Primary          │  Shared              │  Human Primary   │
│  ────────────         │  ──────              │  ─────────────   │
│  • Formatting         │  • Architecture      │  • Go/no-go      │
│  • Types              │  • Security          │  • Org context   │
│  • Patterns           │  • Business logic    │  • Novel strategy│
│  • Documentation      │  • Design trade-offs │  • Accountability│
│  • Common bugs        │  • Performance       │  • Team dynamics │
│                       │                      │                  │
│  LLM handles fully    │  LLM provides        │  Human provides  │
│                       │  analysis; human     │  irreplaceable   │
│                       │  decides priority    │  value           │
└─────────────────────────────────────────────────────────────────┘
```

In the overlap zone, LLMs provide comprehensive analysis while humans decide what matters most for this specific change.

### The Handoff Principle

When a human reviewer catches something mechanical, the response shouldn't be "please fix this"—it should be "how do we automate catching this?"

This embodies a core tenet of LLM-first review: **every piece of recurring feedback should either become an automated check or be questioned as not worth giving.** If it can't be automated and isn't valuable enough to keep giving manually, perhaps it shouldn't be feedback at all.

```
Human catches issue
        ↓
Ask: "Can this be automated?"
        ↓
    ┌───┴───┐
   Yes      No
    ↓        ↓
Automate  Accept as
it        human-review item
    ↓
Future PRs never
have this issue
```

---

## The Review Stack

Different tools handle different concerns, but the stack is more nuanced than a simple hierarchy. LLM reviewers provide comprehensive analysis across nearly all domains, while humans focus on critical paths.

```
┌────────────────────────────────────────────────────────────────┐
│                     THE REVIEW STACK                           │
│                                                                │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  HUMAN REVIEWER (Critical Paths)                         │  │
│  │  • Go/no-go decisions with accountability                │  │
│  │  • Organizational context & strategic fit                │  │
│  │  • Team dynamics, mentorship, trust                      │  │
│  │  Reviews LLM analysis, decides what matters              │  │
│  └──────────────────────────────────────────────────────────┘  │
│                           ▲                                    │
│                           │ LLM analysis informs human focus   │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  LLM REVIEWER (Comprehensive Analysis)                   │  │
│  │  • Architecture assessment, coupling analysis            │  │
│  │  • Security vulnerability detection                      │  │
│  │  • Business logic coherence, edge cases                  │  │
│  │  • Design trade-offs, performance implications           │  │
│  │  • Code patterns, naming, duplication                    │  │
│  └──────────────────────────────────────────────────────────┘  │
│                           ▲                                    │
│                           │ Semantic issues pre-analyzed       │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  STATIC ANALYSIS (SAST)                                  │  │
│  │  • Security basics (detect-secrets, semgrep)             │  │
│  │  • Vulnerability patterns                                │  │
│  └──────────────────────────────────────────────────────────┘  │
│                           ▲                                    │
│                           │ Security issues filtered          │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  TYPE CHECKERS                                           │  │
│  │  • Type safety (mypy, TypeScript)                        │  │
│  └──────────────────────────────────────────────────────────┘  │
│                           ▲                                    │
│                           │ Type errors filtered              │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  LINTERS & FORMATTERS (Base of Stack)                    │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                │
└────────────────────────────────────────────────────────────────┘
```

The LLM layer doesn't just filter—it provides comprehensive analysis that humans can use to prioritize their attention on what matters most for this specific change.

---

## Benefits for Reviewers

### Cognitive Relief

When LLMs handle mechanical validation:

- **Reduced mental fatigue** — No more scanning for typos, style violations, missing types
- **Fewer context switches** — Stay in strategic thinking mode
- **Less repetition** — Never give the same feedback twice
- **More sustainable pace** — Review energy isn't depleted by tedious checks

### Focus on Critical Paths

With LLMs providing comprehensive analysis, human reviewers can concentrate on:

- **Accountability** — Owning the go/no-go decision for high-stakes changes
- **Organizational context** — Applying knowledge LLMs don't have access to
- **Mentorship** — Building relationships and teaching judgment, not rules
- **Strategic prioritization** — Using LLM analysis to decide what matters most
- **Trust building** — Demonstrating human oversight when stakeholders require it

### Better Decision Quality

When reviewers aren't mentally exhausted:

- **Clearer thinking** — Full cognitive capacity for important decisions
- **Deeper review** — Time to actually understand the change
- **More thoughtful feedback** — Quality over quantity
- **Healthier skepticism** — Energy to question assumptions

---

## Benefits for Teams

### Faster Feedback Loops

| Metric | Traditional | LLM-First |
|--------|-------------|-----------|
| Time to first feedback | Hours-days | Minutes |
| Review iterations | 3-5 | 1-2 |
| Time to merge | Days | Hours |
| Reviewer availability | Scarce | Augmented |

### Consistent Quality

- **Uniform enforcement** — Same rules applied to every PR
- **No reviewer lottery** — Quality doesn't depend on who reviews
- **Institutional knowledge captured** — Patterns encoded in automation
- **Reduced bus factor** — Standards exist independent of individuals

### Healthier Collaboration

When reviews focus on substance:

- **Less friction** — Feedback is about design, not nitpicks
- **More learning** — Discussions are educational, not pedantic
- **Better relationships** — Reviews feel collaborative, not adversarial
- **Sustainable culture** — Review doesn't cause burnout

---

## The Self-Improving System

### The Feedback-to-Automation Loop

```
┌─────────────────────────────────────────────────────────────────┐
│                 SELF-IMPROVING REVIEW SYSTEM                    │
│                                                                 │
│     ┌─────────────────┐                                         │
│     │ Human gives     │                                         │
│     │ feedback on PR  │                                         │
│     └────────┬────────┘                                         │
│              ↓                                                  │
│     ┌─────────────────┐                                         │
│     │ System tracks:  │                                         │
│     │ "Feedback X     │                                         │
│     │  appeared 3+    │                                         │
│     │  times"         │                                         │
│     └────────┬────────┘                                         │
│              ↓                                                  │
│     ┌─────────────────┐                                         │
│     │ New automated   │                                         │
│     │ check proposed  │                                         │
│     └────────┬────────┘                                         │
│              ↓                                                  │
│     ┌─────────────────┐                                         │
│     │ Team reviews    │                                         │
│     │ and approves    │                                         │
│     └────────┬────────┘                                         │
│              ↓                                                  │
│     ┌─────────────────┐                                         │
│     │ Future PRs      │                                         │
│     │ never have      │                                         │
│     │ this issue      │                                         │
│     └─────────────────┘                                         │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Reviewers as Trainers

In this model, human reviewers become **trainers** of the automation, not gatekeepers:

- Every piece of feedback potentially improves the system
- Giving feedback once is an investment in never giving it again
- The system gets smarter over time
- Reviewer expertise is multiplied across all future PRs

This connects to [Radical Self-Improvement for LLMs](Radical-Self-Improvement-for-LLMs.md)—the review process isn't static, it evolves.

---

## Implementation Patterns

### Pattern 1: Pre-Commit Automation

Catch issues before code is even committed:

```yaml
# .pre-commit-config.yaml
repos:
  - repo: local
    hooks:
      - id: format
        name: Format code
        entry: ruff format
        language: system
      - id: lint
        name: Lint code
        entry: ruff check --fix
        language: system
      - id: typecheck
        name: Type check
        entry: mypy
        language: system
```

### Pattern 2: LLM Review Integration

Configure LLM reviewers to run on every PR:

```yaml
# LLM review triggers
on_pr_opened:
  - check_patterns
  - check_naming
  - check_complexity
  - suggest_improvements

on_feedback_pattern:
  threshold: 3
  action: propose_automation
```

### Pattern 3: Human Review Focus Areas

Guide human reviewers on what to focus on:

```markdown
## Human Review Checklist

- [ ] Does this PR solve the stated problem?
- [ ] Is the architecture appropriate for this change?
- [ ] Are there security implications?
- [ ] Does this align with our technical direction?
- [ ] Would I be comfortable deploying this?

Note: Style, formatting, and mechanical issues are handled
by automated tools. Focus on strategic concerns.
```

---

## Anti-Patterns to Avoid

### Anti-Pattern 1: Duplicate Feedback

**Problem:** Human comments on something a linter should catch

```
Human: "Please use const instead of let here"
```

**Why it fails:** Wastes human attention on automatable concerns.

**Solution:** Add a lint rule; never comment on this again.

### Anti-Pattern 2: Style Debates in PRs

**Problem:** Reviewers argue about formatting, naming conventions

```
Reviewer A: "I prefer camelCase for this"
Reviewer B: "snake_case is more readable"
```

**Why it fails:** Style debates should happen once, then be encoded.

**Solution:** Establish conventions in automation; PRs follow rules, not opinions.

### Anti-Pattern 3: Rubber-Stamp Reviews

**Problem:** Human approves without substantive review

```
Human: "LGTM" (after 30 seconds on 500-line change)
```

**Why it fails:** Defeats the purpose of human oversight.

**Solution:** If automated tools pass and you have no strategic concerns, explicitly note what you checked.

### Anti-Pattern 4: Ignoring LLM Analysis

**Problem:** Human reviewer doesn't read or use the LLM's comprehensive analysis

```
Human: "I'm worried about the architecture here."
(LLM already flagged coupling issues and suggested refactoring)
```

**Why it fails:** Duplicates work and misses the opportunity to build on LLM insights.

**Solution:** Review the LLM review first. Use it to prioritize your attention on what matters most.

---

## Getting Started

### For Reviewers

1. **Trust the LLM analysis** — LLMs can assess architecture, security, and business logic; use their analysis as input
2. **Focus on critical paths** — Go/no-go decisions, organizational context, accountability, team dynamics
3. **Review the LLM review** — Scan what the LLM flagged; decide what's worth human attention
4. **Think "automate or accept"** — Every manual comment should either become a rule or be acknowledged as uniquely human
5. **Own your decisions** — When you approve, you're providing human accountability for the change

### For Teams

1. **Configure LLMs for comprehensive review** — Architecture, security, business logic—not just formatting
2. **Define critical paths** — Explicitly list where human attention is irreplaceable
3. **Audit your feedback** — What comments are given repeatedly? Automate them via LLM prompts.
4. **Build the stack** — Linters → Type checkers → SAST → LLM reviewers → Human critical path review
5. **Track the loop** — Monitor what LLMs miss; refine their prompts

### The Goal

The goal is not to limit LLM review to mechanical concerns—LLMs can assess nearly everything. The goal is to concentrate human attention on critical paths where accountability, organizational context, and human judgment are irreplaceable.

When LLMs provide comprehensive analysis and humans focus on high-stakes decisions, everyone wins: reviewers are more engaged, developers get faster feedback, and critical paths get the human attention they deserve.

This is what intentionality looks like in practice: a deliberate choice to protect human cognitive capacity for the decisions that matter most.

---

## Related Documents

| Document | Description |
|----------|-------------|
| [A Pragmatic Guide for Software Engineering in a Post-LLM World](Pragmatic-Guide-Software-Engineering-Post-LLM-World.md) | Strategic umbrella connecting all three pillars |
| [Human-Driven, LLM-Navigated Development](Human-Driven-LLM-Navigated-Software-Development.md) | Philosophy for human-LLM collaboration |
| [Radical Self-Improvement for LLMs](Radical-Self-Improvement-for-LLMs.md) | Framework for autonomous LLM self-improvement |

---

**Last Updated:** 2025-12-05
**Next Review:** 2026-01-05 (Monthly)
