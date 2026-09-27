# Gaia — Open Questions

> Unresolved questions discovered while inventorying the documentation.
>
> These are **not** answered here. Each records conflicting or missing
> evidence found in the documents themselves. Answering any of them requires
> evidence from the Gaia code repositories, not from the documentation.
>
> Format follows `rules.md` section 7: ID, question, evidence, why it matters,
> whether a decision is required.

---

## Q-001 — Where does Gaia actually run?

**Status:** Open

**Question:**
Is Gaia a desktop-first system, or a cloud-hosted system that desktop is a
client of? These are not the same architecture and they cannot both be current.

**Evidence:**
- `foundation/vision.md` section 1: "Gaia is a **desktop-first, lifelong personal
  intelligence**."
- `foundation/vision.md` section 1, immediately after: "SOUL, Logos, Hindsight,
  and Capabilities all run in **Gaia Cloud** — that is where Gaia herself lives.
  Gaia Desktop … is a representation of Gaia, reached over a secure API — never
  a second place where she runs."
- `architecture/architecture.md`: "Gaia herself lives in **Gaia Cloud**. Gaia
  Desktop — and every future client — is a **representation of Gaia, not an
  instance of her**."
- `operations/operations.md`: `gaia-api` deployed to a VPS at a Tailscale
  address; `services/cognition` and `proxy/` likewise.
- `foundation/index.md`: "Gaia … Runs in Gaia Cloud."
- `development/split-plan.md`: Desktop already talks only to `services/gaia-api`;
  Web still calls Hermes, Hindsight and SOUL directly.

**Why it matters:**
This is the most fundamental fact about Gaia. It determines system boundaries,
where identity and memory live, what "the client" means, and every deployment
and documentation statement downstream. "Desktop-first" as a *product posture*
and "cloud-hosted" as a *deployment topology* can coexist — but no document
says so, and the phrase as written in `vision.md` does not distinguish them.

**Requires decision:** Yes

---

## Q-002 — Does the architecture v3.0 rewrite supersede v2.4.0?

**Status:** Open

**Question:**
Is `architecture/architecture.md` (v2.4.0) still the authoritative architecture,
or has the v3.0 rewrite superseded it? If it has, what is the status of the
documents that describe v2.4.0 as current?

**Evidence:**
- `architecture/gaia-architecture-v3-0-rewrite-proposal.md`: "**Status:
  voorstel.** Dit document herschrijft `docs/architecture.md` (v2.4.0) voor de
  besluiten van september 2026."
- Its changelog removes seven things from v2.4.0, including "intentIQ/reasonIQ
  geschrapt als Logos-sublagen", and introduces "Gaia-harnas" and sub-agents.
- `architecture/architecture-v3.0.md`: "**Status:** Definitief (September 2026)"
  and "In september 2026 zijn de voorheen gescheiden subsystemen **IntentIQ** …
  en **ReasonIQ** … definitief gepensioneerd als losse softwarelagen."
- `foundation/v3.0-foundation.md`: "**Status:** Definitief (Herziening September
  2026)" and the same retirement of IntentIQ/ReasonIQ.
- `architecture/architecture.md` front-matter still reads
  `version: 2.4.0, status: foundation`.
- `foundation/index.md` lists `architecture.md` as a current foundation document
  with no mention of v3.0.
- `architecture/evolution.md` describes intentIQ and reasonIQ as real,
  implemented components in `gaia-api`, with file paths, as of Milestone 2 and
  later.
- The v3.0 documents do not reference, replace, or mark
  `architecture/architecture.md` as superseded, and do not reference the
  proposal that preceded them.

**Why it matters:**
Every other document in the repository names intentIQ, reasonIQ, Logos,
Hindsight and Hermes. There are now **three** mutually incompatible statements
of the same architectural generation: v2.4.0 (present tense, still current), a
proposal (v3.0 as a suggestion), and two documents (v3.0 as final). Nothing in
the repository says which generation a reader should believe. The word
"definitief" in a document is not the same as a decision having been taken.

**Requires decision:** Yes

---

## Q-003 — Are there two architectures, or one architecture with two vocabularies?

**Status:** Open

