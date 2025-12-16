# Action Plan: Addressing Ian's Feedback

**Status:** Draft
**Created:** December 2025
**Context:** Feedback from Ian on the Collaborative Development Framework
**Owner:** James Wiesebron

---

## Background

Ian provided detailed feedback on the Collaborative Development Framework (CDF) after reading through the documentation. His feedback highlights areas where the framework could be more accessible, clearer, and better accommodate different working styles.

This document captures Ian's feedback, analyzes each point, and proposes concrete actions to improve the framework.

---

## Feedback Summary

### 1. Meta-Level: "Too Heady Too Quickly"

**Ian's feedback:**
> Overall, I found this got a little too heady too quickly. The "what this is" and "what this isn't" sections were great, but they didn't go far enough IMO. E.g., some questions I found myself asking after reading that first doc: Is this suggesting teams need specific tools? Or prescribing that engineers will have to change their day-to-day practices? What is the scale it's applied at? A team? An individual? A repo? Sometimes it felt like a philosophy; at other times, it felt like this is a design document for a system that's yet to be built.

**Context:** The CDF is intentionally a philosophy meant to guide readers in building systems that work for them. It serves dual audiences (humans and LLMs) with different reading modes.

**Analysis:** Ian's confusion is valid—the framework doesn't explicitly address scope, tooling requirements, or application scale early enough. Readers need clearer orientation before diving into the philosophical content.

---

### 2. Prescriptive Tone vs. Iterative Working Styles

**Ian's feedback:**
> Also, even though you caveated the document as non-authoritative, the overall pattern feels overly prescriptive. And in general it seems to suggest that a lot of thinking can be done in a waterfall approach that doesn't necessarily match my style of thinking. E.g., I don't know what's best until I do it. I don't know if it feels good to a human until I've toyed with the problem. Does this framework still support thinking in that way?

**Context:** The framework supports both heavy-planning and iterative-experimental working styles. The prescriptive tone is intentional for LLM consumption but may confuse human readers.

**Analysis:** The framework needs to explicitly validate iterative/experimental approaches. The Collaborative Planning Framework (CPF) phases could be presented as optional checkpoints rather than a mandatory sequence.

---

### 3. Driver/Navigator Metaphor Confusion

**Ian's feedback:**
> This is a personal nit, but the driver/navigator metaphor kinda felt backwards to me? I feel like a driver is the mechanical task while the navigator is the planner. But that might just be based on how my personal role in the car shakes out. I really like the in-practice section in here showing the back-and-forth of the LLM and the human. It brought some concreteness to what is otherwise pretty cerebral.

**Context:** The metaphor is meant to emphasize that navigation is meaningless without a destination—the driver decides *where* to go (destination, stops, route preferences), while the navigator tracks *how* to get there (current position, optimal path, hazards).

**Analysis:** The "destination" framing that makes the metaphor work is not prominent enough. The explanation needs strengthening, or an alternative metaphor should be considered.

---

### 4. Multi-Persona Implementing Agents

**Ian's idea:**
> I could see a system that supports multiple types of implementing agents. E.g., sometimes I want the clever pizzazz-focused FE engineer vs the by-the-book implementer vs the first-principles-foundational-rethinker. As the human driver, I could see directing which persona I want for the tasks at hand.

**Analysis:** This is an enhancement idea rather than a critique. It fits naturally with the Multi-Agent Framework foundation and represents style-based specialization beyond domain specialization. Worth capturing for future development.

---

### 5. Self-Reflection: Learning Prioritization and Experimentation

**Ian's feedback:**
> I recently saw a conference presentation where an engineer had built their claude code environment to do something like this (to seemingly great success). And one of the elements they included was the idea of "eureka" moments—those insights which are so important that they trump other concerns. Something like this (or some sort of weighting) feels important; there are so many different learnings a system can have and they will often conflict with each other. Similarly, we may want a means of scoping improvements. E.g., is this learning per task, per product, or global?
>
> I don't love the experimentation piece of this so much. It feels icky as a human, but also it feels difficult to do given the small sample size of an engineering team (or org), the relatively complexity of a software system, and the fact that there will be many learnings being applied concurrently.

**Context:** The goal of experimentation is to avoid making arbitrary decisions—ensuring changes actually improve outcomes.

**Analysis:**
- Learnings need classification by priority (critical insight vs. minor observation) and scope (task-specific vs. organization-wide)
- Formal A/B testing may be impractical; "evidence-based iteration" or "observational learning" might better communicate the intent while being more realistic
- The concern about small sample sizes and concurrent learnings is valid and should be acknowledged

---

### 6. Code Review: Dependency on Engineer Behavior

**Ian's feedback:**
> A lot of this section felt like it depended on engineers participating in the system just so. The engineer has a checklist that they must follow, the engineer must add lint rules instead of comments, the engineer must not do superficial checks... Practically, I don't think this can rely on specific engineer behaviors—especially since not all PRs (or even a majority) have strategic concerns that need meaningful secondary evaluation. IMO, we should instead seek to build a system that can meet engineers where they're at and not expect personal rigor. At the very least, the lint-rule-writing could probably be passed to the LLM; but I think there's more thinking to be done here about how to actually enforce good human review.
>
> I really love the bits about anchoring human review around accountability. That's a potent concept—I'm sure there's a way to leverage that.

**Context:** The code review pillar represents a cultural shift. Code built with LLMs with sufficient rigor should change how we approach reviews. Engineers who don't follow the process effectively have their code rewritten by LLMs anyway.

**Analysis:**
- The section should emphasize system capabilities rather than behavioral requirements
- Automation should handle the heavy lifting (LLM proposes lint rules, detects rubber-stamping)
- The accountability framing resonated and should be amplified
- A maturity model could show teams where to start vs. where they're heading

---

