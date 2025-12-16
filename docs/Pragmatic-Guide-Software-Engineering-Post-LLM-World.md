# A Pragmatic Guide for Software Engineering in a Post-LLM World

**Status:** Draft
**Author:** James Wiesebron, james-in-a-box
**Created:** December 2025
**Purpose:** Strategic umbrella document connecting the three pillars of LLM-augmented software engineering

---

## Executive Summary

We are witnessing a fundamental shift in software engineering. Large Language Models have moved from experimental tools to practical collaborators capable of generating, reviewing, and maintaining code at unprecedented scale. This creates both opportunity and challenge: opportunity to amplify human capabilities, challenge to adapt our practices accordingly.

But how we adapt matters. The dominant narrative—that LLMs will simply make everything faster—is misleading. The idea that speed is an inherent benefit of software development with LLMs is a fallacy. With the models that exist currently, we don't have a path forward for automating the job of software engineers generally. We do, however, have a path towards radically improving the software development lifecycle, leading to higher quality code while reducing cognitive load on engineers.

But we can only navigate this path with **intentionality**, **rigor**, and **care**.

This document presents a **holistic philosophy** for software engineering in the post-LLM era, built on three mutually reinforcing pillars:

| Pillar | Core Question | Guiding Value | Document |
|--------|---------------|---------------|----------|
| **1. LLM-First Code Reviews** | Where should human attention focus? | Intentionality | [LLM-First Code Reviews](LLM-Assisted-Code-Review.md) |
| **2. Human-Led, LLM-Navigated Development** | How should humans and LLMs collaborate? | Rigor | [Human-Led, LLM-Navigated Software Development](Human-Led-LLM-Navigated-Software-Development.md) |
| **3. Radical Self-Improvement** | How do systems get better over time? | Care | [Radical Self-Improvement for LLMs](Radical-Self-Improvement-for-LLMs.md) |

**The core thesis:** Software engineering practices must evolve to leverage LLM strengths (exhaustive attention, pattern consistency, tireless execution) while preserving human strengths (strategic judgment, creative problem-solving, interpersonal collaboration). Each pillar addresses a different dimension of this evolution, and together they form a complete, pragmatic philosophy.

**Technical Foundation:** The concrete technical requirements to implement this vision are detailed in the companion document [Foundational Technical Requirements](Foundational-Technical-Requirements.md), which defines the six technical foundations: Multi-Agent Framework, Collaborative Planning Framework, PR Reviewer System, Codebase Analysis Engine, Index-Based Documentation Strategy, and Continual Self-Reflection Framework.

---

## Table of Contents

