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

## Deployment

- **Host:** VPS, reached over Tailscale only — no public SSH (closed as of 2026-08-29).
- **`gaia-api`:** `100.65.0.15:8891`, deployed automatically on push to `main` (`.github/workflows/deploy.yml`): the runner joins the tailnet via `tailscale/github-action`, SSHes to `100.65.0.15`, runs `git reset --hard origin/main` in `/root/gaia`, then `docker compose up -d --build` in `services/gaia-api`.
- **`services/cognition`:** Tailscale-only, `:8890` (Postgres-backed patterns & hypotheses).
- **`proxy/` (`gaia-hermes-proxy`):** internal nginx fronting `hermes-agent`, injecting its auth token so no client ever sees it.

See `docs/split-plan.md` for the full topology and what's still interim, and `docs/evolution.md` (Milestone 9 and later) for how `gaia-api` came to exist.

---

## How to Read This Document

This is a living reference, not a foundation document like `vision.md` or `architecture.md` — it records *where things currently run and how to reach them*, not why. Update it whenever a URL, port, or deployment mechanism actually changes; treat a stale entry here as a defect, same as any other doc.