**Question:**
What is the relationship between Gaia's architecture and the Universal
Foundation / Creative Companion architecture? Are they the same system with
different names, two systems, or one a historical precursor of the other?

**Evidence:**
- `architecture/architecture.md` uses SOUL / Logos / Hindsight / Capabilities /
  Gaia Desktop.
- `architecture/universal-foundation-comprehensive-design-architecture.md`
  (347 KB) uses Vertaaldomein / Hypothesedomein / Vertrouwensdomein /
  Continuitydomein / Context Base / ITM.
- `architecture/gaia-master-foundation.pdf`: "This document consolidates the
  core philosophy, architecture, and design principles of Gaia … merging the
  initial Gaia product foundation with the broader concepts from the Universal
  Foundation (Creative Companion) framework."
- No document in the repository provides a mapping between the two vocabularies.
- `foundation/chronicle-manifest-v6-core-manifest.md` section 2 performs exactly
  this kind of rename for its *own* lineage (Creatieve Companion to Chronicle)
  and explicitly flags that the rename is not automatically applied to technical
  component names.
- `foundation/v3.0-foundation.md` now declares "**Vervangt:** Universal
  Foundation / Gaia Master Foundation (v2.x)" — an explicit supersession claim
  covering two of the documents in this very cluster.
- `architecture/architecture-v3.0.md` and `foundation/v3.0-foundation.md`
  introduce a third framing — the **harnas** ("Gaia is not the LLM; the LLM is
  an inwisselbaar inferentie-orgaan") — which neither `architecture.md` nor the
  Universal document uses, and which is not reconciled with the SOUL / Logos /
  Hindsight / Capabilities layering of `architecture.md` v2.4.0.

**Why it matters:**
Without a mapping, "the architecture" is ambiguous. A reader of
`architecture.md` and a reader of the Universal document will not recognise the
same system — and now a reader of the v3.0 documents will recognise a third
framing. The v3.0 documents appear to resolve this question by supersession, but
they do not say what replaces the v2.4.0 vocabulary, and v2.4.0 is still
present-tense in the repository.

**Requires decision:** Yes

---

## Q-004 — Is the canonical SOUL in this repository or in the code?

**Status:** Open

**Question:**
Where does Gaia's constitution of record live, and who is authoritative when
`foundation/soul.md` and `services/gaia-api/identity/soul.md` disagree?

**Evidence:**
- `foundation/soul.md`: "The canonical SOUL document lives in Gaia Cloud, owned
  by the Gaia API: `services/gaia-api/identity/soul.md` … This is the single
  source of truth."
- Same document: "`docs/soul.md` (this file) is the architectural overview …
  The actual constitution is the canonical file in the identity layer."
- `architecture/architecture.md` claims `status: foundation` and is described by
  `foundation/index.md` as authoritative.
- `architecture/evolution.md` Milestone 2 describes a "Canonical Foundation
  Engine" that loads `soul.md`, `principles.md`, `lexicon.md`,
  `architecture.md` and `evolution.md` from `docs/` into `artifact.json` — so
  these documentation files are also a **build input** for the web and desktop
  clients, not only prose.
- `development/split-plan.md` confirms: "Both web and desktop builds depend on
  `docs/` being present."
- `architecture/architecture-v3.0.md` and `foundation/v3.0-foundation.md` describe
  SOUL again, differently: "a small, constant constitutional layer" defining
  "stem, waarden en kalme, eerlijke stem", learning happening only in "Logos
  Memory" so that "kennis" and "hypothesen" change while character does not.

**Why it matters:**
These documents are simultaneously (a) architecture documentation, (b) the
system prompt source for a shipping product, and (c) partly superseded by a
file in another repository. Editing them has runtime consequences, not just
documentation consequences. This is not recorded anywhere in the repository. The
v3.0 documents add a third description of what SOUL is and where it lives.

**Requires decision:** Yes

---

## Q-005 — Is there a Gaia Cloud API, and what is its contract?

**Status:** Open

**Question:**
What is the authoritative interface between clients and Gaia Cloud, and is
`conversation/turn` the whole of it?

**Evidence:**
- `development/split-plan.md` section 1: "**Key structural finding: there is no
  uniform Gaia Cloud API yet.** 'Gaia Cloud' is today a deployment topology …
  that clients reach **directly** through three same-origin proxy paths.
  `nginx.conf` is the only shared API surface. The client orchestrates
  reasoning itself."
- `development/split-plan.md` section 1 also marks `services/gaia-api/` and
  `desktop/` as "(superseded, see Milestone 9 addendum below)" — superseded by
  *what* is not stated in the table.
- `architecture/architecture.md` presents clients reaching Gaia "over the Gaia
  API" as settled.
- `development/web-migration-plan.md`: "Desktop already talks only to
  `services/gaia-api` … Web still does all three directly", and "Desktop's
  `conversation/turn` contract is **deliberately smaller** than what Web's turn
  lifecycle actually does today."
- `architecture/decisions/conversational-guidance-architecture-decision.md`
  describes `turn.js` assembling the system prompt on the server side.
- `architecture/components/`, `architecture/contracts/` and `architecture/flows/`
  are **empty**.
- `architecture/architecture-v3.0.md` section 1 introduces a different live turn
  path — "Gebruiker -> Gaia -> LLM -> Response … zonder synchrone
  redeneervertraging" — with all cognition moved asynchronously to "prompt- en
  achtergrondniveau". This is a third description of what happens during a turn,
  and it does not mention `conversation/turn`, `turn.js`, or `gaia-api` at all.

**Why it matters:**
Clients, deployment, and the whole Web migration depend on this contract. No
document in the repository states it. The architecture has no contract-level
documentation at all — and the newest, self-declared definitive architecture
document describes a turn flow in terms that do not reference any of the
contracts the other documents depend on.

**Requires decision:** Yes

---

## Q-006 — Should `architecture.md` be decomposed into components, flows and contracts?

**Status:** Open

**Question:**
`architecture.md` is 61 KB and covers boundaries, topology, flows, storage
abstraction, model agnosticism and more. Should it be split into the
`components/`, `flows/` and `contracts/` structure, or kept whole?

**Evidence:**
- The three target subdirectories exist and are empty.
- `architecture.md` mixes all three concerns in one document, and the
  `foundation/index.md` summary reflects that mixture.
- No document in the repository addresses a single component in isolation, and
  no document describes an interface contract on its own.
- The v3.0 documents make the gap sharper: they name seven components
  (Gaia-Cloud, Foundation, capture-rs, gaia-desktop, gaia-web, plus the Logos
  dimensions and sub-agents) and describe a three-stage memory pipe, without any
  document existing for any of them.

**Why it matters:**
Determines whether the architecture section is navigable or a single monolith.
Note: this is an organizational question, deliberately **not** acted on in
Phase 1.

**Requires decision:** Yes

---

## Q-007 — What is the ownership and purpose of the lyrics style document?

**Status:** Open

**Question:**
What is `development/lyric-style-preference-alternative-indie.pdf` for, and
which component owns it?

**Evidence:**
- The file was named `gpt.pdf` — a name that conveys nothing.
- Content: a single-page set of instructions for writing alternative/indie rock
  lyrics (no all-caps, avoid "epic"/"explosive", prefer restrained delivery,
  list of preferred and avoided vocal-direction terms).
- No other document in the repository mentions lyrics, Melodiq, or
  SongCompanion as owning this.
- `foundation/vision.md` lists Melodiq (music) and SongCompanion (song work) as
  capabilities.
- `foundation/v3.0-foundation.md` section 5 names Hermes, Melodiq and
  SongCompanion as the sub-agents subject to the HADES rule — making them a
  named architectural concern for the first time in a document marked definitive.
- `foundation/chronicle-philosophy-maker-facts-google-gemini.pdf` shows a
  separate lyrics/music context existed alongside Chronicle.

**Why it matters:**
It is either a prompt fragment for a shipped capability, a personal working
note, or a leftover. Its location in `development/` is provisional.

**Requires decision:** Yes

---

## Q-008 — Which repositories are the actual sources of truth?

**Status:** Open

**Question:**
`.sources.yaml` lists seven repositories. Which of them currently exist, which
is authoritative for which concern, and what is the relationship between
`Gaia-Cloud`, `Gaia-Web`, `Foundation` and `Chronicle-Gaia` today?

**Evidence:**
- `.sources.yaml` lists: Gaia -> `Bojanni050/Gaia-Cloud`, Foundation-Chronicle
  -> `Bojanni050/Foundation`, Chronicle-Gaia -> `Bojanni050/Chronicle-Gaia`,
  Gaia-Web -> `Bojanni050/gaia-web`, Gaia Website, Chronicle-Diary, Hermes.
- The Hermes entry URL is corrupted: `.../hermes-agentvvvvvvvvvvvvvvvvvv...`, and
  the YAML structure of that entry is malformed.
- `development/split-plan.md` describes a monorepo being split into
  Gaia-Cloud / Gaia-Web / Gaia-Desktop with "Phase 0 and Phase 1's repo cut
  complete — VPS cutover still pending".
- Documents reference `services/gaia-api/`, `services/cognition/`, `proxy/`,
  `frontend/`, `desktop/`, `src-tauri/`, `foundation/` — paths that belong to
  the monorepo, not obviously to any single listed repository.
- `architecture/gaia-architecture-v3-0-rewrite-proposal.md` section 4.5 states
  Hermes is "an external product (Nous Research)" — while other documents treat
  Hermes as a Gaia component.
- **A different answer again:** `architecture/architecture-v3.0.md` section 4 and
  `foundation/v3.0-foundation.md` section 4 both specify **five** repositories:
  `Gaia-Cloud` (the harnas: SOUL, Logos, `gaia-api`, orchestration),
  `Foundation` (Chronicle storage, epistemic rules, `mcpServer.js`),
  `capture-rs` (local capture in Rust), `gaia-desktop` (Tauri + React), and
  `gaia-web` (higaia.nl).
- That five-repository list matches `.sources.yaml` better than
  `development/split-plan.md` does — `Foundation` and Chronicle appear in both —
  but `capture-rs` appears in neither `.sources.yaml` nor `split-plan.md`.

**Why it matters:**
`rules.md` section 1 makes current implementation the top of the evidence
hierarchy. Without knowing which repository is which, that hierarchy cannot be
applied. The repository count is now stated three different ways in this
repository (3 / 5 / 7), and `split-plan.md` — the document with the most
implementation detail — is the one that disagrees with the newest,
most definitive-sounding documents.

**Requires decision:** Yes

---

## Q-009 — Is Hermes a Gaia component or an external dependency?

**Status:** Open

**Question:**
Is Hermes something Gaia builds, vendors, forks, or consumes from a third party?

**Evidence:**
- `foundation/vision.md`: Capabilities include "Hermes (reasoning) … optional
  instruments Gaia reaches for when they serve her goals".
- `architecture/architecture.md` framing: "Logos is Gaia's cognitive reasoning
  layer"; Hermes appears in the Capabilities row of the core model.
- `architecture/gaia-master-foundation.pdf` Pillar 2: "Hermes is the abstract,
  replaceable thinking layer … can be swapped (e.g., to GPT, Claude, or future
  models)".
- `architecture/evolution.md` Milestone 0: Hermes was a "dev-stub" — "a FastAPI
  service" built in-repo; Milestone 2: `HermesProvider` talks to "a local
  OpenAI-compatible `/v1/chat/completions` endpoint".
- `architecture/evolution.md` Milestone 0 lesson: "'Hermes' was used to mean
  three different things at once: a local API, a backend service, and a brand.
  That ambiguity cost clarity."
- `architecture/gaia-architecture-v3-0-rewrite-proposal.md` section 4.5: "Hermes
  is geen eigen bouwsel" (not our own construction); external product, Nous
  Research.
- `operations/operations.md`: `proxy/` fronts `hermes-agent`, "injecting its auth
  token so no client ever sees it".
- `.sources.yaml` lists a Hermes repository.
- `architecture/architecture-v3.0.md` section 5 calls Hermes a sub-agent for
  "executie en communicatie op meerdere apparaten" — a fourth characterisation,
  neither internal reasoning layer nor external product, but a delegated
  multi-device actor.

**Why it matters:**
Determines component ownership, the provider abstraction boundary, and whether
Gaia Cloud or a third party runs the reasoning layer. The ambiguity was already
recognised in Milestone 0 and has not been closed; the v3.0 documents add a
fourth description rather than resolving it.

**Requires decision:** Yes

---

## Q-010 — What is the status of the pre-2026-08 documents?

**Status:** Open

**Question:**
`foundation/roadmap.md`, `foundation/design-language.md`,
`foundation/ui-principles.md` and `development/coding-standards.md` all carry
`last_updated: 2026-06-01` and describe a product shape that predates
`services/gaia-api/`. Are they still current?

**Evidence:**
- `development/coding-standards.md` section 2 describes a representative structure
  rooted at `/gaia-desktop` with `frontend/`-era client-side `hermes`,
  `hindsight` and `chronicles` clients, and "the ONLY reasoning surface".
- `development/split-plan.md` and `architecture/evolution.md` describe a world
  where `gaia-api` owns the turn and `frontend/` is being superseded by
  `desktop/` and `gaia-web/`.
- `operations/operations.md` last updated 2026-08-20, describes the deployed
  reality.
- `architecture/architecture.md` last updated 2026-08-20, describes a cloud
  topology that the June documents do not mention at all.
- The v3.0 documents (September 2026) do not reference any of these four
  documents, and do not state whether they survive.

**Why it matters:**
Roughly half the foundation documents predate the current architecture by two to
three months. Their status cannot be established from the documentation alone.

**Requires decision:** Yes

---

## Q-011 — Is the epistemic-trust layer (`services/cognition`) part of Gaia, and where does it belong?

**Status:** Open

**Question:**
`hypotheses` live in `services/cognition/` (Postgres, Tailscale-only, port 8890),
mirrored into `gaia-api`'s `reasonModels.js`. Is this a Gaia component, a
transitional shim, or a separate service with a defined contract? And does it
survive v3.0?

**Evidence:**
- `architecture/decisions/chronicle-hindsight-considerations.md` places
  epistemic status enforcement on an "adapterlaag" at the Hindsight to Logos
  boundary, and calls it "de eerste bouwtaak" (the first build task).
- `development/epistemic-inspector-plan.md` tabulates what actually exists:
  hypotheses in `services/cognition/` with its own migration
  `002_create_hypotheses.sql`; routes `/v1/banks/:bank_id/hypotheses`,
  `/:id/confirm`, `/:id/reject`; a mirror of the hypothesis model in Logos; a
  per-day JSONL decision log in `gaia-api`.
- `operations/operations.md` lists `services/cognition` as deployed on the VPS.
- `architecture/architecture.md` does not mention `cognition` in the core model.
- Two schemas now describe the same concept: a Postgres one and one in
  `gaia-api`.
- The v3.0 documents define four epistemic statuses
  (`observation` / `interpretation`+`hypothesis` / `confirmed` / `rejected`) and
  an "Absolute Override" rule, and place the enforcing rules in the `Foundation`
  repository alongside Chronicle storage and `mcpServer.js` — but neither names
  `services/cognition`, `cognition`, or the adapter layer as the implementation.

**Why it matters:**
Two stores of the same concept, in two services, with no documented contract
between them, and an architectural decision that says the enforcing layer has
not been built yet. The v3.0 documents restate the *rule* while silently
relocating the *owner* to a repository (`Foundation`) that no other document
mentions as holding this responsibility.

**Requires decision:** Yes

---

## Q-012 — Should `foundation/index.md` exist at all?

**Status:** Open

**Question:**
`foundation/index.md` was the repository `README.md` and describes a
nine-document foundation set that lived at the root. Now that the tree is
organised by purpose, does an index belong here, or is `inventory.md` its
replacement?

**Evidence:**
- It links to `./vision.md`, `./architecture.md`, `./design-language.md` etc.
  as siblings; eight of those nine paths no longer exist relative to it.
- It also contains substantive claims of its own — the structure summary and a
  "Resolved Open Questions" list with six stances — which duplicate
  `architecture.md` and `vision.md`.
- It describes `operations.md` as "*(not a foundation document — a living
  reference)*", a distinction that the new tree now makes structurally.
