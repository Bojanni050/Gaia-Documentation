# Gaia Documentation — Inventory

> **Phase 1 output.** This inventory records *what was found and where it was put*.
> It does not assert that any document is correct. Statuses are deliberately
> conservative: `unclear` means the documentation alone did not establish
> whether the document is still current.
>
> No document content was rewritten, merged, split, or corrected in this phase.

---

## 1. Structure

```text
/
├── rules.md                       # how documentation is maintained
├── inventory.md                   # this file
├── recalibration-matrix.md        # per-statement foundation claims vs. v3.0 claims
├── open-questions.md              # unresolved questions
├── .sources.yaml                  # source repository registry
│
├── foundation/                    # what Gaia is, and the concepts she rests on
│
├── architecture/                  # how Gaia is structured
│   ├── components/                # (empty)
│   ├── flows/                     # (empty)
│   ├── contracts/                 # (empty)
│   └── decisions/                 # recorded decisions and their reasoning
│
├── development/                   # working on Gaia
│
└── operations/                    # running Gaia
```

`architecture/components/`, `architecture/flows/` and `architecture/contracts/`
are currently empty. That is a real finding, not an omission: no document in the
repository was written as a description of a single component, a flow, or an
interface contract. See Q-006.

An empty `open questions/` folder present before this phase was removed; its
role is taken by `open-questions.md` at the root, per the target structure.

---

## 2. Inventory table

