---
pulse-tags: ["documentation-standards", "governance", "evidence-hierarchy", "workflow", "guidelines"]
pulse-connections: [{"path": "recalibration-matrix.md", "relation": "supports", "why": "Establishes the governance and evidence hierarchy principles applied during the foundation recalibration process."}, {"path": "open-questions.md", "relation": "supports", "why": "Mandates the creation and structure of open questions whenever documentation conflicts or architectural ambiguities occur."}]
---

# Documentation Rules

## Purpose

This repository contains the authoritative documentation for the Gaia
project.

The documentation must reflect the current state of Gaia while clearly
distinguishing implemented behavior, architectural decisions, planned
work, and unresolved questions.

---

## 1. Source of Truth

Use the following evidence hierarchy:

1. Current implementation
2. Current tests
3. Explicit architectural decisions
4. Current specifications
5. Existing documentation
6. Historical documentation
7. Comments and informal notes

Do not assume that older documentation is still correct.

---

## 2. Never Guess

Do not invent architecture, behavior, contracts, dependencies, or intent.

If the available evidence is insufficient, record the uncertainty as an
open question.

---

## 3. Implementation vs Intent

Always distinguish between:

- implemented
- designed
- proposed
- deprecated
- unresolved

Do not describe planned or proposed behavior as implemented.

Do not assume that existing code necessarily represents the intended
architecture.

---

## 4. Conflicts

When sources disagree:

1. Identify the conflict.
2. Gather evidence from the relevant repositories.
3. Determine whether the conflict can be resolved from existing decisions.
4. If it cannot be resolved, create an open question.
5. Do not silently choose an architectural interpretation.

---

## 5. Documentation Changes

Before changing a document:

1. Read the existing document.
2. Inspect the relevant implementation.
3. Check related documentation.
4. Identify contradictions.
5. Preserve valid information.
6. Update only claims supported by evidence.
7. Record unresolved issues separately.

Do not rewrite documentation merely for stylistic consistency.

---

## 6. Architecture Decisions

The documentation agent must not make unresolved architectural decisions.

The agent may:

- identify architectural conflicts
- explain implications
- compare documented alternatives
- identify affected components
- propose questions
- update documentation after a decision has been made

Architectural decisions must remain explicit and traceable.

---

## 7. Open Questions

Create an open question when:

- documentation conflicts with implementation
- two architectural documents disagree
- implementation intent is unclear
- an important architectural decision is missing
- ownership of a responsibility is unclear
- a contract is ambiguous
- multiple interpretations are possible

Each significant question should contain:

- ID
- question
- evidence
- affected areas
- why the decision matters
- decision required

---

## 8. Historical Documentation

Do not silently rewrite history.

Current documentation should describe the current system.

Historical architectural decisions should be preserved separately,
preferably as ADRs or decision records.

---

## 9. Cross-Repository Consistency

Gaia may consist of multiple repositories.

When changing documentation, check whether the change affects:

- other repositories
- shared contracts
- APIs
- data flows
- architecture diagrams
- deployment documentation
- development documentation

Do not update one document while knowingly leaving contradictory
documentation elsewhere.

---

## 10. Audit Before Rewrite

For a large documentation update, first perform an audit.

The audit should identify:

- outdated documentation
- missing documentation
- contradictions
- implementation/documentation mismatches
- cross-repository inconsistencies
- unresolved architectural questions
- proposed documentation changes

Only then perform large-scale updates.

---

## 11. Evidence

Every significant architectural claim must be traceable to evidence.

Prefer explicit references to:

- repository
- file
- component
- test
- ADR
- specification

Do not present an AI interpretation as an established project decision.

---

## 12. Final Verification

After documentation changes, verify that:

- documented behavior matches the current implementation
- planned behavior is clearly marked
- deprecated behavior is clearly marked
- unresolved questions remain visible
- no unsupported assumptions were introduced
- cross-repository references remain valid
- architecture documents do not contradict one another

---

## 13. Optimize For

Accuracy > Completeness

Evidence > Assumption

Explicit Decisions > Inferred Intent

Current State > Outdated Documentation

Traceability > Convenience

Architectural Clarity > Stylistic Consistency