- `inventory.md` now covers the whole repository.
- Its document list is now also factually incomplete: it omits
  `architecture/architecture-v3.0.md` and `foundation/v3.0-foundation.md` entirely,
  the two documents that claim to be definitive.

**Why it matters:**
It is currently a broken-link document asserting authority it may not have, and
it does not mention the newest architecture. Left untouched in Phase 1 per the
no-rewrite rule.

**Requires decision:** Yes

---

## Q-013 — When did the live turn path lose its synchronous reasoning?

**Status:** Open

**Question:**
Did the live conversation turn actually change from a synchronous
intent-classify-then-reason pipeline to a direct "User -> Gaia -> LLM ->
Response" path with cognition moved asynchronously? If so, when, and does the
code still do the former?

**Evidence:**
- `architecture/architecture-v3.0.md` section 1: "**Het Conversatie- vs.
  Reflectiemaxime:** 'De conversatie is de ervaring. Logos is de reflectie op die
  ervaring.' Live gespreksbeurten verlopen direct (Gebruiker -> Gaia -> LLM ->
  Response) zonder synchrone redeneervertraging."
- `foundation/v3.0-foundation.md` section 1 states the same maxim and path, under
  the heading "Eenvoudig Live Gesprekspad".
- `architecture/gaia-architecture-v3-0-rewrite-proposal.md` changelog item 1:
  "intentIQ/reasonIQ geschrapt als Logos-sublagen (diagrammen, 1.3, 4.2, 5.1,
  Cognitive Loop) — Subverdeling is prompt-taal, geen systeemgrens."