| Document | Location | Primary purpose | Status | Notes |
|---|---|---|---|---|
| recalibration-matrix.md | `recalibration-matrix.md` | Per-statement comparison of foundation claims vs. v3.0 claims | current | **Created at the end of Phase 1.** Not a foundation document. Holds no Gaia facts of its own — only the questions that must be answered against code before any foundation document may be rewritten. Produces Q-015 and Q-016. |
| architecture-v3.0.md | `architecture/architecture-v3.0.md` | v3.0 system architecture: harnas, Logos consolidation, memory pipe, 5-repo split, HADES | unclear | **Added after the first Phase 1 pass.** Self-declares "Status: Definitief (September 2026)". Does not use front-matter; no version, no `last_updated` field. Nearly duplicates `foundation/v3.0-foundation.md`. |
| v3.0-foundation.md | `foundation/v3.0-foundation.md` | v3.0 foundation constitution: core law, principles, epistemic discipline | unclear | **Added after the first Phase 1 pass.** Self-declares "Definitief (Herziening September 2026)" and "**Vervangt:** Universal Foundation / Gaia Master Foundation (v2.x)". Same six sections as `architecture-v3.0.md`, shorter. |
| rules.md | `rules.md` | Maintenance rules for the documentation itself | current | Special file. Contains no Gaia architectural facts, correctly. |
| open-questions.md | `open-questions.md` | Unresolved questions | current | Was empty (0 bytes) before this phase. Populated now. |
| .sources.yaml | `.sources.yaml` | Registry of related source repositories | unclear | Not a document. Contains a corrupted URL (`hermes-agentvvvvv…`) and a broken YAML structure. Flagged, not fixed. |
| index.md | `foundation/index.md` | Index of the foundation documents | duplicate/overlapping, unclear | Was `README.md`. Lists 9 documents, 8 of which have since moved out of the root. Its relative links (`./vision.md` etc.) are now broken. |
| vision.md | `foundation/vision.md` | What Gaia is, why, for whom, success criteria | unclear | Self-declares `last_updated: 2026-06-01`, v1.0.0. Calls Gaia "desktop-first"; contradicts `architecture.md` and `operations.md`, which place Gaia in Gaia Cloud. Not reconciled here. |
| principles.md | `foundation/principles.md` | Durable principles everything else derives from | unclear | No front-matter, no version, no date. Cannot date it from content. |
| personality.md | `foundation/personality.md` | Gaia as a person-like presence: style, initiative, trust | unclear | v1.0.0, 2026-06-01. Records several "open question resolved" stances inline. |
| soul.md | `foundation/soul.md` | Architectural overview of SOUL (identity layer) | unclear | Explicitly states it is *not* the constitution: the canonical SOUL is `services/gaia-api/identity/soul.md` in the code repo. This doc is a pointer, not the source of truth. |
| lexicon.md | `foundation/lexicon.md` | Gaia's vocabulary; translation rules | unclear | No front-matter, no date. Contains no architecture. |
| design-language.md | `foundation/design-language.md` | Visual, spatial, motion and communication philosophy | unclear | v1.0.0, 2026-06-01. Substantially overlaps `ui-principles.md`. |
| ui-principles.md | `foundation/ui-principles.md` | Concrete UX rules derived from the design language | duplicate/overlapping, unclear | Self-describes as a translation of `design-language.md`. Overlap is by design; the degree is not established. |
| roadmap.md | `foundation/roadmap.md` | V1→V3→long-term product direction, MoSCoW | unclear | v1.0.0, 2026-06-01 — predates every `gaia-api` reference in the other documents. May describe an earlier product shape. |
| chronicle-manifest-v6-core-manifest.md | `foundation/chronicle-manifest-v6-core-manifest.md` | Chronicle's core constitution (v6) | unclear | Describes **Chronicle**, not Gaia. Relationship to Gaia is not stated in the document. |
| chronicle-manifest-v5-core-manifest.pdf | `foundation/chronicle-manifest-v5-core-manifest.pdf` | Chronicle core constitution, v5 | duplicate/overlapping, historical | Byte-identical in size to the two copies below. Superseded by v6 per its own numbering. |
| chronicle-manifest-v5-core-manifest (1).pdf | `foundation/…(1).pdf` | same as above | duplicate/overlapping, historical | Download artifact. Exact duplicate; kept per Phase 1 rules. |
| chronicle-manifest-v5-core-manifest (2).pdf | `foundation/…(2).pdf` | same as above | duplicate/overlapping, historical | Download artifact. Exact duplicate; kept per Phase 1 rules. |
| chronicle-philosophy-maker-facts-google-gemini.pdf | `foundation/…google-gemini.pdf` | Raw Gemini conversation that produced the Chronicle philosophy shift | historical | A transcript, not a specification. It is the apparent origin of the v5→v6 "niche → universal" reframing. |
| chronicle-creative-companion-architecture.pdf | `foundation/chronicle-creative-companion-architecture.pdf` | Creative Companion architecture (scanned) | incomplete | **Text is not machine-extractable** — the PDF contains only images. Content could not be read or verified. |
| first-person-authority-inverse-planning.pdf | `foundation/…` | Academic paper: first-person authority vs. Bayesian inverse planning | unclear | Standalone academic research. Its connection to Gaia is not stated in the document. |
| horizon-merge-in-formal-logic.pdf | `foundation/…` | Academic paper: formalising Gadamer's Horizontverschmelzung for human–AI dialogue | unclear | Standalone academic research. Connection to Gaia not stated. |
| intent-between-mental-state-interpretation-interaction.pdf | `foundation/…` | Academic paper: layered temporal model of communicative intent | unclear | Standalone academic research. Connection to Gaia not stated. |
| gaia-onboarding-design-ideas.md | `foundation/gaia-onboarding-design-ideas.md` | Onboarding design ideas; two user trajectories | incomplete, unclear | Ideas phase, not a decision. Its header cites "Hindsight/Chronicle-architectuur zoals vastgelegd in het afwegingen-document" — a cross-reference to another document in this repo. |
| architecture.md | `architecture/architecture.md` | System architecture: boundaries, topology, flows, storage | unclear | v2.4.0, 2026-08-20. The most-referenced document in the repository. Its own status field says `foundation`, which conflicts with its placement here. |
| gaia-architecture-v3-0-rewrite-proposal.md | `architecture/…` | Proposal to rewrite architecture.md to v3.0 | incomplete, unclear | Self-declares "Status: voorstel". Supersedes 7 specific things in v2.4.0 — including removing intentIQ/reasonIQ as named Logos sublayers. Not applied. |
| evolution.md | `architecture/evolution.md` | Historical record of milestones, trade-offs and lessons | historical / current | Self-describes as "not a changelog — a record of intent". Contains the most concrete implementation evidence in the repo (file paths, provider names, milestones 0–9+). Highly valuable and highly specific; its claims are unverified against the code. |
| universal-foundation-comprehensive-design-architecture.md | `architecture/…` | Creative Companion / Universal Foundation: design philosophy + architecture | duplicate/overlapping, unclear | 347 KB, the largest document. Overlaps heavily with `architecture.md`, `vision.md` and the Chronicle manifests, under different names for the same concepts. Dutch. |
| gaia-master-foundation.pdf | `architecture/gaia-master-foundation.pdf` | Consolidates Gaia philosophy + Universal Foundation into one document | duplicate/overlapping, unclear | Self-describes as "the ultimate source of truth", which conflicts with `vision.md`'s claim to be the root of the foundation. Contradiction left intact. |
| conversational-guidance-architecture-decision.md | `architecture/decisions/…` | ADR: removal of the per-turn conversational heuristics layer | current (self-declared implemented) | The only document in the repository in ADR shape (Incident → Evidence → Decision → Consequences → Regression risk). Describes a completed change, including removed files. |
| chronicle-hindsight-considerations.md | `architecture/decisions/…` | Decision record: "two pipes, one truth" — Chronicle vs. Hindsight data flow | current (self-declared decided) | Records a settled architectural decision after two external reviews. Referenced as authoritative by `gaia-architecture-v3-0-rewrite-proposal.md` and `gaia-onboarding-design-ideas.md`. |
| wat-gaia-kan-leren-van-vellum.md | `architecture/decisions/…` | Comparative analysis of an external project (Vellum) against Gaia | unclear | Recommendations, not decisions — 4 concrete proposals, none marked as accepted. Placed in `decisions/` because it evaluates architectural options; flagged as a possible misfile. |
| chapters-to-add-universal-document.md | `architecture/decisions/…` | Three proposed chapters to insert into the Universal document | incomplete, unclear | A patch, not a document. Assumes the Universal document's existing chapter numbering (41, 49, 67). Never merged. |
| coding-standards.md | `development/coding-standards.md` | Engineering conventions: structure, contracts, state, testing | unclear | v1.0.0, 2026-06-01. Its project-structure section describes a `/gaia-desktop` layout that predates the `gaia-web` / `gaia-api` split described in `split-plan.md`. |
| split-plan.md | `development/split-plan.md` | Repository boundaries and the plan to split into three repos | unclear | v1.2.0. Most implementation-dense document after `evolution.md`; documents real coupling points and env var names. |
| web-migration-plan.md | `development/web-migration-plan.md` | Plan to move Gaia Web onto `gaia-api` | incomplete, unclear | Self-declares "scoped — not started". Depends on facts stated in `split-plan.md` and `evolution.md`. |
| epistemic-inspector-plan.md | `development/epistemic-inspector-plan.md` | Plan for a Memory & Epistemisch Inspector dashboard | incomplete, unclear | Explicitly a proposal, in Dutch. Notable: it corrects the assumptions of an external AI-generated spec against the real codebase — i.e. it is itself a piece of evidence about the current system. |
| chatgpt-importer-review-and-gemini-preparation.md | `development/…` | Code review of the ChatGPT bulk importer, prep for Gemini | unclear | A code review of code whose current location in the repo is not established by any document here. |
| claude-browser-plugin-for-chronicle-chat-export.md | `development/…` | Raw Claude chat export: designing a Chronicle chat-export browser plugin | historical, unclear | 610 KB raw transcript, mostly cited search results and irrelevant third-party links. No decision recorded. |
| lyric-style-preference-alternative-indie.pdf | `development/…` | Lyric-writing style rules for alternative/indie genres | unclear | **Purpose unclear.** Originally `gpt.pdf` — a bare, meaningless filename. It is a prompt fragment, plausibly for Melodiq/SongCompanion, but no document connects it to Gaia. See Q-007. |
| operations.md | `operations/operations.md` | Where things run: admin UI, deployment addresses, ports | current (self-declared) | The only document in `operations/`. Self-describes as a "living reference" and explicitly *not* a foundation document. Contains hardcoded Tailscale IPs and ports — will go stale. |

