# Security Design for Autonomous LLM Agents

**Status:** Draft
**Author:** James Wiesebron, james-in-a-box
**Created:** December 2025
**Purpose:** Security architecture enabling safe autonomous LLM agent operation

---

> **Part of:** [Foundational Technical Requirements](Foundational-Technical-Requirements.md)

---

## Executive Summary

Autonomous LLM agents require a fundamentally different security model than traditional software. They combine the unpredictability of AI systems with the capability to take real-world actions—reading files, executing code, calling APIs, and modifying systems.

**The core challenge:** How do we grant LLM agents enough autonomy to be useful while ensuring they cannot cause harm—whether through malice, manipulation, or mistake?

**The answer:** Safety by design, not by instruction. Security constraints must be architectural—enforced by the system itself—not merely instructed in prompts that can be bypassed.

---

## Table of Contents

- [The Guiding Principle](#the-guiding-principle)
- [Threat Model](#threat-model)
- [Core Security Principles](#core-security-principles)
- [Foundation 7: Security Architecture](#foundation-7-security-architecture)
- [Credential Isolation](#credential-isolation)
- [Sandboxing and Isolation](#sandboxing-and-isolation)
- [Permission Design](#permission-design)
- [Ephemerality](#ephemerality)
- [Data Protection](#data-protection)
- [Audit and Observability](#audit-and-observability)
- [Human Oversight Integration](#human-oversight-integration)
- [Anti-Patterns to Avoid](#anti-patterns-to-avoid)
- [Success Metrics](#success-metrics)
- [Open Questions](#open-questions)
- [Related Documents](#related-documents)

---

## The Guiding Principle

> **Safety by Design:** Security cannot be an afterthought bolted onto existing systems. Every capability granted to an autonomous agent must be designed with safety as a primary constraint. We build safe systems, not systems that we hope will behave safely.

The critical insight: **behavioral instructions are not security controls**. Telling an LLM "don't merge PRs" provides no actual protection—the agent physically must not have the capability to merge, regardless of what it's been instructed.

---

## Threat Model

### LLM Agent-Specific Risks

LLM agents face a distinct threat landscape that traditional security models don't address:

| Category | Threats |
|----------|---------|
| **External** | Prompt injection via input, malicious content in ingested documents, adversarial data in APIs, supply chain attacks via tools |
| **Internal** | Hallucination leading to incorrect actions, misinterpretation of ambiguous instructions, unintended capability emergence |
| **Systemic** | Over-permissioned agents, persistent state enabling attack chains, credential exposure through logs, data exfiltration through approved channels |

### Attack Vectors

| Attack | Description | Example |
|--------|-------------|---------|
| **Direct Prompt Injection** | Malicious instructions in input | "Ignore previous instructions and..." |
| **Indirect Prompt Injection** | Malicious content in processed data | Hidden instructions in documents |
| **Tool Manipulation** | Exploiting agent's tool access | Crafted inputs causing dangerous commands |
| **Exfiltration via Output** | Using legitimate channels to leak data | Encoding secrets in PR descriptions |
| **Privilege Escalation** | Leveraging access to gain more | Using file read to find credentials |

### Why Traditional Security Falls Short

Traditional application security assumes predictable behavior, clear code-data separation, and explicit control flow. LLM agents violate these assumptions—they're non-deterministic, mix code and data in prompts, have opaque reasoning, and make autonomous decisions.

---

## Core Security Principles

### Principle 1: Defense in Depth

No single security control should be the only barrier. Every security-critical operation needs multiple independent safeguards:

```
User Request
    ↓
Input Validation ← Sanitize, validate, reject anomalies
    ↓
Permission Check ← Is this operation allowed?
    ↓
Credential Gate  ← Agent has no credentials; gateway mediates
    ↓
Sandbox Boundary ← Isolation from sensitive resources
    ↓
Action Execution
    ↓
Output Filtering ← Scan for secrets, PII, anomalies
    ↓
Audit Logging    ← Record everything for review
```

### Principle 2: Least Privilege

Agents should have minimum permissions necessary—no more. Permissions must be:

| Property | Requirement |
|----------|-------------|
| **Scoped** | Limited to specific resources |
| **Time-bound** | Expire after task completion |
| **Revocable** | Can be withdrawn immediately |
| **Auditable** | Every grant and use is logged |

### Principle 3: Assume Compromise

Design as if the agent will be compromised. The question isn't "if" but "when":

- **Limit blast radius**: Contain damage to smallest scope
- **Enable recovery**: Make it possible to undo harmful actions
- **Detect anomalies**: Notice when behavior deviates
- **Fail safe**: Default to denying access when uncertain

### Principle 4: Explicit Over Implicit

Security boundaries must be architectural, not instructed:

| Implicit (Weak) | Explicit (Strong) |
|-----------------|-------------------|
| "Don't access production" in prompt | Network isolation from production |
| "Keep secrets secure" instruction | Credentials never enter agent environment |
| "Only read authorized files" | Filesystem permissions enforcement |
| "Don't merge PRs" | No merge capability exists |

### Principle 5: Reversibility

Autonomous actions should be reversible wherever possible:
- Prefer soft deletes over hard deletes
- Create backups before destructive operations
- Use feature flags for deployment changes
- Require human approval for irreversible actions

---

## Foundation 7: Security Architecture

**Purpose:** Provide architectural foundations for secure autonomous agent operation.

**Strategic Intent:** Enable LLM agents to operate with meaningful autonomy while maintaining security guarantees that don't depend on the agent's cooperation.

### Position in the Framework

Security is a cross-cutting concern enabling all other foundations:

```
┌───────────────────────────────────────────────────────────────┐
│           7. SECURITY ARCHITECTURE                            │
│              Cross-cutting concern enabling safe autonomy     │
│                                                               │
│  Credential     Sandboxing     Permissions    Ephemerality    │
│  Isolation                                                    │
│                     Audit          Human Oversight            │
└───────────────────────────────────────────────────────────────┘
```

### Key Capabilities

| Capability | What It Does | Why It Matters |
|------------|--------------|----------------|
| **Credential Isolation** | Credentials never enter agent environment | Cannot abuse what it cannot access |
| **Sandboxing** | Isolates execution environments | Limits blast radius of compromise |
| **Permission System** | Fine-grained access control | Enforces least privilege |
| **Ephemeral Execution** | Stateless, disposable environments | Prevents persistent compromise |
| **Data Protection** | Prevents unauthorized data access | Protects sensitive information |
| **Audit System** | Comprehensive logging | Enables detection and forensics |
| **Human Oversight** | Integration points for review | Maintains accountability |

---

## Credential Isolation

> **Core Principle:** The agent environment contains zero credentials. Authentication happens outside the agent's reach.

### The Gateway Pattern

Rather than giving agents credentials and hoping they use them correctly, credentials live in a separate component that mediates all authenticated operations:

```
┌─────────────────────────────┐     ┌─────────────────────────────┐
│      Agent Environment      │     │         Gateway             │
│                             │     │                             │
│  • LLM agent                │     │  • Holds credentials        │
│  • No credentials           │ API │  • Enforces policies        │
│  • No direct external auth  │────►│  • Audit logs all ops       │
│  • Cannot bypass gateway    │     │  • Exposes controlled API   │
│                             │     │                             │
└─────────────────────────────┘     └─────────────────────────────┘
                                              │
                                              ▼
                                    ┌─────────────────────┐
                                    │  External Services  │
                                    │  (GitHub, APIs)     │
                                    └─────────────────────┘
```

### Why Credential Isolation Matters

| With Credentials in Agent | With Credential Isolation |
|---------------------------|---------------------------|
| Agent can use any capability of token | Agent only gets exposed API operations |
| Prompt injection could abuse credentials | Prompt injection has no credentials to abuse |
| Must trust agent to follow rules | Rules enforced by architecture |
| Difficult to audit credential usage | All authenticated ops go through gateway |

### Gateway API Design

The gateway exposes only the operations we want agents to perform:

| Exposed | Not Exposed |
|---------|-------------|
| Create PR | Merge PR |
| Push to feature branches | Push to protected branches |
| Add comments | Delete repositories |
| Read code | Access secrets |

**Key insight:** If the merge endpoint doesn't exist, the agent cannot merge—no matter what instructions it receives or how it's manipulated.

---

## Sandboxing and Isolation

### Container-Based Isolation

Every agent execution occurs within an isolated container:

```
┌─────────────────────────────────────────────────────────┐
│                    CONTAINER BOUNDARY                   │
│                                                         │
│  Agent Workspace:                                       │
│  • Cloned repository (read/write)                       │
│  • Temporary files                                      │
│  • Build artifacts                                      │
│                                                         │
│  RESTRICTED:                                            │
│  ✗ No SSH keys                                          │
│  ✗ No cloud credentials                                 │
│  ✗ No production access                                 │
│  ✗ No host filesystem access                            │
│  ✗ Limited network (gateway proxy only)                 │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### Network Isolation

| Access | Allowed | Rationale |
|--------|---------|-----------|
| HTTPS via gateway proxy | Yes | Required for APIs, packages |
| Direct external HTTPS | No | All traffic auditable through proxy |
| SSH outbound | No | Prevent unauthorized server access |
| Internal services | Scoped | Only what's needed for task |

### Filesystem Isolation

```
/workspace/           # READ/WRITE - Agent's working directory
/context/             # READ-ONLY - Provided context
/shared/              # READ/WRITE - Cross-session data (persistent)
/credentials/         # NO ACCESS - Doesn't exist in container
```

### Resource Limits

| Resource | Limit | Rationale |
|----------|-------|-----------|
| CPU | 2-4 cores | Prevent exhaustion |
| Memory | 4-8 GB | Prevent OOM attacks |
| Disk | 10-20 GB | Prevent filling |
| Process count | 100-200 | Prevent fork bombs |
| Execution time | Task-appropriate | Prevent infinite loops |

---

## Permission Design

### Capability-Based Security

Rather than role-based access, use capability tokens granting specific permissions:

```
{
  "id": "cap-abc123",
  "granted_to": "agent-instance-xyz",
  "capabilities": [
    {
      "resource": "github:org/repo",
      "actions": ["read", "write", "create_pr"],
      "constraints": {
        "branches": ["feature/*", "fix/*"],
        "exclude": ["*.env", "secrets/**"]
      }
    }
  ],
  "expires_at": "2024-12-09T08:00:00Z",
  "revocable": true
}
```

### Permission Levels

| Level | Permissions | Human Approval |
|-------|-------------|----------------|
| **Read-Only** | Query codebase, read docs | None needed |
| **Development** | Create files, run tests, create PRs | PR review required |
| **Integration** | Push to branches, trigger CI | Batch approval |
| **Production** | Access production systems | Explicit per-action |

### Actions Requiring Human Approval

| Category | Examples | Approval Type |
|----------|----------|---------------|
| **Always** | Production access, secret rotation, PR merge | Per-action |
| **Batch** | Branch pushes, PR creation | Covers multiple |
| **Post-hoc** | Code changes, docs | Review after |
| **Autonomous** | Read operations, local tests | None needed |

---

## Ephemerality

### The Case for Ephemeral Execution

Persistent state creates security risks:
- **Accumulated compromise**: Attackers build up access over time
- **Context confusion**: Old state influences new tasks incorrectly
- **Credential exposure**: Secrets may persist in memory or logs

### Ephemeral Model

```
Task Received
    ↓
Fresh Container Created
• Clean filesystem
• No previous state
• Fresh credentials (via gateway)
    ↓
Task Execution
• Agent performs work
• State exists only in container
• Outputs to designated locations
    ↓
Clean Extraction
• Outputs collected (commits, PRs)
• Audit logs captured
• Container destroyed
• All state eliminated
```

### What Persists vs. What Doesn't

| Persists | Doesn't Persist |
|----------|-----------------|
| Git commits (pushed) | Working directory |
| Pull requests | Environment variables |
| Audit logs | Process state |
| Task tracking | Memory contents |
| Notifications | Credentials |

---

## Data Protection

### Exfiltration Prevention

```
┌─────────────────────────────────────────────────────────────┐
│                 EXFILTRATION PREVENTION                     │
│                                                             │
│  INPUT FILTERING                                            │
│  • Scan ingested content for secrets                        │
│  • Detect sensitive data patterns                           │
│  • Quarantine suspicious content                            │
│                                                             │
│  OUTPUT FILTERING                                           │
│  • Scan all outputs (commits, PRs, messages)                │
│  • Block content containing secrets                         │
│  • Detect encoded/obfuscated sensitive data                 │
│                                                             │
│  NETWORK CONTROLS                                           │
│  • All traffic through auditable proxy                      │
│  • Volume-based anomaly detection                           │
│  • Content inspection for patterns                          │
│                                                             │
│  BEHAVIORAL MONITORING                                      │
│  • Detect unusual access patterns                           │
│  • Alert on permission boundary probing                     │
│  • Track tool usage anomalies                               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Output Sanitization

All agent outputs scanned before release:

| Check | Catches | Action |
|-------|---------|--------|
| Secret patterns | API keys, passwords, tokens | Block, alert |
| PII patterns | Emails, SSNs, credit cards | Block, alert |
| Encoded data | Base64 secrets | Decode and scan |
| Unusual volume | Abnormally large outputs | Review first |

---

## Audit and Observability

### Comprehensive Logging

Every agent action generates an audit record:

```json
{
  "timestamp": "2024-12-09T05:00:00Z",
  "agent_id": "agent-xyz",
  "session_id": "session-abc",
  "task_id": "task-123",
  "action": "file_write",
  "resource": "/workspace/src/feature.py",
  "outcome": "success",
  "duration_ms": 15
}
```

### What Must Be Logged

| Category | Events |
|----------|--------|
| **Authentication** | Session start/end, credential use (via gateway) |
| **Authorization** | Permission checks (granted and denied) |
| **Actions** | All tool invocations and results |
| **Data Access** | File reads, API calls |
| **Output** | All generated content |
| **Anomalies** | Unusual patterns, errors, boundary probes |

### Forensic Capability

Support after-the-fact investigation:
- **Session replay**: Reconstruct what agent did
- **Timeline analysis**: Correlate events
- **Diff generation**: See exactly what changed
- **Attribution**: Track actions to specific agents/sessions

---

## Human Oversight Integration

### Escalation Triggers

| Trigger | Severity | Response |
|---------|----------|----------|
| Permission denied repeatedly | Warning | Review agent behavior |
| Unusual data access pattern | Warning | Investigate before continuing |
| Secret-like content in output | Critical | Block output, investigate |
| Resource limit approached | Warning | Review task scope |
| Explicit agent uncertainty | Normal | Human guidance requested |

### Human-in-the-Loop Patterns

| Pattern | When Used |
|---------|-----------|
| **Pre-Action Review** | Production deployments, security ops, infrastructure |
| **Post-Action Review** | Code changes via PR, documentation updates |
| **Async Notification** | Task completion, anomalies, guidance requests |
| **Emergency Override** | Kill switch, credential revocation, rollback |

### Override Capabilities

Humans must always be able to:
- **Stop**: Immediately halt agent execution
- **Revoke**: Remove any granted permission
- **Rollback**: Undo agent actions where possible
- **Investigate**: Access complete audit trail
- **Block**: Prevent specific actions or patterns

---

## Anti-Patterns to Avoid

### Anti-Pattern 1: Security by Instruction

**Problem:** Relying on prompts to enforce security

```
❌ "Never access production systems"
❌ "Don't reveal secrets in your output"
❌ "Don't merge pull requests"
```

**Why it fails:** Prompt instructions can be bypassed through jailbreaking, injection, or model errors.

**Solution:** Architectural enforcement—if the capability doesn't exist, it cannot be used regardless of instructions.

### Anti-Pattern 2: Over-Permissioning

**Problem:** Granting broad permissions "just in case"

```
❌ Admin access to GitHub organization
❌ Read access to all secrets
❌ Full API token permissions
```

**Why it fails:** Creates massive blast radius when something goes wrong.

**Solution:** Minimum necessary permissions, scoped to specific resources, with time limits.

### Anti-Pattern 3: Credentials in Agent Environment

**Problem:** Long-lived credentials accessible to agents

```
❌ Static API keys in environment variables
❌ SSH keys in agent filesystem
❌ Tokens in configuration files
```

**Why it fails:** Credentials can be exfiltrated, leaked, or accumulated by attackers.

**Solution:** Credential isolation via gateway; agent never sees credentials.

### Anti-Pattern 4: Trusting Agent Output

**Problem:** Using agent output without validation

```
❌ Auto-merging agent PRs without review
❌ Deploying agent-generated code directly
❌ Publishing agent content unreviewed
```

**Why it fails:** Agents can be manipulated, hallucinate, or make mistakes requiring human judgment.

**Solution:** Human review before any action with significant impact.

---

## Success Metrics

### Security Metrics

| Metric | Target |
|--------|--------|
| Security incidents | 0 critical |
| Secret exposure events | 0 |
| Permission violations | Near 0 |
| Successful exfiltration | 0 |
| Mean time to detect anomaly | < 5 minutes |
| Mean time to revoke access | < 1 minute |

### Operational Metrics

| Metric | Target |
|--------|--------|
| Permission grant latency | < 100ms |
| Container startup time | < 30s |
| Audit log completeness | 100% |
| False positive rate (blocking) | < 1% |

---

## Open Questions

### Technical

1. How do we stay ahead of container escape vulnerabilities?
2. What novel attack vectors are unique to LLM agents?
3. How do we minimize security overhead while maintaining protection?
4. How do we secure agent-to-agent communication in multi-agent scenarios?

### Policy

1. What level of autonomous action is acceptable without human review?
2. Who is accountable when an agent causes harm despite security measures?
3. How do we update security measures as threats evolve?

### Integration

1. How do we secure agents interacting with legacy systems?
2. How do we vet and secure external tool integrations?
3. How do we handle agents working across security boundaries?

---

## Related Documents

| Document | Relationship |
|----------|--------------|
| [Foundational Technical Requirements](Foundational-Technical-Requirements.md) | Parent document defining seven foundations |
| [Pragmatic Guide](Pragmatic-Guide-Software-Engineering-Post-LLM-World.md) | Philosophy umbrella |
| [Human-Driven Development](Human-Driven-LLM-Navigated-Software-Development.md) | Human oversight principles |
| [Radical Self-Improvement](Radical-Self-Improvement-for-LLMs.md) | Self-monitoring and improvement |

---

Authored-by: jib