- But `architecture/architecture.md` v2.4.0 still documents a Cognitive Loop
  with intentIQ and reasonIQ as distinct stages, and still carries
  `status: foundation`.
- `architecture/decisions/conversational-guidance-architecture-decision.md`
  documents a *recent, implemented* change in which `turn.js` still injects
  `intentIQ`/`reasonIQ`-derived context into the system prompt, and states
  "Kept — `intentIQ.js`'s actual intent taxonomy and routing … `reasonIQ.js`'s
  evidence/hypothesis reasoning".
- `development/split-plan.md` states Logos "runs client-side — explicitly
  flagged as interim".
- The v3.0 documents use "Logos Memory" without ever defining which component
  owns it or where it is stored.

**Why it matters:**
This is a behavioural change to the hottest path in the product, asserted as
already-decided in the two newest documents, and contradicted by an
implementation record that describes the opposite as recently-shipped. The phrase
"prompt-taal, geen systeemgrens" implies the components were never real system
boundaries — which retroactively changes how `evolution.md` and
`conversational-guidance-architecture-decision.md` should be read, since both
describe them as components in files.

**Requires decision:** Yes

---

## Q-014 — Which of the two v3.0 documents is canonical?

**Status:** Open

**Question:**
`architecture/architecture-v3.0.md` and `foundation/v3.0-foundation.md` document
the same v3.0 architecture in the same six sections, both marked "Definitief".
Which one governs, and are the differences between them intentional?

