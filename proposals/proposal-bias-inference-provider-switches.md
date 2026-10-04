---
title: Ontwerpvoorstel — Bias-inferentie over reasoning-providerwisselingen
document: proposal-bias-inference
version: 1.0.0-proposal
status: proposal
last_updated: 2026-10-05
owner: Gaia Product Foundation
framing: "Gaia is a lifelong personal intelligence designed to grow through understanding."
---

# Ontwerpvoorstel — Bias-inferentie over reasoning-providerwisselingen

**Status:** voorstel, niet geïmplementeerd
**Datum:** 2026-10-05
**Aanleiding:** transcriptie `Voorkom_Identiteitsverschuiving_In_Gaia_V3_2` (AI-review van de V3-docs):
*"Dan vindt er al een vorm van identity drift plaats, ver voordat jouw statusmarkering of die
menselijke override in werking kan treden."*

---

## 1. Het gat dat dit dicht

De bestaande bescherming tegen identity drift is: **statusmarkering + Absolute Override**. Logos
produceert hypotheses, die worden gelabeld als `interpretation`, en alleen een mens promoveert ze
naar `confirmed`.

Maar er zit een stap vóór die bescherming:

```
LLM (reasoning provider)  →  vormt de hypothese  →  statusmarkering  →  humana override
                            ^^^^^^^^^^^^^^^^^^
                            HIER grijpt niets in
```

**Welke** hypotheses er überhaupt gevormd en aan Hindsight aangeboden worden, is een filter van
het gekozen model. Een model dat gefinetuned is op overdreven behulpzaamheid, of op sluitende
westerse logica, laat andere dingen zien dan een ander model — nog vóórdat de
statusmarkering of de override iets kan tegenhouden. De denkruimte wordt onmerkbaar ingeperkt
door het instrument.

Dit is een echt gat, en het is des te relevanter omdat het LLM expliciet **vervangbaar** is: de
architectuur zegt dat je morgen een ander model kunt kiezen. Dat is pas veilig als je kunt meten
wat zo'n wissel met je interpretaties doet.

---

## 2. Wat er al ligt (aanknopingspunten in de code)

- **`services/gaia-api/src/providerStore.js`** — houdt de reasoning-rol bij:
  `roles.reasoning = { mode: 'catalog'|'manual', model: '...' }`.
- **`services/gaia-api/src/logos/llmCallLog.js`** — logt al per model-call:
  `system`, `provider`, `model`, `purpose`, `latencyMs`, `ok`, `contextId`, `correlationId`.
  Wordt weggeschreven naar `decisionStore` onder `kind: 'llm.call'` en is zichtbaar in
  `/admin`'s decision log.
- **`services/gaia-api/src/reasoning/cognitionSync.js`** — spiegelt elke state-change van een
  hypothese naar Hindsight met `document_id gaia-hyp-{id}-v{N}` en `gaia_hypothesis_*`-metadata
  (incl. `sources`, `confidence`, `scope`, `counter_hypothesis`).
- **`services/cognition/src/hypotheses.js`** — de store houdt `sources`, `counter_hypothesis` en
  `scope` per record; `confirmed`/`rejected` met timestamps.

De ruwe data om "wat veranderde er na de wissel" te beantwoorden is er dus grotendeels al. Wat
ontbreekt is een **meting** en een **interpretatie** van die meting.

---

## 3. Het idee

Drie stukken, in volgorde van toenemende complexiteit. Alleen (A) is klein; (B) en (C) zijn
optioneel.

### A. Provider-stempel op de afgeleide records (klein)

Elke hypothese die Cognition invoert krijgt bij het aanmaken een stempel van de provider/model
die hem gevormd heeft. Dit is puur provenance, geen oordeel.

- Waar: `cognitionSink.js` / `hypothesisManager.js` bij `propose()`.
- Vorm: een veld `formed_by: { provider, model }` naast de bestaande velden — of, als we de
  Cognition-schema niet willen uitbreiden, in de Hindsight-metadata die `cognitionSync.js` al
  schrijft (`gaia_hypothesis_formed_by_provider` / `_model`).
- Waarom klein: het is een extra attribuut op een bestaand pad, geen nieuwe logica.
- Waarom nodig: zonder stempel is "systematische afwijking" niet meetbaar — je kunt dan niet
  vaststellen bij welk model een hypothese hoorde.

**Let op (A/B keuze):** als de Cognition-tabel een kolom moet krijgen, is dat een migratie in
`services/cognition/src/db/migrations/`. De goedkopere route is de bestaande Hindsight-metadata
hergebruiken, want de sync-laag schrijft daar al `gaia_hypothesis_*`-velden. Dat vraagt geen
migratie en de read-adapters (`hindsightHypothesisAdapter.js`) negeren onbekende velden al.