---

## 3. Documents with unclear purpose

| Document | Why unclear |
|---|---|
| `.sources.yaml` | Not a document. Registry with a corrupted entry. |
| `foundation/index.md` | Index whose links are now broken; claims a document set that no longer matches the tree. |
| `development/lyric-style-preference-alternative-indie.pdf` | No stated relationship to Gaia. Formerly `gpt.pdf`. |
| `architecture/decisions/wat-gaia-kan-leren-van-vellum.md` | Comparative study producing recommendations, not a decision. Possibly belongs in `architecture/` proper. |
| `foundation/chronicle-creative-companion-architecture.pdf` | Scanned images only — content unverifiable. |
| The three academic PDFs in `foundation/` | Genuine research, but the documents never state why Gaia holds them. |

---

## 4. Duplicate and overlapping material (recorded, not merged)

1. **The Chronicle manifest, three times plus a transcript.**
   V5 exists as three byte-identical PDFs; V6 exists as Markdown. The Gemini
   transcript is the reasoning that produced the V5→V6 change.

2. **Two architecture documents describing the same system.**
   `architecture/architecture.md` (v2.4.0, Gaia's own terms: SOUL / Logos /
   Hindsight / Capabilities) and `architecture/universal-foundation-comprehensive-design-architecture.md`
   (Universal Foundation / Creative Companion terms). They describe overlapping
   territory with different vocabularies and no stated mapping between them.

3. **"Gaia Master Foundation" vs. "Vision" as source of truth.**
   Both claim the root. Both are dated. Neither is reconciled with the other.

4. **Design language vs. UI principles.**
   `design-language.md` and `ui-principles.md` cover the same ground; the second
   declares itself a translation of the first.

5. **Deployment topology in three places.**
   `operations/operations.md` (addresses, ports, workflow), `development/split-plan.md`
   (boundaries, coupling), `architecture/evolution.md` (how `gaia-api` came to
   exist, milestone by milestone). All three describe the same deployment and
   all three carry version-sensitive facts.