- [Why This Matters Now](#why-this-matters-now)
- [The Three Pillars](#the-three-pillars)
- [How They Work Together](#how-they-work-together)
- [Adopting the Philosophy](#adopting-the-philosophy)
- [Key Principles](#key-principles)
- [What This Is Not](#what-this-is-not)
- [Scope and Application](#scope-and-application)
- [Getting Started](#getting-started)
- [References](#references)

---

## Why This Matters Now

### The Speed Fallacy

There is a seductive narrative in the industry: LLMs will make software development faster. Ship more features. Move faster. 10x productivity.

This narrative doesn't capture the subtlety of the reality we're facing.

Speed without rigor creates technical debt. Speed without intentionality creates chaos. Speed without care creates systems that degrade over time. Speed as a metric is straightforward. Improving it is not.

### Three Shifts Require Three Responses

LLMs have fundamentally changed three aspects of software development, and each requires a thoughtful response:

**1. Rethinking Engineer Focus** — LLMs generate code faster than traditional review processes can handle. Rather than trying to keep pace, we need to rethink where human attention creates irreplaceable value. This requires **intentionality**.

**2. The Collaboration Question** — Human-computer interaction has moved from "human instructs, computer executes" to genuine collaboration. We need a new model for how humans and LLMs work together—one that maintains consistent structure and clear roles. This requires **rigor**.

**3. The Improvement Imperative** — Static systems cannot keep pace with rapidly evolving capabilities. LLM-augmented systems must continuously improve themselves, learning from every interaction. This requires **care**.

These three challenges are interconnected. You cannot address the focus question without rethinking collaboration. You cannot establish sustainable collaboration without building in self-improvement. And self-improvement depends on having clear quality signals from review.

### The Human Factor

Engineers are already overwhelmed. Adding "work with LLMs" to their responsibilities without changing how work happens leads to:

- Rubber-stamping (approving without genuine review)
- Cognitive overload (trying to manage LLM output without clear frameworks)
- Inconsistency (applying organizational standards differently)
- Stagnation (no mechanism for the system to improve)

We need practices that **reduce** human cognitive load while **increasing** code quality and enabling **continuous improvement**.

---

## The Three Pillars

Each pillar addresses a fundamental question. Together, they form a complete philosophy for LLM-augmented software engineering.

### Pillar 1: LLM-First Code Reviews

**Question:** Where should human attention focus?

**Answer:** Invert the review model—LLMs review first, humans approve last.

**Guiding Value — Intentionality:** Be deliberate about what deserves human cognitive investment. LLMs can assess patterns, catch bugs, and flag concerns—but humans must own the decisions that carry accountability.

**Key Insight:**
> Every piece of recurring human feedback represents a process failure. It should either be automated or questioned.

**Core Practices:**
- Automated tools (linters, type checkers, SAST) catch mechanical issues
- LLM reviewers catch semantic issues (patterns, naming, complexity)
- Human reviewers focus on strategy, architecture, and business logic
- Recurring feedback becomes new automated checks

**Connection to Other Pillars:** Review quality signals feed self-improvement (Pillar 3). The human/LLM division of labor in review reflects the broader collaboration model (Pillar 2).

**Read more:** [LLM-First Code Reviews](LLM-Assisted-Code-Review.md)

---

### Pillar 2: Human-Led, LLM-Navigated Development

**Question:** How should humans and LLMs collaborate on software development?

**Answer:** Humans set strategic direction (the "captain"), LLMs handle structural rigor (the "navigator").

**Guiding Value — Rigor:** Establish precise roles and maintain them consistently. The captain/navigator metaphor isn't just a suggestion—it's a discipline that prevents the chaos of undefined collaboration.

**Key Insight:**
> Humans and LLMs have complementary cognitive strengths. Optimal software development emerges when each focuses on what they do best.

**Division of Labor:**

| Human Responsibilities | LLM Responsibilities |
|----------------------|---------------------|
| Define vision and goals | Enumerate edge cases and considerations |
| Make strategic decisions | Ensure pattern consistency |
| Review and approve changes | Generate comprehensive tests |
| Handle novel situations | Keep documentation current |
| Collaborate with stakeholders | Track dependencies and implications |

**Core Philosophy:**
- Humans decide **what** and **why**
- LLMs handle **how** with precision
- Neither role is subordinate; both are essential
- Human judgment remains the final authority

**Connection to Other Pillars:** This model defines how humans and LLMs interact during review (Pillar 1) and improvement (Pillar 3). It's the philosophical foundation that unifies the framework.

**Read more:** [Human-Led, LLM-Navigated Software Development](Human-Led-LLM-Navigated-Software-Development.md)

---

### Pillar 3: Radical Self-Improvement for LLMs

**Question:** How do LLM systems improve over time without constant human intervention?

**Answer:** Build systems that proactively detect inefficiencies and propose improvements.

**Guiding Value — Care:** Treat the development process itself as something worth investing in and nurturing. Rather than accepting static tooling, invest in systems that actively reflect on their own behavior.

**Key Insight:**
> An LLM agent system should get measurably better at its job every week, automatically. Human oversight shifts from directing improvements to validating them.

**Four Capabilities:**
1. **Automated Maintenance** - Repository hygiene, documentation sync, dependency health
2. **Continuous Self-Reflection** - Pattern detection, inefficiency analysis, root cause identification
3. **PR Review Reviewer** - Meta-review that improves the review process itself
4. **Strategic Human Escalation** - Surfacing systemic issues for human decision

**The Shift:**
- **Old model:** Humans tell the system what to improve
- **New model:** System proposes improvements, humans validate

**Connection to Other Pillars:** Self-improvement learns from review patterns (Pillar 1) and operates within the human-driven collaboration model (Pillar 2).

**Read more:** [Radical Self-Improvement for LLMs](Radical-Self-Improvement-for-LLMs.md)

---

## How They Work Together

The three pillars are not independent options or sequential phases—they are mutually reinforcing aspects of a single philosophy:

```
                    ┌──────────────────────────────────┐
                    │          PHILOSOPHY              │
                    │   "Each pillar supports the      │
                    │    weight of the whole idea"     │
                    └──────────────────────────────────┘
                                   │
         ┌─────────────────────────┼───────────────────────┐
         │                         │                       │
         ▼                         ▼                       ▼
┌─────────────────┐     ┌─────────────────┐     ┌────────────────────┐
│   PILLAR 1      │◀─▶│   PILLAR 2      │◀─▶│   PILLAR 3         │
│   LLM-First     │     │   Human-Driven  │     │   Radical          │
│   Code Reviews  │     │   LLM-Navigated │     │   Self-Improvement │
│                 │     │                 │     │                    │
│  Intentionality │     │  Rigor          │     │  Care              │
└────────┬────────┘     └────────┬────────┘     └──────────┬─────────┘
         │                       │                         │
         └───────────────────────┼─────────────────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────────────┐
                    │       MUTUAL REINFORCEMENT       │
                    │   Reviews generate improvement   │
                    │   signals. Improvements enhance  │
                    │   reviews. Both follow the       │
                    │   collaboration model.           │
                    └──────────────────────────────────┘
```

**How they reinforce each other:**

- **Rigor + Improvement → Quality → Feasible Reviews:** This is the foundational causal chain. When humans provide clear direction (Pillar 2) and systems continuously improve (Pillar 3), the resulting code is higher quality from the start. Higher quality code means fewer issues to catch in review, making LLM-first review (Pillar 1) not just possible but practical. Without this upstream quality, review becomes a bottleneck no matter how good the review process.

- **Reviews inform Self-Improvement:** Recurring review feedback becomes automated checks. Meta-review analyzes patterns across PRs.

- **Self-Improvement enhances Reviews:** Better prompts, smarter checks, fewer false positives—all from learning what works.

- **The Collaboration Model governs both:** The human-as-captain principle applies whether you're reviewing code, approving improvements, or designing the system.

- **Each pillar is incomplete without the others:** Reviews without improvement stagnate. Improvement without a collaboration model has no guardrails. Collaboration without review has no feedback loop.

---

## Adopting the Philosophy

You don't adopt these pillars sequentially—you adopt them together as aspects of a unified approach. However, your emphasis and starting point will vary based on scale and context.

### Adoption Path: Individual Developer

**Starting point:** Begin with personal workflow improvements using existing tools.

**Phase 1: Internalize the Captain/Navigator Model**
- Practice human-driven, LLM-navigated work with tools like Claude, Copilot, or Cursor
- Focus on providing clear intent while letting the LLM handle implementation details
- Develop the discipline of reviewing and approving rather than dictating every detail

**Phase 2: Structure Your Reviews**
- Trust automated checks (linters, type checkers) to handle mechanical concerns
- Focus your review attention on architecture, business logic, and strategic decisions
- Build the habit of asking "should this be automated?" when you find yourself giving the same feedback

**Phase 3: Reflect and Improve**
- Track your own patterns: What tasks are difficult? What takes longer than expected?
- Surface observations to your team: documentation gaps, unclear requirements, tool limitations
- Propose improvements based on your experience

**Success indicators:**
- Reduced cognitive fatigue during development
- Faster task completion without quality loss
- More time spent on creative/strategic work vs. mechanical implementation

### Adoption Path: Team

**Starting point:** Establish shared practices and consistent LLM collaboration patterns.

**Phase 1: Align on Collaboration Model**
- Hold a team workshop on the captain/navigator philosophy
- Define team standards for how to work with LLMs (when to use them, how to structure tasks)
- Create shared examples of good human-driven, LLM-navigated work

**Phase 2: Build the Review Stack**
- Implement automated checks (pre-commit hooks, CI linters, type checkers)
- Integrate LLM reviewers into PR workflows
- Define where human review should focus (architecture, security, business logic)
- Establish team conventions for when recurring feedback should become automated

**Phase 3: Enable Continuous Improvement**
- Track recurring review feedback across PRs
- Hold retrospectives on what slows the team down
- Systematically convert recurring issues into automated checks
- Build team-specific documentation based on observed patterns

**Success indicators:**
- Consistent velocity without heroic effort
- Reduced review iteration counts
- Faster PR turnaround time
- More substantive review discussions (less nitpicking)

### Adoption Path: Organization

**Starting point:** Build infrastructure and culture that scales across teams.

**Phase 1: Provide Infrastructure**
- Provide shared tooling that enables LLM-assisted development across the organization
- Build centralized review automation (LLM reviewers, security scanning, pattern enforcement)
- Create shared documentation and codebase analysis systems
- Establish security and privacy guardrails for LLM use

**Important:** While organizations benefit from shared infrastructure, embrace flexibility in how developers work with LLMs:
- Some developers may prefer headless workflows managed through Slack and GitHub
- Others may prefer CLI tools like Claude Code
- Still others may prefer IDE integrations like Cursor or Copilot
- The shared infrastructure (review systems, documentation, security) should enable all these approaches rather than mandating a single tool

**Phase 2: Cultivate Culture**
- Train engineers on human-driven, LLM-navigated practices
- Establish organizational standards for code quality and review
- Create forums for sharing learnings and best practices
- Recognize and reward effective human-LLM collaboration

**Phase 3: Enable Self-Improvement at Scale**
- Build systems that surface patterns across teams (common documentation gaps, recurring issues)
- Create feedback loops from reviews to automated checks
- Implement organizational learning: successful practices in one team inform others
- Measure and track improvement: token efficiency, quality metrics, developer satisfaction

**Success indicators:**
- Consistent quality across teams
- Knowledge sharing and reduced duplication of effort
- Measurable improvement in development velocity and code quality
- High developer satisfaction with tools and processes

### For Teams Already Using LLM Tools

If you're already using LLMs but want to adopt this philosophy:

**Assess your current state:**
- **Pillar 1 (Reviews):** Are review practices keeping pace with LLM output speed? Is human attention focused on strategic concerns?
- **Pillar 2 (Collaboration):** Is there a consistent model, or does each person interact with LLMs differently?
- **Pillar 3 (Improvement):** Are you learning from experience, or making the same mistakes repeatedly?

**Identify your gaps:**
- Which pillar needs the most attention?
- Where are the bottlenecks in your current workflow?
- What friction points keep recurring?

**Start with the biggest opportunity:**
- If reviews are slow: Focus on building the review stack (Pillar 1)
- If collaboration is inconsistent: Align on the captain/navigator model (Pillar 2)
- If improvement is ad-hoc: Build self-reflection capabilities (Pillar 3)

### Success Indicators Across All Pillars

| Dimension | What to Measure |
|-----------|-----------------|
| **Quality** | Defect rates, review iteration count, time to production |
| **Collaboration** | Task specification clarity, first-attempt success rate, human cognitive load |
| **Improvement** | Automated check growth, proposal acceptance rate, decreasing intervention |

---

## Key Principles

These principles span all three pillars:

### 1. Humans Remain in Control

LLMs are powerful collaborators, not autonomous decision-makers. Humans:
- Set strategic direction
- Make judgment calls on trade-offs
- Approve all changes before deployment
- Can override any automated decision

### 2. Leverage Complementary Strengths

Don't use LLMs for what humans do better (judgment, creativity, relationships). Don't burden humans with what LLMs do better (exhaustive checking, pattern consistency, documentation sync).

### 3. Reduce Burden, Not Add It

Every automated system should make humans' lives easier:
- Less cognitive load, not more
- Faster feedback, not more noise
- Clearer decisions, not more complexity

### 4. Continuous Improvement Is Built In

Static systems cannot keep pace. The system should get better over time:
- Recurring issues become automated checks
- Human feedback improves LLM behavior
- LLM feedback improves human behavior
- Documentation evolves with the code

### 5. Ground Decisions in Evidence

All three pillars benefit from active incorporation of external research and evidence-based iteration:
- **External research** informs decisions across review practices, collaboration models, and self-improvement strategies
- **Evidence-based iteration** validates that changes actually improve outcomes rather than just feeling right
  - Formal A/B testing when you have the scale and volume
  - Observational learning and before/after measurement for most situations
  - The key is avoiding arbitrary changes—have *some* evidence that changes help
- Evidence-based practice prevents both humans and systems from cycling through modifications without knowing their impact

### 6. Transparency and Observability

All automated actions should be:
- Visible (humans can see what's happening)
- Explainable (humans can understand why)
- Reversible (humans can undo if needed)

---

## What This Is Not

### Not a Replacement for Human Judgment

LLMs cannot:
- Understand business context you haven't explained
- Make trade-offs only humans can evaluate
- Take accountability for decisions
- Replace the need for human oversight

### Not a Silver Bullet

This approach requires:
- Investment in tooling and infrastructure
- Team training and buy-in
- Ongoing calibration and adjustment
- Clear ownership and accountability

### Not "Set and Forget"

Even self-improving systems need:
- Human validation of improvements
- Periodic review of system behavior
- Adjustment as requirements change
- Intervention when things go wrong

---

## Scope and Application

### What This Framework Is

This is a **philosophy and set of principles** for software engineering in the LLM era—not a tool specification, not a product, not a rigid methodology. It guides how you think about and design your development systems, but it doesn't prescribe specific implementations.

**Key characteristics:**
- **Philosophy-first** — A way of thinking about human-LLM collaboration
- **Tool-agnostic** — No specific platforms or products required
- **Implementation-flexible** — Adapt to your context and constraints
- **Scale-independent** — Applies from individual to organization level

### Where This Applies

The framework operates at multiple scales:

| Scale | Application | Example |
|-------|-------------|---------|
| **Individual** | A developer using LLM tools to augment their work | Using Claude/Copilot with intentional human-driven practices |
| **Team** | A group adopting consistent LLM collaboration patterns | Shared code review workflows, planning frameworks |
| **Organization** | Company-wide infrastructure and cultural practices | Centralized LLM tools, standardized quality processes |

**Important:** You don't need organization-wide adoption to benefit. An individual developer can practice these principles with existing tools. A team can adopt shared practices without infrastructure changes. Organizations can build comprehensive systems when ready.

### What You Don't Need

This framework does **not** require:
- Specific LLM platforms (works with Claude, GPT, Copilot, etc.)
- Custom tooling (though it can inform tool-building)
- Specialized infrastructure (start with what you have)
- Organizational transformation (begin where you are)

### The Goal: Guiding System Design

The framework's purpose is to help you **design systems that work for you**:

- If you're building LLM tooling, these principles shape your design decisions
- If you're adopting existing tools, these principles guide how you use them
- If you're establishing team practices, these principles inform your processes
- If you're an individual contributor, these principles structure your workflow

**The framework succeeds when it helps you build better systems—whether those "systems" are personal habits, team workflows, or organizational infrastructure.**

---

## Getting Started

### Understand the Philosophy

1. **Read Pillar 2 first** — The collaboration model provides the conceptual foundation
2. **Then Pillar 1** — See how the collaboration model applies to review
3. **Then Pillar 3** — Understand how the system improves over time

### For Individual Contributors

1. **Internalize the captain/navigator model** — You set strategic direction; LLMs navigate details
2. **Trust appropriate automation** — If automated checks pass, focus on higher-level concerns
3. **Give feedback intentionally** — Your input shapes how the system improves
4. **Focus on what matters** — Strategy, architecture, business logic are your domain

### For Tech Leads

1. **Model the collaboration** — Demonstrate the captain/navigator dynamic with your team
2. **Establish consistent practices** — Everyone should collaborate with LLMs the same way
3. **Champion continuous improvement** — When you see patterns, help turn them into automated checks
4. **Track meaningful metrics** — Quality, collaboration effectiveness, improvement rate

### For Engineering Leadership

1. **Set the vision** — Commit to LLM-augmented development as a philosophy, not just tooling
2. **Invest holistically** — All three pillars need support, not just the technical ones
3. **Validate the model** — Ensure human oversight remains meaningful as automation grows
4. **Learn and adapt** — This is a new paradigm; expect to iterate on your approach

---

## References

### This Document Series

| Document | Guiding Value | Focus |
|----------|---------------|-------|
| [Foundational Technical Requirements](Foundational-Technical-Requirements.md) | — | Technical foundations for implementing the vision |
| [LLM-First Code Reviews](LLM-Assisted-Code-Review.md) | Intentionality | Practical guide to LLM-assisted review |
| [Human-Led, LLM-Navigated Development](Human-Led-LLM-Navigated-Software-Development.md) | Rigor | Collaboration philosophy |
| [Radical Self-Improvement for LLMs](Radical-Self-Improvement-for-LLMs.md) | Care | Autonomous improvement framework |

### External Resources

- [AWS AI-Driven Development Life Cycle](https://aws.amazon.com/blogs/devops/ai-driven-development-life-cycle/)
- [Atlassian HULA Framework](https://www.atlassian.com/blog/atlassian-engineering/hula-blog-autodev-paper-human-in-the-loop-software-development-agents)
- [Anthropic Claude Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices)

---

Authored-by: jib
