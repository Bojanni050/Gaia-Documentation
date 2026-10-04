---
title: Gaia — Capture & Chronicle
document: capture-chronicle
version: 1.0.0
status: active
last_updated: 2026-10-05
owner: Gaia Product Foundation
framing: "Gaia is a lifelong personal intelligence designed to grow through understanding."
---

# Gaia — Capture & Chronicle

Two roles, one word. **Chronicle** is both a personal archive app and the capture
path into Gaia. This document fixes what the *capture* role is — and, more
importantly, what it is not — so the boundary cannot drift later.

## The one-line rule

Chronicle's only connection to Gaia is this: it brings a chat, **byte for byte**,
into **Foundation** as a raw observation. Nothing else crosses.

No tag, no summary, no interpretation, no relationship, no ordering of importance
travels from Chronicle toward Gaia. Capture is transport, not thought.

## What this settles

`architecture.md` v2.4.0 deferred exactly this question — local observation/capture
was *"explicitly out of scope"*, deferred, and stated that nothing in that document
should be read as deciding it. This is the decision, made only for the part that is
now known: **chat logs**.

## The two roles

**Chronicle, the archive (the user's tool).** A personal app to read, search and
explore your own AI conversations. It is *your* archive. Its derived layer —
summaries, tags, notes, mind maps, connections, analytics — lives entirely inside
that app and stays there. It never reaches Foundation or Gaia.

**Chronicle, the capture path (Gaia's input).** Takes the chat as-is and hands it
to Foundation. One direction, one payload, one destination.

These are two roles, not two authors of the same truth. The archive may be as rich
as you like; the capture path is deliberately bare.

## The chat is immutable

A chat is a record of something that happened. It does not change.

- **Immutability is why nothing can drift.** The same chat is stored in the archive
  and in Foundation, and both copies are never mutated. Two copies of an unchanged
  thing cannot diverge — there is nothing to synchronise.
- **Derived data never mutates the chat.** A tag, a summary or a note is a new fact
  *about* the chat, not an edit *of* it — the same shape as a hypothesis over an
  observation. Recording that a chat exists and recording what you think about it are
  separate acts, in separate places.
- **Foundation stays raw.** Only the chat itself, its source (which AI it came from)
  and its timestamp enter Foundation. Anything derived stays in the archive.

## Where derived data lives

Summaries, tags, connections: **in the archive, never in Foundation.** They belong
to you, they live in your tool, and they are not an input to Gaia's reasoning. If a
day comes when Gaia should know them too, that is a separate decision with a separate
path (Logos proposes, a human confirms) — not a widening of the capture step.

## Open — not decided here

- **What counts as "a chat".** Each source (ChatGPT, Claude, Gemini, Qwen, …) has its
  own export shape. Whether one canonical raw form exists, or each source keeps its
  own, is not settled by this document.
- **Other documents.** The intent is to capture only chats for now. Uploading other
  documents you care about may follow; that needs its own thought. If it does, the
  rule is unchanged — raw, immutable, one direction — but the *kind* of thing a
  document is, and how it is identified, is undecided.
- **Which archive app.** Several Chronicle prototypes exist. Which one becomes the
  archive is still being chosen; this document describes the capture role, which is
  independent of that choice.

## What must never happen

- Chronicle writes a summary, pattern or interpretation into Foundation — or into
  anything Gaia reads. **Never.**
- Chronicle reads derived knowledge from Gaia and presents it as its own archive
  content. The archive shows *your* notes; Gaia's derived knowledge, if ever shown,
  is labelled as Gaia's.
- "We captured it, so let's also tidy it up on the way" — normalisation that alters
  the chat is mutation, however innocent.