### B. Een wissel-detectie (middel)

Bij een wijziging van `roles.reasoning.model` (in `PUT /admin/api/provider/roles`) of van de
hoofd-provider (in `PUT /admin/api/provider/config`):

1. Leg het moment vast (timestamp + oude en nieuwe `provider`/`model`).
2. Definieer een **observatievenster**: de N hypotheses van vóór de wissel tegenover de N erna.
3. Vergelijk op *controleerbare* assen, niet op "is het waar":
   - **Verdeling van `scope`** (micro/macro) — verschuift de nieuwe provider naar meer macro?
   - **Aanwezigheid van `counter_hypothesis`** — formuleert het nieuwe model nog opposities,
     of wordt het `null`-percentage hoger?
   - **Aantal `evidenceFor`/`evidenceAgainst`** per hypothese — formuleert het nieuwe model nog
     tegenspraak, of alleen ondersteuning?
   - **`confidence`-verdeling** — een model dat structureel hogere confidence geeft is een signaal.
4. Rapporteer als **signaal**, niet als conclusie. De uitkomst is een notitie in de decision log:
   *"Na wissel naar model X: aandeel macro-hypotheses steeg van 12% naar 41%; aandeel hypotheses
   zonder tegenhypothese steeg van 5% naar 30%."*

Dit is heel bewust een **heuristiek op vormkenmerken** en niet op semantiek: het beweert niets over
waarheid, het meet alleen of het gedrag van het instrument verschoven is. Dat past bij het
bestaande verbod op "intelligentie in de store" — dit is een deterministische vergelijking, geen
redeneerlaag.

### C. Taal van de gebruiker als ijkpunt (groot, optioneel)

Het scherpste voorbeeld uit de bron is: *een nieuw model dat overdreven beleefd is strijkt
sarcastische opmerkingen glad.* Dit te detecteren vraagt een signatuur van het taalgebruik van de
gebruiker (toon, directheid) en het vergelijken daarvan met wat het model ervan maakte. Dat is
semantisch, duur, en raakt privacy. **Niet nu.** Alleen benoemen zodat het niet vergeten wordt.

---

## 4. Wat dit expliciet NIET is

- Geen wijziging aan de Absolute Override of aan `canConfirm()`. De mens blijft de enige weg naar
  `confirmed`.
- Geen automatische correctie. Als de bias-check een verschuiving meldt, verandert er **niets**
  aan de hypotheses — de mens ziet een signaal.
- Geen oordeel over "waarheid". Het meet vormkenmerken (scope, tegenhypothese, evidence-balans,
  confidence), niet of een hypothese klopt.
- Geen LLM in de meetstap. Dat zou precies de fout zijn die de bron zelf aankaart: een model dat
  oordeelt over de bias van een model.

---

## 5. Waarom dit de moeite waard is

- Het raakt een gat dat de bestaande lagen (statusmarkering, tegenhypothese, override) **niet**
  afdekken: de vorming van de hypothese zelf.
- Het is een van de weinige ideeën uit het bronmateriaal dat (a) concreet bouwbaar is, (b) op een
  bestaande naad past (`llmCallLog` + `providerStore` + `cognitionSync`), en (c) het
  "model is vervangbaar"-principe omzet in iets meetbaars. Een vervangbaar orgaan is pas veilig
  als je kunt zien wat de vervanging doet.
- Het is klein te beginnen: (A) is provenance, (B) is een rapport. (C) hoeft nooit.

---

## 6. Open vragen om te beslissen

1. **Cognition-kolom of Hindsight-metadata?** Voorkeur: metadata, geen migratie. Besluit dit
   vóór (A), want het bepaalt de vorm.
2. **Hoeveel hypotheses is "genoeg"?** N=..? Zonder minimum is elke vergelijking ruis. Voorstel:
   rapporteer pas boven drempels (bijv. ≥10 aan elke kant), anders "te weinig data".
3. **Waar komt het rapport?** `decisionStore` onder een nieuw `kind: 'provider.drift'` past bij de
   bestaande `/admin` decision log.
4. **Moet een wissel ooit geblokkeerd worden?** Nee. Dit is observability, geen poort.
5. Samenhang met het soft-evidence-voorstel: als (B) ooit confidence-verdelingen meet, moet de
   meetstap niet veranderen als de confidence-rekenkunde verandert. Houd de twee gescheiden.

---

## 7. Aanbeveling

1. **Nu:** niets implementeren. Leg dit vast zodat de afweging niet opnieuw gemaakt wordt.
2. **Als eerste stap, wanneer er behoefte is:** (A) — de provider-stempel, want zonder die
   provenance is (B) niet te bouwen.
3. **Daarna, optioneel:** (B) — het rapport als observability-signaal in de decision log.
4. **(C) nooit, tenzij iemand er expliciet om vraagt.**
