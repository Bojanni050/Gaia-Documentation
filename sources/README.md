# sources — raw input, not documentation

This folder holds the raw material the documentation was derived from: conversation
transcripts, research papers, and proposal PDFs. It is **input**, not documentation.

Two things matter before you read anything here.

## 1. The transcripts are AI-generated reviews, not specifications

The six files in `transcripts/` are two-voice "deep dive" conversations, exported from a
transcription model (`voxtral-mini-latest`) on 2026-10-03. They were generated *after* the
architecture documents, describing them. One voice usually defends the design, the other plays
critic.

**They describe the architecture; they do not define it.** Where a transcript disagrees with the
code or with the documents in this repository, the code and the documents win. Treat them as
critique and inspiration — useful for spotting gaps, not as a source of truth.

## 2. Known errors carried by the transcripts

These recur across the transcripts. Do not propagate them:

- **"Chronicle is the raw store."** False. The raw store is **Foundation**; Chronicle is the
  capture client that *feeds* it. See `../docs/Epistemic Memory & Execution Rules.txt` (or the copy
  in this repository's parent) — this mis-drawing is called out and corrected there explicitly, and
  it is exactly the one the transcripts repeat.
- **"A hard confidence cap at 80% / 85%."** Does not exist. The real values:
  - `0.95` — the certainty clamp (`clampConfidence()`, "soul.md: never claim certainty"), in
    `services/gaia-api/src/reasoning/hypothesisManager.js` and
    `services/cognition/src/hypotheses.js`.
  - `0.80` — a **threshold** for machine soft-promotion to `corroborated`, not a ceiling.
  The "80%"/"85%" figures in the transcripts (and in the podcast-style discussion) are described
  behaviour, not implemented constants.
- **"A soft-evidence equation `C(t+1) = C(t) + α(1−C(t))·score`."** Not implemented. Confidence is
  updated with a linear delta (`confidence + confidenceDelta`). An asymptotic accumulator is a
  *proposal* only — see the decision note in Hindsight and
  `~/.opencode/plan/soft-evidence-micro-accumulator-voorstel.md`.

## What lives here

- **`transcripts/`** — six AI-generated review conversations, 2026-10-03. Read with the caveats above.
- **`research/`** — external papers and syntheses referenced by the design
  (LLM hallucination governance, epistemic control, formal logic, first-person authority).
  Note: `horizon-merge-in-formal-logic.pdf` and `Horizontverschmelzung in Formele Logica.pdf`
  are the same paper under two names.
- **`proposals/`** — proposal PDFs that fed the V3 documents. `3 pijlers.pdf` and
  `Architectuurbesluit_ Consolidatie van Logos en Pensioen van IntentIQ & ReasonIQ.pdf` are the
  primary sources for the Logos consolidation (IntentIQ/ReasonIQ retired). `soul.pdf` and
  `gaia-v3.0-foundation.pdf` are the rendered documents, not separate material.

## Ideas worth carrying forward

Not all of what the transcripts suggest is wrong. The genuinely useful suggestions, recorded so
they are not lost:

1. **Bias inference across provider switches** — the transcripts observe that *which* hypotheses a
   model forms is already a filter of that model, before status labelling or the human override can
   act. Proposal: stamp derived records with the provider/model that formed them, and compare
   form-features (scope, presence of a counter-hypothesis, evidence balance, confidence
   distribution) across a model switch. See `~/.opencode/plan/bias-inferentie-providerwissel-voorstel.md`.

2. **Drift of the intent-translation memory** (from `Voorkom_Identiteitsverschuiving`): Logos could
   explicitly check, via Hindsight, whether interpretations **systematically deviate** after an
   architecture or provider change — not whether they are *true*, but whether the translation of
   user intent has shifted. Concrete example given: a new model fine-tuned on extreme politeness
   smooths over the user's sarcasm. This is the semantic counterpart to idea 1 (which is
   deterministic form-features only) and is deliberately **not** built: it needs an intent-reference
   baseline, is semantically expensive, and touches privacy. Recorded, not scheduled.

3. **Graded consolidation / an implicit confirmation parameter** (from the same transcript): the
   micro/macro split for the Absolute Override is already implemented (`scope` + the REQUIRED
   `rationale` for macro). The second half — an implicit confirmation threshold so low-risk
   hypotheses consolidate without a human click — is the soft-evidence proposal above. The micro/macro
   half is done; the implicit-threshold half is not.

4. **Friction must not be gamifiable** (from `Gaia_V3_Fixes_For_LLM_Hallucinations`): "if you ask a
   human to verify 50 hypotheses a day, they will rubber stamp 49 of them." The rationale requirement
   for macro exists server-side; the *non-trivial interaction* (retyping, a multiple-choice
   implication test) lives in the GaiaChat client and is out of scope for this repository.
