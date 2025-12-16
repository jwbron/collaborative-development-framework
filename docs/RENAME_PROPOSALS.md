# Proposals for Renaming "Human-Driven, LLM-Navigated Software Development"

## Context

PR #23 updated the framework's core metaphor from **driver/navigator** to **captain/navigator** to better convey:
- The captain sets the destination and makes command decisions
- The navigator provides critical information and charts the course
- Both roles are essential and complementary

However, the document `Human-Driven-LLM-Navigated-Software-Development.md` still uses "driven" in its title, which doesn't align with the new "captain" metaphor. "Human-Captained, LLM-Navigated" sounds awkward and less natural.

## Naming Criteria

A good replacement should:
1. **Align with captain/navigator metaphor** - Use nautical/journey language where natural
2. **Sound professional and clear** - Not forced or cutesy
3. **Convey the core concept** - Humans set strategy, LLMs handle execution/rigor
4. **Work as both title and filename** - Natural in both contexts
5. **Be memorable and distinctive** - Easy to reference in conversation

## Proposals

### Option 1: "Human-Led, LLM-Navigated Software Development" ⭐ RECOMMENDED

**Rationale:**
- "Led" naturally pairs with leadership/direction (aligns with captain setting destination)
- Minimal change from current name (just "driven" → "led")
- Already has precedent in industry ("human-led", "AI-led", etc.)
- Sounds natural: "We practice human-led, LLM-navigated development"
- "Led" implies strategic direction without requiring direct "captain" terminology

**Filename:** `Human-Led-LLM-Navigated-Software-Development.md`

**Document title:** "Human-Led, LLM-Navigated Software Development"

**Tagline:** "Where humans lead the strategy and LLMs navigate the execution"

---

### Option 2: "Human-Directed, LLM-Navigated Software Development"

**Rationale:**
- "Directed" emphasizes the human role in providing direction/guidance
- Aligns with captain role of setting course and making decisions
- Professional tone, commonly used in technical contexts
- Natural pairing: direction + navigation

**Filename:** `Human-Directed-LLM-Navigated-Software-Development.md`

**Document title:** "Human-Directed, LLM-Navigated Software Development"

**Tagline:** "Where humans direct the vision and LLMs navigate the implementation"

---

### Option 3: "Human-Guided, LLM-Navigated Software Development"

**Rationale:**
- "Guided" conveys the human role in providing guidance and oversight
- Softer tone than "directed" but still clear about human authority
- Works well with navigation metaphor (guide + navigate)
- Emphasizes collaborative nature

**Filename:** `Human-Guided-LLM-Navigated-Software-Development.md`

**Document title:** "Human-Guided, LLM-Navigated Software Development"

**Tagline:** "Where humans guide the strategy and LLMs navigate the execution"

---

### Option 4: "Strategic Humans, Systematic LLMs"

**Rationale:**
- Completely different approach: focuses on the cognitive division of labor
- "Strategic" captures human creativity, judgment, decision-making
- "Systematic" captures LLM thoroughness, consistency, precision
- More abstract, less tied to any specific metaphor (allows flexibility)
- Concise and memorable

**Filename:** `Strategic-Humans-Systematic-LLMs.md`

**Document title:** "Strategic Humans, Systematic LLMs"

**Tagline:** "A paradigm where humans focus on strategic thinking while LLMs handle systematic execution"

**Potential issue:** Doesn't explicitly reference the captain/navigator metaphor that is central to the framework

---

### Option 5: "Human Strategy, LLM Navigation"

**Rationale:**
- Very concise, parallel structure
- Clearly delineates the roles: strategy vs navigation
- Drops "driven" entirely, focuses on the work itself
- Natural and professional
- Aligns perfectly with captain (strategy) and navigator (navigation)

**Filename:** `Human-Strategy-LLM-Navigation.md`

**Document title:** "Human Strategy, LLM Navigation"

**Tagline:** "A framework where humans own strategy and LLMs own navigation"

---

### Option 6: "Captain and Navigator: Human-LLM Software Development"

**Rationale:**
- Explicitly uses the captain/navigator terminology
- Makes the metaphor front and center
- Clear and direct
- Could make the document's scope immediately obvious

**Filename:** `Captain-Navigator-Human-LLM-Software-Development.md`

**Document title:** "Captain and Navigator: Human-LLM Software Development"

**Tagline:** "Where humans captain the journey and LLMs navigate the route"

**Potential issue:** Might be too literal/explicit compared to the more abstract current approach

---

## Comparison Matrix

| Option | Aligns w/ Metaphor | Professional | Clear Concept | Natural Sound | Memorable |
|--------|-------------------|--------------|---------------|---------------|-----------|
| **Human-Led** ⭐ | ✓✓ | ✓✓✓ | ✓✓✓ | ✓✓✓ | ✓✓ |
| Human-Directed | ✓✓ | ✓✓✓ | ✓✓✓ | ✓✓ | ✓✓ |
| Human-Guided | ✓✓ | ✓✓ | ✓✓ | ✓✓✓ | ✓ |
| Strategic/Systematic | ✓ | ✓✓✓ | ✓✓✓ | ✓✓ | ✓✓✓ |
| Strategy/Navigation | ✓✓✓ | ✓✓ | ✓✓✓ | ✓✓ | ✓✓ |
| Captain/Navigator | ✓✓✓ | ✓✓ | ✓✓✓ | ✓✓ | ✓✓✓ |

Legend: ✓ = Good, ✓✓ = Very Good, ✓✓✓ = Excellent

---

## Recommendation

**Primary recommendation: Option 1 - "Human-Led, LLM-Navigated Software Development"**

Reasoning:
- Minimal disruption (single word change: "driven" → "led")
- "Led" naturally implies leadership/direction, aligning with captain role
- Sounds professional and natural
- Easy migration path for existing references
- Industry-standard terminology ("human-led" is widely used)

**Secondary recommendation: Option 5 - "Human Strategy, LLM Navigation"**

If you want something more distinctive and concise, this option directly names the roles rather than describing the relationship. It's bold and clear, though it represents a bigger change from the current naming.

---

## Implementation Notes

Whichever option is chosen, the following updates would be needed:

1. **Rename the file itself**
   - `docs/Human-Driven-LLM-Navigated-Software-Development.md` → new name

2. **Update document title** (line 1)

3. **Update references in other documents:**
   - `Pragmatic-Guide-Software-Engineering-Post-LLM-World.md` (references to Pillar 2)
   - `LLM-Assisted-Code-Review.md` (related documents section)
   - `Radical-Self-Improvement-for-LLMs.md` (related documents section)
   - `README.md` (if it references the document)
   - `docs/index.md` (if present)

4. **Update the tagline throughout the document** where it says "human-driven, LLM-navigated"
   - Line 15: "This document articulates a new paradigm for software development: **human-driven, LLM-navigated**."
   - And other occurrences

5. **Preserve git history** - Use `git mv` to rename the file so history is maintained

---

## Questions for Review

1. Do any of these options resonate with you?
2. Is there a different direction you'd like to explore?
3. Would you prefer to keep "navigated" in the name, or would you be open to something like "Human Strategy, LLM Execution"?
4. How important is it that the document name directly references the captain/navigator metaphor vs. just aligning with its spirit?

---

**Created:** 2025-12-16
**Context:** Response to feedback that "human-captained, LLM-navigated" sounds awkward after PR #23's driver→captain terminology change
