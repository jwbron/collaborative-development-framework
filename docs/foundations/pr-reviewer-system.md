# PR Reviewer System Specification

**Status:** First Draft
**Author:** James Wiesebron, james-in-a-box
**Created:** December 2025
**Foundation:** 4 of 7 (see [Foundational Technical Requirements](../docs/Foundational-Technical-Requirements.md))
**Purpose:** Technical specification for implementing automated, specialized code review

---

> **For Implementers:** This specification describes *what* the PR Reviewer System should accomplish and *why*, along with key patterns and design concepts. It intentionally leaves *how* open for creative interpretation. Use this as a north star, not a blueprint.

---

## Executive Summary

The PR Reviewer System is the quality gate that enables "LLM-first, human-last" code review. It automates comprehensive code analysis across multiple specialized domains, freeing human reviewers to focus on critical paths where their judgment is irreplaceable.

**Core Thesis:** LLMs can assess nearly every dimension of code quality. The goal isn't to limit what LLMs review—it's to ensure human attention concentrates on accountability, organizational context, and high-stakes decisions.

---

## Table of Contents

- [Design Philosophy](#design-philosophy)
- [System Boundaries](#system-boundaries)
- [Specialized Reviewer Domains](#specialized-reviewer-domains)
- [Key Patterns](#key-patterns)
- [Information Flow](#information-flow)
- [Feedback Mechanisms](#feedback-mechanisms)
- [Integration Points](#integration-points)
- [Security Considerations](#security-considerations)
- [Quality Attributes](#quality-attributes)
- [Open Design Questions](#open-design-questions)
- [Anti-Patterns to Avoid](#anti-patterns-to-avoid)
- [Success Criteria](#success-criteria)

---

## Design Philosophy

### The Inversion Principle

Traditional code review treats humans as the comprehensive filter and automation as a convenience. This system inverts that model:

```
Traditional:   Human reviews → catches everything → approves
LLM-First:     LLM analyzes comprehensively → Human reviews critical paths → approves
```

The LLM provides exhaustive analysis. The human provides judgment and accountability.

### Comprehensive Analysis, Focused Attention

The system should analyze everything it can—architecture, security, business logic, patterns, performance, documentation. But its purpose is not to replace human reviewers; it's to **inform their focus**.

A well-designed PR Reviewer System answers: "What should a human reviewer pay attention to in this PR?"

### Digestible Reviews

Reviews are only valuable if they're read. A review that flags 200 issues is worse than one that highlights 5 critical concerns. The system must:

- **Prioritize ruthlessly** — Critical issues first, nitpicks last (or not at all)
- **Synthesize, don't enumerate** — Patterns over instances
- **Respect reader attention** — Brevity is a feature

### The Handoff Principle

When a human gives feedback on something the system should have caught, that feedback becomes input for system improvement. Every piece of recurring human feedback represents a system failure.

---

## System Boundaries

### What the System Reviews

The PR Reviewer System has access to:

| Source | Purpose |
|--------|---------|
| **PR Diff** | The actual changes being proposed |
| **PR Metadata** | Title, description, labels, commits |
| **Repository Context** | Related files, project structure, patterns |
| **Project Documentation** | README, style guides, contribution guides |
| **CI/CD Results** | Test outcomes, linter output, build status |
| **Review History** | Previous comments and discussions on this PR |

### What the System Does NOT Access

| Source | Why Not |
|--------|---------|
| **External Documentation** | Reviews should be reproducible from PR context |
| **Author Profiles** | Reviews should be code-focused, not person-focused |
| **Unrelated PRs** | Scope creep; reviews should be bounded |
| **Production Systems** | Security boundary; reviews don't need runtime data |

### The Reproducibility Principle

If two implementations of this spec review the same PR with the same context, they should produce substantially similar findings. External dependencies that change independently of the PR make reviews non-reproducible and harder to audit.

---

## Specialized Reviewer Domains

The system organizes review concerns into specialized domains. Each domain has distinct expertise, priorities, and failure modes.

### Domain: Security

**Focus:** Protecting users, systems, and data from vulnerabilities.

| Concern | Examples |
|---------|----------|
| **Secrets & Credentials** | Hardcoded API keys, leaked passwords, exposed tokens |
| **Injection Vulnerabilities** | SQL injection, command injection, XSS |
| **Authentication & Authorization** | Broken access control, insecure defaults |
| **Data Exposure** | PII leakage, logging sensitive data |
| **Dependency Vulnerabilities** | Known CVEs, outdated packages |

**Characteristic:** Security findings often warrant blocking merges. False negatives (missing vulnerabilities) are more costly than false positives.

### Domain: Architecture

**Focus:** Long-term maintainability, coupling, and structural integrity.

| Concern | Examples |
|---------|----------|
| **Coupling** | Inappropriate dependencies, circular imports |
| **Cohesion** | Functions/classes with mixed responsibilities |
| **Patterns** | Violations of established project patterns |
| **Complexity** | Deep nesting, overly complex logic |
| **Technical Debt** | Shortcuts that will cost later |

**Characteristic:** Architecture concerns are often subjective. The system should explain *why* something is concerning, not just flag it.

### Domain: Correctness

**Focus:** Does the code do what it claims to do?

| Concern | Examples |
|---------|----------|
| **Logic Errors** | Off-by-one, null handling, boundary conditions |
| **Type Safety** | Type mismatches, unsafe casts |
| **Error Handling** | Swallowed exceptions, missing error cases |
| **Race Conditions** | Concurrent access, timing issues |
| **Edge Cases** | Unusual inputs, boundary values |

**Characteristic:** Correctness findings are usually objective and verifiable. High confidence is possible.

### Domain: Testing

**Focus:** Is the change adequately validated?

| Concern | Examples |
|---------|----------|
| **Coverage Gaps** | New code paths without tests |
| **Test Quality** | Tests that don't actually test anything |
| **Edge Case Coverage** | Missing boundary tests |
| **Test Maintainability** | Brittle, hard-to-understand tests |
| **Integration Testing** | Component interaction validation |

**Characteristic:** Testing concerns are often suggestions rather than blockers. Context matters—not every change needs exhaustive tests.

### Domain: Documentation

**Focus:** Can future developers (and LLMs) understand this code?

| Concern | Examples |
|---------|----------|
| **Missing Documentation** | Public APIs without docs |
| **Stale Documentation** | Docs that don't match the code |
| **Unclear Intent** | Code that requires explanation but has none |
| **Onboarding Barriers** | Changes that make the codebase harder to enter |

**Characteristic:** Documentation concerns are rarely blocking. They're investments in future maintainability.

### Domain: Style & Conventions

**Focus:** Consistency with project standards.

| Concern | Examples |
|---------|----------|
| **Naming** | Inconsistent or unclear names |
| **Formatting** | Style violations not caught by linters |
| **Idioms** | Non-idiomatic code for the language/framework |
| **Organization** | File structure, import organization |

**Characteristic:** Style concerns should be largely automated away. If they reach human review, something upstream failed.

### Domain: Performance

**Focus:** Efficiency at runtime and development time.

| Concern | Examples |
|---------|----------|
| **Algorithmic Complexity** | O(n²) when O(n) is possible |
| **Resource Usage** | Memory leaks, connection exhaustion |
| **Latency Impact** | Changes that slow critical paths |
| **Scalability** | Code that won't handle growth |

**Characteristic:** Performance concerns require context. What's slow for a hot path is fine for a cold one.

---

## Key Patterns

### Pattern: Severity Classification

Every finding should have a severity that communicates expected action:

| Severity | Meaning | Expected Response |
|----------|---------|-------------------|
| **Blocking** | Must be resolved before merge | Author fixes; reviewer verifies |
| **Warning** | Should be addressed or explicitly accepted | Author fixes or explains |
| **Suggestion** | Improvement opportunity | Author considers |
| **Note** | Informational; no action expected | Author acknowledges |

### Pattern: Evidence-Based Findings

Each finding should include:

1. **Location** — Where in the code
2. **What** — Description of the issue
3. **Why** — Why this matters
4. **How** — Suggested resolution (when possible)
5. **Confidence** — How certain the system is

```
Example:

[BLOCKING] Security: Potential SQL injection (api/users.py:42)

What: User input is concatenated directly into SQL query without sanitization.

Why: An attacker could manipulate the query to access or modify data they
     shouldn't have access to.

Suggested Fix:
  Use parameterized queries:
  cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))

Confidence: High (direct string interpolation in SQL context)
```

### Pattern: Synthesis Over Enumeration

When the same issue appears multiple times, synthesize into a pattern finding:

```
Instead of:
  - api/users.py:42: Variable name 'x' is unclear
  - api/users.py:55: Variable name 'y' is unclear
  - api/users.py:63: Variable name 'z' is unclear

Prefer:
  [SUGGESTION] Naming: Multiple unclear variable names in api/users.py

  Found 3 instances of single-letter variable names (lines 42, 55, 63).
  Consider using descriptive names that convey purpose.
```

### Pattern: Proportional Depth

Review depth should scale with PR complexity:

| PR Size | Review Focus |
|---------|--------------|
| **Small** (<50 lines) | Correctness, security, quick pattern check |
| **Medium** (50-300 lines) | Full domain analysis |
| **Large** (300+ lines) | Flag size concern; focus on critical paths |
| **Huge** (1000+ lines) | Strong recommendation to split; limited analysis |

Large PRs get less thorough review because they're harder to review well. This is a feature, not a bug—it creates pressure to keep PRs small.

### Pattern: Incremental Review

When a PR is updated after review:

1. **Re-analyze changed portions** — New commits may introduce new issues
2. **Verify fix resolution** — Confirm previously flagged issues are addressed
3. **Avoid redundant feedback** — Don't re-flag issues already acknowledged

### Pattern: Context Awareness

The same code can be acceptable or concerning depending on context:

```
# Acceptable in a quick script
def get_data():
    return requests.get(URL).json()

# Concerning in production API code
def get_data():
    return requests.get(URL).json()  # No error handling, no timeout, no retry
```

The system should infer context from project structure, file location, and surrounding code.

---

## Information Flow

### Input Processing

```
PR Event (opened, updated, review requested)
           │
           ▼
    ┌──────────────────┐
    │ Context Gatherer │
    │                  │
    │ • Fetch PR diff  │
    │ • Load metadata  │
    │ • Gather related │
    │   repository     │
    │   context        │
    └────────┬─────────┘
             │
             ▼
    ┌──────────────────┐
    │ Review Context   │
    │ (Structured      │
    │  representation) │
    └────────┬─────────┘
             │
             ▼
    Domain Analyzers
```

### Domain Analysis

Each domain analyzer receives the review context and produces domain-specific findings. Analyzers can run in parallel.

```
    Review Context
           │
     ┌─────┼─────┬──────┬──────┬──────┬──────┐
     ▼     ▼     ▼      ▼      ▼      ▼      ▼
  Security  Arch  Correct Testing  Docs  Style  Perf
     │      │       │       │       │      │      │
     └──────┴───────┴───────┴───────┴──────┴──────┘
                          │
                          ▼
                  Domain Findings
```

### Synthesis

Findings from all domains are combined, deduplicated, prioritized, and formatted:

```
    Domain Findings
           │
           ▼
    ┌──────────────────┐
    │ Synthesizer      │
    │                  │
    │ • Deduplicate    │
    │ • Prioritize     │
    │ • Group patterns │
    │ • Apply limits   │
    └────────┬─────────┘
             │
             ▼
    ┌──────────────────┐
    │ Formatter        │
    │                  │
    │ • Structure for  │
    │   platform       │
    │ • Apply severity │
    │   styling        │
    └────────┬─────────┘
             │
             ▼
      Review Output
```

### Output Delivery

Reviews can be delivered through multiple channels:

| Channel | Use Case |
|---------|----------|
| **PR Comments** | Primary delivery; inline and summary |
| **Status Checks** | CI integration; blocking/passing |
| **Notifications** | Alert authors/reviewers of critical findings |
| **Dashboards** | Aggregate visibility across repositories |

---

## Feedback Mechanisms

### The Self-Improvement Loop

The system should learn from human reviewer behavior:

```
┌─────────────────────────────────────────────────────────────┐
│               FEEDBACK-TO-IMPROVEMENT LOOP                  │
│                                                             │
│  Human gives feedback on PR                                 │
│            │                                                │
│            ▼                                                │
│  System records: What was caught? What was missed?          │
│            │                                                │
│            ▼                                                │
│  Analysis: Is this a pattern? Should we automate it?        │
│            │                                                │
│            ▼                                                │
│  ┌─────────┴─────────┐                                      │
│  │ Pattern detected? │                                      │
│  └─────────┬─────────┘                                      │
│       Yes  │   No                                           │
│            ▼                                                │
│  Propose new check                                          │
│            │                                                │
│            ▼                                                │
│  Team reviews, approves                                     │
│            │                                                │
│            ▼                                                │
│  Future PRs benefit                                         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Feedback Signals

| Signal | Meaning |
|--------|---------|
| **Human approves without comment** | LLM review was sufficient |
| **Human adds new finding** | LLM missed something |
| **Human dismisses LLM finding** | False positive |
| **Human requests re-review** | Something changed |
| **Human escalates LLM finding** | LLM was right but undersold it |

### False Positive Management

False positives erode trust. The system should:

1. **Track dismissals** — Which findings get ignored?
2. **Identify patterns** — Are certain checks consistently wrong?
3. **Adjust confidence** — Lower confidence for noisy checks
4. **Propose removal** — Checks that are mostly wrong should be reconsidered

---

## Integration Points

### Source Control Platform

The system integrates with source control (GitHub, GitLab, etc.) for:

| Capability | Purpose |
|------------|---------|
| **Webhook Events** | Trigger reviews on PR events |
| **PR API** | Read diff, metadata, comments |
| **Review API** | Post findings as review comments |
| **Status API** | Report pass/fail status |
| **Annotation API** | Inline comments on specific lines |

### CI/CD Pipeline

The system can integrate with CI/CD for:

| Capability | Purpose |
|------------|---------|
| **Test Results** | Incorporate test outcomes into review |
| **Linter Output** | Avoid duplicating linter findings |
| **Build Status** | Adjust review based on build state |
| **Artifact Analysis** | Review generated artifacts |

### Multi-Agent Framework

As part of the broader [Multi-Agent Framework](../docs/Foundational-Technical-Requirements.md#foundation-3-multi-agent-framework), the PR Reviewer System:

| Interaction | Description |
|-------------|-------------|
| **Receives tasks** | From orchestrator when PRs need review |
| **Provides findings** | To synthesis/reporting agents |
| **Requests analysis** | From Codebase Analysis Engine for deeper context |
| **Reports metrics** | To Self-Reflection Framework for improvement |

### Documentation Intelligence

Integrates with [Documentation Intelligence](../docs/Foundational-Technical-Requirements.md#foundation-1-documentation-intelligence) to:

- Understand project conventions from docs
- Check documentation completeness
- Identify doc-code drift introduced by PR

---

## Security Considerations

### Access Model

The PR Reviewer System needs read access to review, but should have minimal write capability:

| Access | Level |
|--------|-------|
| **Repository Code** | Read |
| **PR Metadata/Diff** | Read |
| **Review Comments** | Write (create/edit own) |
| **Status Checks** | Write (update) |
| **Repository Settings** | None |
| **Merge Capability** | None |

### Credential Handling

- Credentials should be short-lived and scoped
- No credentials stored persistently
- Audit logging for all access

### Untrusted Input

PRs may contain malicious content. The system should:

- Never execute code from PRs
- Sanitize content before rendering
- Be resilient to prompt injection attempts
- Treat all PR content as untrusted

### Review Integrity

Reviews should be:

- **Attributable** — Clear that they're automated
- **Auditable** — Logs of what was reviewed and why
- **Verifiable** — Reproducible given the same inputs

---

## Quality Attributes

### Performance

| Metric | Target Range |
|--------|--------------|
| **Time to First Review** | Seconds to minutes, not hours |
| **Review Latency** | Proportional to PR size |
| **Concurrent Reviews** | Scale with demand |

### Reliability

| Metric | Target Range |
|--------|--------------|
| **Availability** | Reviews happen when PRs are created |
| **Graceful Degradation** | Partial review better than no review |
| **Idempotency** | Same PR, same inputs → same findings |

### Accuracy

| Metric | Target Range |
|--------|--------------|
| **False Negative Rate** | Low for security; acceptable for style |
| **False Positive Rate** | Low enough to maintain trust |
| **Confidence Calibration** | High-confidence findings are actually correct |

### Usability

| Metric | Target Range |
|--------|--------------|
| **Review Digestibility** | Can be read in minutes, not hours |
| **Actionability** | Findings lead to clear next steps |
| **Noise Ratio** | Signal dominates noise |

---

## Open Design Questions

These questions are intentionally left open for implementers to answer based on their context:

### Architectural Questions

1. **Single Agent vs. Multi-Agent**: Should each domain be a separate agent, or should one agent handle all domains?

2. **Synchronous vs. Asynchronous**: Should reviews block on PR creation, or post asynchronously?

3. **Caching Strategy**: How much analysis can be cached across PRs that touch similar code?

4. **Incremental Analysis**: How to efficiently re-review when PRs are updated?

### Behavioral Questions

5. **Self-Review**: Should the system review PRs created by automated agents (including itself)?

6. **Review Frequency**: How often to re-review? On every commit? On request only?

7. **Severity Thresholds**: What severity level should block merges vs. warn vs. note?

8. **Large PR Handling**: Should large PRs get partial review, size warning only, or full review with caveats?

### Integration Questions

9. **Platform Abstraction**: How to support multiple source control platforms?

10. **CI Coordination**: How to avoid duplicating CI linter/checker output?

11. **Human Override**: How should humans signal "I've seen this, it's fine"?

12. **Team Customization**: How much should teams be able to customize reviewer behavior?

---

## Anti-Patterns to Avoid

### Anti-Pattern: Review Spam

**Problem:** System posts so many comments that they're ignored.

**Why it fails:** Noise drowns signal. Developers learn to skip automated reviews.

**Instead:** Prioritize ruthlessly. Better to catch 5 important things than flag 50 marginal ones.

### Anti-Pattern: False Precision

**Problem:** System reports exact numbers for uncertain metrics.

```
Test coverage: 73.4%  ← precise but possibly meaningless
```

**Why it fails:** False precision implies false confidence. Coverage numbers vary by tool.

**Instead:** Report confidence bands or qualitative assessments when quantitative precision isn't meaningful.

### Anti-Pattern: Context-Free Flagging

**Problem:** System applies rules without considering context.

```
[WARNING] Function has no docstring
(on an internal helper function in a test file)
```

**Why it fails:** One-size-fits-all rules create noise.

**Instead:** Consider file location, function visibility, project conventions.

### Anti-Pattern: Redundant Feedback

**Problem:** System repeats feedback already given by CI.

```
[ERROR] Linting failed
(CI already reported this with full details)
```

**Why it fails:** Duplicates information without adding value.

**Instead:** Reference CI results; add insight only if there's something new.

### Anti-Pattern: Blocking on Style

**Problem:** Style violations block PR merges.

**Why it fails:** Style should be handled by formatters. If it reaches review, fix the tooling.

**Instead:** Style findings should be suggestions at most.

### Anti-Pattern: Hiding Uncertainty

**Problem:** System presents uncertain findings as definitive.

**Why it fails:** Overconfident systems lose trust when wrong.

**Instead:** Express uncertainty. "This might be a SQL injection vulnerability" is better than definitive incorrectness.

---

## Success Criteria

The PR Reviewer System succeeds when:

### For Authors

- Feedback arrives quickly (minutes, not hours)
- Findings are actionable and understandable
- Critical issues are surfaced before human review
- Time to merge decreases

### For Reviewers

- Mechanical concerns are pre-resolved
- Attention focuses on strategic concerns
- Review workload is sustainable
- Trust in automated findings is high

### For Teams

- Code quality improves measurably
- Security vulnerabilities decrease
- Onboarding is faster (conventions are documented in feedback)
- Review culture is healthier

### For the System

- False positive rate decreases over time
- Coverage expands based on human feedback
- Feedback loop is closed (human input improves automation)
- System explains itself well

---

## Related Documents

| Document | Relationship |
|----------|--------------|
| [Foundational Technical Requirements](../docs/Foundational-Technical-Requirements.md) | Parent specification defining all seven foundations |
| [LLM-First Code Reviews](../docs/LLM-Assisted-Code-Review.md) | Philosophy document (Pillar 1: Intentionality) |
| [Multi-Agent Framework](../docs/Foundational-Technical-Requirements.md#foundation-3-multi-agent-framework) | Infrastructure this system runs on |
| [Codebase Analysis Engine](../docs/Foundational-Technical-Requirements.md#foundation-5-codebase-analysis-engine) | Deep code understanding capability |
| [Continual Self-Reflection Framework](../docs/Foundational-Technical-Requirements.md#foundation-6-continual-self-reflection-framework) | How the system improves itself |

---

*This specification is designed to be implemented creatively. The patterns and principles matter more than any specific implementation detail.*

Authored-by: jib
