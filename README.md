---
title: Gaia — Foundation Index
document: index
version: 1.1.0
status: foundation
last_updated: 2026-10-05
owner: Gaia Product Foundation
framing: "Gaia is a lifelong personal intelligence designed to grow through understanding."
---

# Gaia — Foundation Documents

> **Gaia is a lifelong personal intelligence designed to grow through understanding.**

This is the long-term foundation for Gaia. Every document reinforces the same product philosophy: Gaia is the agency, not a shell around any one capability, and she preserves a clean separation between **identity, cognition, memory, capability, and experience**. When any decision is unclear, defer to `vision.md`.

## Layout

This repository keeps three kinds of material apart, because they are read differently:

- **The documents (this folder)** — the canonical, long-lived foundation. Read these.
- **[proposals/](./proposals/)** — V3 target documents and blueprints. They are *not yet adopted*; while their open decisions remain unresolved, the documents above stay authoritative.
- **[sources/](./sources/)** — the raw input documentation was derived from: research papers, conversation transcripts, and proposal PDFs. Input, not documentation.
- **[archief/](./archief/)** — the previous folder structure, kept for provenance, not for reading. It holds only material that exists **nowhere else** in this repository (old development notes, the Chronicle manifests, early decision records). Superseded versions of the documents above, and any byte-identical copy, have been removed: **one version of each file across the whole repository, never two.** Git history keeps every earlier state.

## The Documents

| # | Document | Defines |
|---|----------|---------|
| 1 | [vision.md](./vision.md) | What/why/who Gaia is, philosophy, values, success criteria, what she must never become |
| 2 | [architecture.md](./architecture.md) | System boundaries (SOUL · Logos · Hindsight · Capabilities [Hermes, Melodiq, SongCompanion, MCP, …] · Gaia Desktop), flows, streaming lifecycle, storage abstraction, model agnosticism |
| 3 | [soul.md](./soul.md) | Identity: Gaia's voice, values and limits; small and stable, never rewritten by learned patterns |
| 4 | [design-language.md](./design-language.md) | How Gaia feels daily; visual, spatial, motion, and communication philosophy |
| 5 | [personality.md](./personality.md) | Gaia as a person-like presence; style, initiative, boundaries, trust, consistency |
| 6 | [roadmap.md](./roadmap.md) | V1→V3→Long-term, MoSCoW, intentionally small V1, maturity path |
| 7 | [coding-standards.md](./coding-standards.md) | Structure, contracts, state, testing, dependency governance, maintainability |
| 8 | [ui-principles.md](./ui-principles.md) | Conversation-first, calm, silence, motion-as-meaning, legible growth |
| 9 | [evolution.md](./evolution.md) | The implementation history — how the architecture and decisions actually changed over time |
| 10 | [split-plan.md](./split-plan.md) | Boundaries between Gaia Cloud / Web / Desktop in the current monorepo and the phased plan to split them into three independent repositories |
| 11 | [web-migration-plan.md](./web-migration-plan.md) | The web client's migration onto the Gaia API |
| 12 | [operations.md](./operations.md) | *(not a foundation document — a living reference)* Where things actually run: the admin interface, deployment addresses, how to reach each service |

`lexicon.md` and `principles.md` are companion references: the vocabulary and the design principles that cut across the documents above.

## Proposals

The V3 documents live in [proposals/](./proposals/) while their open decisions remain unresolved. Until they are explicitly adopted, the foundation set above remains authoritative; their status notes distinguish the target architecture from what is present in the repositories.

- [proposals/foundation-v3.md](./proposals/foundation-v3.md) — **Proposal:** V3 foundation for identity, human authorship, understanding, model independence, and epistemic principles.
- [proposals/architecture-v3.md](./proposals/architecture-v3.md) — **Proposal:** V3 target architecture with repository implementation status and unresolved architecture decisions.
- [proposals/gaia-architecture-v3-0-rewrite-proposal.md](./proposals/gaia-architecture-v3-0-rewrite-proposal.md), [proposals/universal-foundation-comprehensive-design-architecture.md](./proposals/universal-foundation-comprehensive-design-architecture.md) — earlier, broader V3 rewrites.

## Gaia's Structure

- **Gaia** → the agency herself — acts, decides, maintains continuity. **Runs in Gaia Cloud.**
- **SOUL** → Identity  ·  **Logos** → Cognition, Gaia's own reasoning faculty, with intent interpretation, meaning & evidence, and DecisionIQ as prompt-level faculties of one integrated pass  ·  **Hindsight** → the derived knowledge store, load-bearing for continuity, never optional
- **Capabilities** → optional instruments Gaia reaches for when they serve her goals — Hermes (reasoning), Melodiq (music), SongCompanion (song work), MCP (actions), and others  ·  **Gaia Desktop** → Experience, and her first **client**

These remain separate over time. No system absorbs another's role. No capability — Hermes included — is a default; Gaia decides, turn by turn, whether one is needed at all. No client — Gaia Desktop included — hosts Gaia; every client reaches her over the Gaia API. Clients are representations of Gaia, never instances of her.

## Resolved Open Questions (stances documented)

- **Where does Gaia run?** → Gaia Cloud, from V1 — not a desktop-hosted brain (architecture §2, "Deployment Topology").
- **Offline-first?** → Network-dependent initially with an offline-graceful shell (architecture §11).
- **Memory provenance visibility?** → Always available on demand, never omnipresent (architecture §8, ui-principles §9).
- **Proactivity level?** → Earned, tiered, reversible; ceiling is "never noisy" (personality §2, roadmap §8).
- **Personality variability?** → Stable core, subtle contextual expression (personality §10).
- **Infrastructure beyond Gaia Cloud?** → Only on a proven need the baseline Gaia Cloud runtime cannot own (architecture §9).