### 7. Framework Size and Approachability

**Ian's feedback:**
> And final piece of overall feedback: this framework is big—I love the pillars and how they reinforce each other, but then we go pretty deep into a lot of systems. If you're thinking about generating buy-in, I might consider how to make it more approachable or bite-size.

**Context:** Plans exist for piecemeal adoption—the core LLM collaboration philosophy can be pushed independently. This Khan-specific context will be included after forking the doc.

**Analysis:** The framework would benefit from explicit entry points and a "start here" guide showing incremental adoption paths.

---

## Proposed Actions

### High Priority (Address Core Accessibility)

| Action | Document | Description |
|--------|----------|-------------|
| **Add "Scope and Application" section** | Pragmatic Guide | Address Ian's core confusion: this is a philosophy (not a tool spec), applicable at individual/team/org level, no specific tools required |
| **Add adoption paths by scale** | Pragmatic Guide | Concrete examples of how individuals, teams, and orgs can apply the framework |
| **Add "Workflow Flexibility" section** | Human-Driven | Explicitly validate iterative working styles, show how quick-prototype-then-spec approaches fit |
| **Reframe behavioral expectations** | LLM-Assisted Code Review | Shift from "engineers must..." to "the system enables/encourages..." |

### Medium Priority (Clarify and Strengthen)

| Action | Document | Description |
|--------|----------|-------------|
| **Strengthen driver/navigator explanation** | Human-Driven | Lead with the "destination" framing; consider alternatives (pilot/co-pilot, captain/navigator) |
| **Add learning classification** | Continual Self-Reflection | Priority levels (critical/helpful/minor) and scope levels (task/product/org) |
| **Reframe experimentation** | Radical Self-Improvement, Continual Self-Reflection | Replace "A/B testing" language with "evidence-based iteration"; acknowledge sample size limitations |
| **Add automation for behavioral expectations** | LLM-Assisted Code Review | LLM proposes lint rules from feedback, detects rubber-stamping, handles heavy lifting |

### Enhancement Opportunities (Future Work)

| Action | Document | Description |
|--------|----------|-------------|
| **Add agent personas/styles** | Multi-Agent Framework | Capture Ian's multi-persona idea: style-based specialization (conservative vs. experimental, pizzazz vs. by-the-book) |
| **Add maturity model** | New section (Pragmatic Guide or standalone) | Show progression from basic LLM assistance to full self-improvement |
| **Create "Getting Started" quick guide** | New document | Bite-sized entry point for newcomers |

---

## Implementation Sequence

### Phase 1: Accessibility (Immediate)

1. **Pragmatic Guide: Add "Scope and Application" section** after "What This Is Not"
   - Explicitly state this is a philosophy, not a tool specification
   - Clarify it applies at any scale (individual → team → org)
   - Note that no specific tools are required
   - State the goal is to guide system design, not prescribe one

2. **Pragmatic Guide: Add concrete adoption paths**
   - Individual developer starting with LLM tools
   - Team adopting LLM workflows
   - Organization building LLM infrastructure

3. **Human-Driven: Add "Workflow Flexibility" section**
   - Show heavy-planner approach (full CPF phases)
   - Show iterative-experimenter approach (prototype → refine → spec)
   - Validate both as legitimate

### Phase 2: Clarification (Near-term)

4. **Human-Driven: Strengthen driver/navigator metaphor**
   - Lead the section with the "destination" framing
   - Possibly add alternative metaphors for readers who prefer them
   - Keep the concrete example (already praised by Ian)

5. **Continual Self-Reflection: Add learning classification**
   - Priority: Eureka / Important / Helpful / Minor
   - Scope: Task-specific / Product-specific / Organization-wide
   - Conflict resolution guidance

6. **Radical Self-Improvement + Continual Self-Reflection: Reframe experimentation**
   - Replace "A/B testing" with "evidence-based iteration"
   - Acknowledge small sample sizes make formal experiments impractical
   - Focus on observational learning and qualitative feedback

7. **LLM-Assisted Code Review: Reframe as system capabilities**
   - Shift language from requirements to enablement
   - Add explicit automation: LLM proposes lint rules, detects patterns
   - Emphasize accountability (Ian's favorite part)

### Phase 3: Enhancement (Future)

8. **Multi-Agent Framework: Agent personas**
9. **Maturity model document**
10. **Quick-start guide**

---

## Open Questions

1. **Driver/navigator metaphor:** Should we keep it with better explanation, or switch to an alternative (pilot/co-pilot, captain/navigator)?

2. **Experimentation language:** Is "evidence-based iteration" the right reframing, or is there better terminology?

3. **Maturity model:** Should this be a standalone document or integrated into existing docs?

4. **Khan-specific fork:** When should this happen relative to these changes?

---

## Success Criteria

- A new reader can understand scope and application within the first few minutes
- Iterative/experimental working styles feel explicitly supported
- Behavioral expectations read as system capabilities, not requirements
- The framework feels approachable for piecemeal adoption

---

## Related Documents

| Document | Relationship |
|----------|--------------|
| [Pragmatic Guide](../Pragmatic-Guide-Software-Engineering-Post-LLM-World.md) | Primary target for accessibility improvements |
| [Human-Driven Development](../Human-Driven-LLM-Navigated-Software-Development.md) | Driver/navigator and workflow flexibility |
| [LLM-Assisted Code Review](../LLM-Assisted-Code-Review.md) | Behavioral expectations reframing |
| [Radical Self-Improvement](../Radical-Self-Improvement-for-LLMs.md) | Experimentation reframing |
| [Continual Self-Reflection](../foundations/Continual-Self-Reflection.md) | Learning classification |
| [Multi-Agent Framework](../foundations/Multi-Agent-Framework.md) | Agent personas (future) |

---

Authored-by: jib
