---
title: Ontwerpvoorstel — Soft-evidence accumulator voor micro-hypotheses
document: proposal-soft-evidence
version: 1.0.0-proposal
status: proposal
last_updated: 2026-10-05
owner: Gaia Product Foundation
framing: "Gaia is a lifelong personal intelligence designed to grow through understanding."
---

# Ontwerpvoorstel — Soft-evidence accumulator voor micro-hypotheses

**Status:** voorstel, niet geïmplementeerd
**Datum:** 2026-10-05
**Aanleiding:** podcast-transcript ("soft evidence building", `C(t+1) = C(t) + α(1−C(t))·score`, "cap op 80%")

---

## 1. Samenvatting

De formule uit het transcript is een **delta-regel** (Rescorla–Wagner): een schatter van een
herhaald, ruisig proces. Onze confidence wordt op dit moment niet gevoed door een stroom
herhaalde observaties, maar door **schaars, door het model beoordeeld bewijs** met een
expliciete `confidenceDelta` per stuk. De formule is dus geen vervanging van de huidige regel,
maar mogelijk een **aanvullend pad** voor één specifiek geval: `micro`-hypotheses die door de
tijd heen steeds opnieuw door observaties worden geraakt.

Dit voorstel beschrijft dat pad, de exacte vorm, de begrenzing, en de voorwaarden waaronder het
pas gebouwd mag worden.

---

## 2. Probleemstelling

Huidige stand, geverifieerd in code:

- `services/gaia-api/src/reasoning/hypothesisManager.js` — `applyUpdate()` doet
  `confidence + confidenceDelta` (lineair). Voor nieuwe hypotheses zet
  `applyReasoningResult()` `rh.confidence` direct.
- `services/cognition/src/hypotheses.js` — `applyEvidence()` doet hetzelfde, met
  `clampConfidence()` op **0.95** ("soul.md: never claim certainty").
- Machine soft-promotie naar `corroborated` gebeurt op de **drempel** `C >= 0.80`
  (`markCorroborated`), niet als cap.
- `scope ∈ {micro, macro}` bestaat al (migratie `007_counter_hypothesis_scope.sql`),
  met `macro` als veilige default.

Wat ontbreekt: een mechanisme dat, wanneer **hetzelfde** micro-patroon over veel dagen
steeds opnieuw wordt waargenomen, de confidence **geleidelijk met afnemende stappen** laat
convergeren — in plaats van losse deltas die elkaar opvolgen en tegen de clamp aanlopen.

---

## 3. Eigenschappen van de voorgestelde formule

Voorgestelde vorm (symmetrische generalisatie van de podcast-variant):

```
C ← C + α · (target − C)          target ∈ [0, 1],  α ∈ (0, 1]
```

Met binaire observaties:

- waargenomen           → `target = 1`   → `C ← C + α(1 − C)`   (stijgt, met afnemende stap)
- niet waargenomen      → `target = 0`   → `C ← C(1 − α)`        (daalt)

Eigenschappen:

1. **Afnemende meeropbrengst** — de stap krimpt naarmate C dichter bij de observatie ligt.
   Dit is het "soft evidence building"-gedrag, en het is de moeite waard.
2. **Convergentie naar de waargenomen frequentie** — gebruikt de persoon het patroon 8 van
   de 10 dagen, dan convergeert C naar ~0,80. Dit is waar het "80%"-getal in het gesprek
   vandaan komt: het is een gedragsfrequentie, **geen wiskundig plafond**.
3. **Begrensd van nature** — voor `target ∈ [0,1]` en `α ∈ (0,1]` blijft C in [0,1]; er is
   geen aparte clamp nodig om binnen bereik te blijven.
4. **Wél een neerwaartse tak** — anders dan de eenzijdige `C + α(1−C)·score` uit het
   transcript, kan deze vorm verzwakken (target laag). Dat is nodig zodra een observatie
   uitblijft.

**De 0.95-zekerheidsgrens blijft.** De formule zelf nadert 1 (bij een 100%-frequentie),
dus soul.md's "never claim certainty" moet alsnog expliciet via `clampConfidence()` worden
gehandhaafd. Er is geen "cap op 0.80" in de formule; 0.80 blijft de bestaande
`corroborated`-drempel.

---

## 4. Waarom dit alleen voor `micro` geldt

De naad bestaat al: `micro` vs. `macro`. De logica van het transcript valt hier naadloos op:

| Situatie | Mechanisme |
| --- | --- |
| `micro` + herhaalde observaties (stijl, routine) | soft accumulator (dit voorstel) |
| `micro` + schaars, expliciet bewijs | bestaande delta-regel, ongewijzigd |
| `macro` (identiteit, carrière, gezondheid) | bestaande delta-regel + rationale-frictie + human override |

**Macro wordt nooit door de accumulator geraakt.** Macro-statements blijven humana-gated en
blijven een expliciete `confidenceDelta` gebruiken. De `corroborated`-soft-promotie is al
micro-only en blijft dat.