6. **Chronicle ↔ Hindsight decision, restated.**
   `architecture/decisions/chronicle-hindsight-considerations.md` holds the
   decision; `architecture/gaia-architecture-v3-0-rewrite-proposal.md` cites it
   as settled; `foundation/gaia-onboarding-design-ideas.md` cites it as
   established. Consistent, but the same architectural fact now lives in three
   places.
7. **v3.0 stated twice, in two documents.** `architecture/architecture-v3.0.md`
   and `foundation/v3.0-foundation.md` cover the same six sections (harnas,
   Logos consolidation, memory pipe, 5-repo split, HADES, SOUL) at different
   lengths. Both self-declare "Definitief (September 2026)". `v3.0-foundation.md`
   additionally declares it **replaces** the Universal Foundation and Gaia Master
   Foundation — i.e. the two v3.x documents supersede two of the documents in
   cluster 2 and 3 above.
8. **The v3.0 rewrite proposal, the v3.0 documents, and v2.4.0 all coexist.**
   `architecture/gaia-architecture-v3-0-rewrite-proposal.md` is a *proposal* for
   the same v3.0 that the two new documents now *state* as final. The proposal
   was not withdrawn, and `architecture/architecture.md` v2.4.0 was not replaced.
9. **Repository count: three different answers.** `development/split-plan.md`
   says three repositories (Cloud / Web / Desktop). `architecture/architecture-v3.0.md`
   and `foundation/v3.0-foundation.md` say **five** (adding `Foundation` and
   `capture-rs`). `.sources.yaml` lists seven entries under different names.
10. **The memory pipe is now stated three times**, with compatible but not
    identical wording: `architecture/decisions/chronicle-hindsight-considerations.md`
    ("twee pijpen, één waarheid"), the v3.0 documents (Chronicle → Hindsight →
    Logos), and `architecture/architecture.md` v2.4.0.

---

## 5. Phase 2 reconciliation candidates

Ordered by how much they will affect the architecture picture. **None of these
were touched.**

1. **Three generations of architecture now coexist.** `architecture/architecture.md`
   (v2.4.0, 2026-08-20), `architecture/gaia-architecture-v3-0-rewrite-proposal.md`
   (a *proposal* for v3.0), and now `architecture/architecture-v3.0.md` +
   `foundation/v3.0-foundation.md` (*both* stating v3.0 as "Definitief"). The
   proposal was never withdrawn and v2.4.0 was never replaced. Everything that
   names intentIQ or reasonIQ is downstream of this.
2. `architecture/architecture-v3.0.md` vs. `foundation/v3.0-foundation.md` —
   the same v3.0 architecture documented twice, with no statement of which is
   canonical or what the difference in length and framing means.
3. `vision.md` ("desktop-first") vs. `architecture.md` / `operations.md` /
   `index.md` (Gaia runs in Gaia Cloud). A direct, unresolved contradiction on
   the most fundamental fact about Gaia. The new v3.0 documents do not address
   it.
4. `architecture.md` vs. `universal-foundation-comprehensive-design-architecture.md`
   — two vocabularies for one system, no mapping. `v3.0-foundation.md` now
   *declares* the Universal Foundation superseded, which is a decision nobody
   appears to have taken explicitly.
5. `foundation/roadmap.md` and `development/coding-standards.md` (both 2026-06-01)
   describe a pre-`gaia-api` product shape.
6. `foundation/soul.md` — explicitly defers to a file in a code repository that
   is not in this repository. Needs a decision on whether the constitution
   belongs here at all. The v3.0 documents describe SOUL again, differently
   (a "kleine, constante constitutionele laag").
7. `foundation/index.md` — broken links and a stale document list; needs to be
   regenerated against the new tree, or removed as redundant with `inventory.md`.
8. Empty `architecture/components/`, `flows/`, `contracts/` — the documentation
   has no component-level, flow-level or contract-level documents at all. This
   is the largest structural gap, and the v3.0 documents make it more visible:
   they name seven components and a memory pipe with no document for either.
9. `.sources.yaml` — corrupted; the Hermes repository entry is unusable, and its
   repository list now contradicts the 5-repository structure in the v3.0 docs.
10. **The foundation needs recalibration, but not uniformly.** `recalibration-matrix.md`
   separates the documents that *contradict* v3.0 (`vision.md`, `lexicon.md`)
   from the ones that are merely *old* (`principles.md`, `personality.md`,
   `design-language.md`, `ui-principles.md`) and are in fact *confirmed* by it.
   Recalibrating the second group would be a needless risk. `lexicon.md` is the
   one case that cannot wait: it is a build input, and it has no v3.0 vocabulary
   at all (Q-015).