**Evidence:**
- Both files were added within seven minutes of each other (2026-09-25 20:48 and
  20:55) and cover the same six sections with near-identical prose.
- Differences that are not merely length:
  - `architecture-v3.0.md` section 1 names two architectural principles
    ("Scheiding van Identiteit en Inferentie" and the conversation/reflection
    maxim); `v3.0-foundation.md` section 1 states the same two ideas as bullet
    points without naming them as principles.
  - Section 4 of both gives the same five repositories with slightly different
    one-line descriptions (e.g. `capture-rs`: "Lokale scherm- en
    activiteitenopname in Rust op de client" vs. the same plus "(ruisfilter op de
    client)").
  - `architecture-v3.0.md` section 2 names the third Logos dimension
    "**DecisionIQ** (Besluitvorming & Uitkomsten)"; `v3.0-foundation.md` section 2
    calls the same dimension "(DecisionIQ)" without that name.
  - `architecture-v3.0.md` section 5 restricts HADES to Hermes;
    `v3.0-foundation.md` section 5 names Hermes, Melodiq and SongCompanion as
    the sub-agents.
  - `architecture-v3.0.md` section 3 adds "schermopnames, audiodagboeken" as
    `observation` types; `v3.0-foundation.md` section 3 says "chats, notities en
    opnames".
  - Only `v3.0-foundation.md` carries a "**Vervangt:**" supersession claim.
- Neither file has front-matter, a version number, an owner, or a
  `last_updated` field — unlike every other foundation document in the
  repository, which all carry them.

**Why it matters:**
Two documents both marked definitive, describing overlapping but not identical
systems, is precisely the ambiguity the reorganization was meant to surface. A
reader cannot tell whether the differences are editorial, intentional scope
differences, or drift. Note that `foundation/v3.0-foundation.md` makes the
supersession claim and `architecture/architecture-v3.0.md` does not — so the
choice of which to trust also determines whether the Universal Foundation and
Gaia Master Foundation are retired.

**Requires decision:** Yes

---

## Q-015 — Is the v3.0 vocabulary in the system prompt, and if not, why is it missing?

**Status:** Open

**Question:**
Is `foundation/lexicon.md` still loaded as a system-prompt input, and does it
contain the v3.0 vocabulary? If not: how does Gaia name `harnas`, `DecisionIQ`,
`HADES`, `Absolute Override` and the four epistemic statuses?

**Evidence:**
- `architecture/evolution.md` Milestone 2: the Canonical Foundation Engine
  loads `soul.md`, `principles.md`, `lexicon.md`, `architecture.md` and
  `evolution.md` into `artifact.json` (the system prompt dictionary).
- `development/split-plan.md`: "Both web and desktop builds depend on `docs/`
  being present."
- `foundation/lexicon.md` lists two registers. Its Technical Language list
  contains Hermes, Hindsight, Chronicles, PostgreSQL, MCP, API, JSON, Provider,
  Streaming, Embeddings, Inference, Reasoning Provider, Vector Database. It
  contains **none** of: harnas, Logos, DecisionIQ, HADES, Absolute Override,
  `observation`, `interpretation`, `confirmed`, `rejected`.
- Neither `architecture/architecture-v3.0.md` nor `foundation/v3.0-foundation.md`
  mentions `lexicon.md`, the Foundation Engine, or `artifact.json` at all.

**Why it matters:**
This is the only finding in the recalibration that is not merely a documentation
lag. `lexicon.md` is described by the repository's own documents as a *build
input* for the running product, not as prose. If it is still loaded, Gaia has no
words for the core of her own epistemology — the statuses that distinguish what
is registered from what is believed are exactly what she would need to explain
to a user who asks "why do you think that?" (a question `ui-principles.md` §9
promises she can answer). If it is no longer loaded, `lexicon.md` is an
emptied-out document and that needs deciding too. Either answer requires a
decision; postponing the question is itself a decision.

**Requires decision:** Yes

---

## Q-016 — "V3": roadmap phase or architecture generation?

**Status:** Open

**Question:**
When someone writes "V3" in this repository, do they mean the maturity phase
from `roadmap.md` or the architecture generation v3.0? And does the same
ambiguity apply to the numbering of future architecture revisions?

**Evidence:**
- `foundation/roadmap.md` section 1: "| **V3** | Proactive & Multi-Surface |
  Mature (proactive, trusted) | Welcome initiative + Gaia extends beyond
  desktop |"
- `architecture/architecture-v3.0.md` and `foundation/v3.0-foundation.md` use
  v3.0 as an architecture version, both marked "Definitief (September 2026)".
- Both now live in `foundation/`, one directory apart.
- `foundation/roadmap.md` dates from 2026-06-01 and knows of no v3.0
  architecture.
- `architecture.md` v2.4.0 also uses a version number (2.4.0) — so the
  repository currently numbers two different things.

**Why it matters:**
This is not cosmetic. A reader who sees "V3" builds the wrong expectation about
what ships when. The risk compounds as soon as a roadmap V4 or an architecture
v4.0 appears. One of the two numberings needs to change, and that is a naming
decision, not a content change — so it is safe to make early, unlike the other
questions in this file.

**Requires decision:** Yes
