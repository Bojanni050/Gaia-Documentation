---
title: Gaia — Operations
document: operations
version: 1.0.0
status: active
last_updated: 2026-08-20
owner: Gaia Product Foundation
framing: "Gaia is a lifelong personal intelligence designed to grow through understanding."
---

# Gaia — Operations

> Where to actually go to look at, or change, how Gaia Cloud is running right now. Not architecture, not philosophy — just the practical "where is it" reference for operating this deployment.

---

## Admin Interface

`gaia-api` serves an operator-only admin page, separate from Gaia Desktop's own Settings panel.

**URL:** `http://100.65.0.15:8891/admin` (Tailscale-only — you must be on the tailnet to reach it)

**Auth:** same Bearer token as every other authenticated `gaia-api` route (one of the configured `GAIA_API_TOKEN` values).

**What's there** (`services/gaia-api/src/adminRoutes.js`, static page at `services/gaia-api/public/admin.html`):

- **Logos decision log** — `GET /admin/api/logos/decisions`: the durable, browsable log of every Logos reflection (what it concluded) and IntentIQ decision (what it classified).
- **LLM call log** — every actual model call from IntentIQ, Logos background reflection, and Gaia's native voice generator is logged and viewable here — the place to look when something Gaia said or decided needs tracing back to the actual model call behind it.
- **Provider Settings** — `GET`/`PUT /admin/api/provider/config`, `.../roles`, `.../capabilities`, `.../models` — the unified model-provider config and per-role (generation/reasoning/vision/kairos/aion) model selection. Logos reflection uses the `reasoning` role; image OCR uses the `vision` role; the Kairos episode synthesizer uses the `kairos` role; Aion (Gaia's own-memory pass) uses the `aion` role and borrows `reasoning` when unset. Every role card shows, top-right, the model and provider it currently resolves to (from `resolved` in the config response), and has a **Test connection** button (`POST /admin/api/provider/role-test`) that sends one minimal chat call to prove the model answers.
- **TTS config** — `GET`/`PUT /admin/api/tts/config`, `GET /admin/api/tts/models`.

None of this is part of any client's contract (Desktop, Web) — it's Gaia Cloud operator tooling only.

---

## Kairos worker

The Kairos episode synthesizer (`services/gaia-api/src/kairos/`) turns raw Foundation observations into narrative episodes, stored in `services/cognition` (`kairos_episodes`) and streamed to clients (`/kairos/episodes/stream`). It is **off by default** and needs two things before it produces anything:

1. **`GAIA_KAIROS_ENABLED=true`** in `services/gaia-api`'s `.env` — starts the poll loop (`GAIA_KAIROS_INTERVAL_MS`, default 30000ms).
2. **A Kairos model** — selected in `/admin` on the **Kairos** role card, or via `KAIROS_MODEL_BASE_URL` + `KAIROS_MODEL_NAME` as an env fallback.

With the worker off, or on but without a model, both clients' Kairos surfaces show an honest empty state ("no episode recognised yet") — nothing crashes and no turn is affected. Each poll costs one cheap LLM call per closed cluster; clustering itself is deterministic and spends zero tokens. Watch it via the **LLM call log** on `/admin` (`purpose: kairos.synthesis`).

---

## Cheap model per role

The live choice is the per-role selection in `/admin` → Provider Settings; the
`GAIA_NATIVE_*`/`REASONIQ_MODEL_*`/`KAIROS_MODEL_*`/`AION_MODEL_*` env vars are
only a fallback. **Every role except Voice can also carry its own provider**
(Use Main Provider / OpenAI / EdenAI / Anthropic / Mistral / Custom) — so you can
put reasoning on EdenAI/DeepSeek while vision stays on Gemini or Mistral, without
one Main Provider having to serve both. For a low-cost setup, these **EdenAI** ids
are verified against its public catalog (`GET https://api.edenai.run/v3/models`,
Oct 2026 — the same endpoint `/admin`'s "Retrieve models" reads, so all of them
appear in the dropdown). USD per 1M tokens in/out:

| Role | EdenAI id | $ in/out | notes |
|---|---|---|---|
| generation | `anthropic/claude-haiku-5-5` | 0.10 / 0.50 | Gaia's voice; vision + tool calling |
| generation | `google/gemini-3.1-flash-lite` | 0.25 / 1.50 | vision + tool calling |
| reasoning | `deepinfra/deepseek-ai/DeepSeek-V4-Flash` | 0.09 / 0.18 | tools + reasoning |
| reasoning | `deepinfra/deepseek-ai/DeepSeek-V3.2` | 0.26 / 0.38 | tools + reasoning |
| vision | `deepinfra/mistralai/Mistral-Small-3.2-24B-Instruct-2506` | 0.075 / 0.20 | multimodal |
| vision | `google/gemini-3.1-flash-lite` | 0.25 / 1.50 | multimodal |
| kairos | `google/gemini-2.5-flash-lite` | 0.10 / 0.40 | JSON, NL-capable |
| kairos | `anthropic/claude-haiku-5-5` | 0.10 / 0.50 | JSON, NL-capable |
| aion | `mistral/ministral-3b-2512` | 0.10 / 0.10 | mostly empty replies |
| aion | `openai/gpt-5-nano` | 0.05 / 0.40 | mostly empty replies |
| intent (offline/eval) | `openai/gpt-5-nano` | 0.05 / 0.40 | classifier |
| backup | `deepinfra/deepseek-ai/DeepSeek-V4-Flash` | 0.09 / 0.18 | keep on another host than the primary |

Two traps, both from `providerConfigResolver.js`: **vision and aion fall back to
the `reasoning` role when unset**, so pointing reasoning at a text-only model
(DeepSeek V4 Flash is text-only) silently breaks OCR — set `vision` explicitly.
And `google/gemini-3.1-flash-lite-image` has **no** tool calling; use the bare
`google/gemini-3.1-flash-lite` for generation. Prefer bare ids: the `@eu`/`@us`
regional variants (Azure/Bedrock/Vertex) are ~10% pricier.

`generation` must support tool calling (the `memoryTool` remember/keep actions);
`reasoning`, `kairos` and `aion` need reliable JSON output.

---

## Hindsight — model configuration

Hindsight is the external memory provider (`vectorize-io/hindsight`), self-hosted
on the VPS as the `hindsight` container (`:8888` API, `:9999` dashboard). It is
**not** part of this repo and is not configurable from CommandCenter
(`configurable: false` in the registry); its config lives in `/opt/hindsight/.env`
on the VPS, untracked.

It has **three** model slots, each configured separately:

| Slot | Env prefix | Default | Notes |
|---|---|---|---|
| **LLM** | `HINDSIGHT_API_LLM_*` | `gpt-5-mini` | fact extraction, reflect, consolidation, mental-model refresh |
| **Embedding** | `HINDSIGHT_API_EMBEDDINGS_*` | `BAAI/bge-small-en-v1.5` (local) | semantic search |
| **Reranker** | `HINDSIGHT_API_RERANKER_*` | `cross-encoder/ms-marco-MiniLM-L-6-v2` (local) | reorders recall results |

The deployment runs the **full image**, which bundles the local embedding and
reranker models — those two cost **$0** and need no config. The only model cost
is the **LLM**, and it can be split per operation (each set has its own
`PROVIDER`/`API_KEY`/`MODEL`/`BASE_URL`, falling back to the global
`HINDSIGHT_API_LLM_*`):

| Operation | What it does | Cheapest suitable | Env |
|---|---|---|---|
| **retain** | extract facts/entities (strict JSON) | `deepseek-v4-flash`, `gemini-3.1-flash-lite`, `gpt-5-mini` | `HINDSIGHT_API_RETAIN_LLM_MODEL` |
| **reflect** | user-facing reasoning + answer (tool loop) | `deepseek-chat` (non-thinking), `gemini-2.5-flash-lite` | `HINDSIGHT_API_REFLECT_LLM_MODEL` |
| **consolidation** | merge observations (background) | `ministral-3b`, `gpt-5-nano` | `HINDSIGHT_API_CONSOLIDATION_LLM_MODEL` |
| **mental-model refresh** | background refresh | `ministral-3b`, or a **local** `ollama` model | `HINDSIGHT_API_MENTAL_MODEL_REFRESH_LLM_MODEL` |

Hindsight's own guidance: retain on a model with strong structured output,
reflect on something faster/cheaper, and the background refresh on a no-think or
local model "so the background job cannot destabilise interactive reflect".

Concrete `/opt/hindsight/.env` — Option A, DeepSeek native (cheapest, simplest):

```bash
# LLM (embedding + reranker stay local in the full image)
HINDSIGHT_API_LLM_PROVIDER=deepseek
HINDSIGHT_API_LLM_API_KEY=sk-...
HINDSIGHT_API_LLM_MODEL=deepseek-v4-flash          # retain: extraction, thinking mode
HINDSIGHT_API_REFLECT_LLM_MODEL=deepseek-chat      # reflect: non-thinking, cheap + fast
HINDSIGHT_API_CONSOLIDATION_LLM_MODEL=deepseek-chat
HINDSIGHT_API_MENTAL_MODEL_REFRESH_LLM_MODEL=deepseek-chat
```

Option B, via EdenAI (one key for everything; EdenAI is not a native Hindsight
provider, so it goes through the OpenAI-compatible base URL):

```bash
HINDSIGHT_API_LLM_PROVIDER=openai
HINDSIGHT_API_LLM_BASE_URL=https://api.edenai.run/v3
HINDSIGHT_API_LLM_API_KEY=<edenai key>
HINDSIGHT_API_LLM_MODEL=deepinfra/deepseek-ai/DeepSeek-V4-Flash
HINDSIGHT_API_REFLECT_LLM_MODEL=google/gemini-2.5-flash-lite
HINDSIGHT_API_CONSOLIDATION_LLM_MODEL=mistral/ministral-3b-2512
HINDSIGHT_API_MENTAL_MODEL_REFRESH_LLM_MODEL=mistral/ministral-3b-2512
```

Three cautions:

- **Switching the embedding model is not free.** Vectors from a different model
  are not comparable, so the whole bank must be re-embedded and the dimension
  changes. The local `bge-small` is the cheapest and already active — leave it
  unless there is a real reason. External options: `text-embedding-3-small`
  (~$0.02/1M) or Gemini embeddings.
- **The reranker** default is local and free; `flashrank` is a lighter CPU
  variant and `rrf` skips neural reranking entirely (cheapest). External cheapest
  is SiliconFlow `BAAI/bge-reranker-v2-m3`.
- **`retain` needs reliable structured output** — going too cheap there hurts
  extraction quality, which is the whole point of the memory. Cut cost on
  `reflect`/`consolidation`/refresh instead.

Apply on the VPS: edit `/opt/hindsight/.env`, then
`docker compose up -d` in `/opt/hindsight`.

---

## Deployment

- **Host:** VPS, reached over Tailscale only — no public SSH (closed as of 2026-08-29).
- **`gaia-api`:** `100.65.0.15:8891`, deployed automatically on push to `main` (`.github/workflows/deploy.yml`): the runner joins the tailnet via `tailscale/github-action`, SSHes to `100.65.0.15`, runs `git reset --hard origin/main` in `/root/gaia`, then `docker compose up -d --build` in `services/gaia-api`.
- **`services/cognition`:** Tailscale-only, `:8890` (Postgres-backed patterns & hypotheses).
- **`proxy/` (`gaia-hermes-proxy`):** internal nginx fronting `hermes-agent`, injecting its auth token so no client ever sees it.

See `docs/split-plan.md` for the full topology and what's still interim, and `docs/evolution.md` (Milestone 9 and later) for how `gaia-api` came to exist.

---

## How to Read This Document

This is a living reference, not a foundation document like `vision.md` or `architecture.md` — it records *where things currently run and how to reach them*, not why. Update it whenever a URL, port, or deployment mechanism actually changes; treat a stale entry here as a defect, same as any other doc.