---

## 5. Voorgestelde API (schets, geen implementatie)

In `hypothesisManager.js`, naast `applyUpdate()`:

```
accumulateObservation(hypothesisId, { observed, alpha = DEFAULT_ALPHA })
```

Gedrag:
- Weigert wanneer `scope !== 'micro'` (macro is niet eligible).
- Weigert wanneer `status === 'rejected'` (bestaande quarantaine).
- `C ← C + alpha * ((observed ? 1 : 0) - C)`, daarna door bestaande `clampConfidence()`.
- Schrijft een audit-record met `relation: 'observation'` en `confidenceBefore/After`
  (het audit-spoor bestaat al en houdt de wijziging uitlegbaar).
- Verandert **niets** aan status anders dan wat de bestaande `corroborated`-drempel-logica
  al doet; het bereikt nooit `confirmed`.

De store-zijde (`services/cognition`) hoeft dit niet te kennen: dit is een
reasoning/manager-side update die via de bestaande `sink` wordt geperst.

---

## 6. Prerequisite die nu ontbreekt (belangrijk)

Er is op dit moment **geen observatiestroom** die dezelfde hypothese herhaaldelijk voedt.
`runDeferredCognition` registreert één Foundation-observatie per beurt, maar er is nog geen
stap die "deze nieuwe observatie bevestigt/ontkracht hypothese X" levert.

Daarom:

1. **Bouw dit pas als die stroom bestaat.** Zonder herhaalde observaties is de accumulator
   een ongebruikt pad en een onverifieerbare parameter (α) — YAGNI.
2. Als de stroom er komt, moet er een **koppeling** zijn observatie → bestaande hypothese,
   met een **dedup-guard** zodat één observatie niet dubbel telt.

---

## 7. Kalibratie van α

- Default conservatief voorstellen: **α = 0.05–0.10** (langzaam, stabiel).
- α is **niet** met data te onderbouwen zonder een gelabelde feedbackset
  ("deze hypothese bleek achteraf waar/onwaar"). Die bestaat niet.
- Daarom: α als expliciete, gedocumenteerde constante — geen magisch getal, consistent met
  de `DEFAULT_POLICY`-stijl in de manager.
- Kalibreren = een aparte, latere exercitie zodra er feedbackdata is.

---

## 8. Effect op tests en bestaande regels

- De huidige testsuite assert exacte rekenkunde (bijv. `0.9 − 0.15 = 0.75`). Het toevoegen
  van een **apart** pad breekt die tests niet, mits de bestaande delta-regel ongemoeid blijft.
- Nieuwe tests:
  - convergentie: herhaalde `observed: true` stijgt met afnemende stappen en blijft ≤ 0.95;
  - `observed: false` verlaagt;
  - macro wordt geweigerd;
  - `rejected` wordt geweigerd;
  - een micro-hypothese bereikt nooit `confirmed` via dit pad;
  - eigenschapstest: C blijft in [0, 0.95] voor willekeurige observatie-reeksen.
- **Niet** aanraken: `clampConfidence` (0.95), `markCorroborated` (0.80), `canConfirm()`,
  de gegenereerde `confirmed`-paden.

---

## 9. Expliciet buiten scope

- De formule als **vervanging** van de lineaire delta-regel.
- Enige wijziging aan macro-gedrag, de confirm-policy, of de Absolute Override.
- De `newConfidence` uit `evidenceAssessments` ineens als state-bron gaan gebruiken
  (dat is een losse vraag; zie §10).
- Een "cap op 80%" implementeren — die bestaat niet en hoort niet te bestaan; 0.80 is een
  statusdrempel.

---

## 10. Losse punten om te beslissen (niet in dit voorstel)

1. **`newConfidence` vs. `confidenceDelta`.** Het schema laat Logos beide teruggeven;
   alleen de delta zet state. Is `newConfidence` bewust review-only? Besluit dit apart —
   het raakt dezelfde confidence-waarde en mag niet tegelijk met dit voorstel veranderen.
2. **Decay bij langdurige afwezigheid.** Moet een micro-hypothese die maanden niet wordt
   waargenomen vanzelf zakken (halveringstijd)? Nu niet voorzien.
3. **Observatie-bron.** Welke stap levert "observatie → hypothese" — een uitbreiding van de
   Logos-reflectie, of een aparte job? Bepaal dit vóór implementatie.

---

## 11. Aanbeveling

1. **Nu:** niets implementeren. Leg dit voorstel vast zodat de afweging niet opnieuw wordt
   gemaakt.
2. **Later, zodra er een observatiestroom is:** het micro-accumulator-pad toevoegen zoals in
   §5, achter de bestaande `scope`-naad, met α als gedocumenteerde constante.
3. **Altijd:** de lineaire delta-regel voor schaars/macro bewijs laten staan; de veiligheid
   van het systeem zit in de lifecycle (Absolute Override, quarantaine, tegenhypothese), niet
   in de confidence-rekenkunde.
