---
title: Gaia — Epistemisch Inzicht (plan)
document: epistemic-inspector-plan
version: 0.1.0
status: proposal
last_updated: 2026-09-25
owner: Gaia Product Foundation
framing: "Gaia is a lifelong personal intelligence designed to grow through understanding."
pulse-tags: ["epistemic-inspector", "cognition", "hypotheses", "logos", "gap-analysis"]
pulse-connections: [{"path": "architecture/architecture.md", "relation": "supports", "why": "Cites architecture document constraints stating external components cannot bypass Hindsight to touch storage internals directly."}, {"path": "development/coding-standards.md", "relation": "supports", "why": "Upholds the coding standards rule that modules must respect declared layer boundaries and not reach into storage internals."}]
---

# Gaia — Epistemisch Inzicht (plan)

> **Status: voorstel. Er is nog geen code geschreven.** Dit document beschrijft
> *wat er vandaag in de codebase staat*, waar een gevraagde "Memory & Epistemisch
> Inspector" tegenaan loopt, en in welke volgorde het — als het de moeite waard
> is — opgelost zou moeten worden. Het is een plan, geen besluit.

---

## 1. Waarom dit document bestaat

Er is een specificatie opgesteld voor een "Logos Memory & Epistemisch Inspector
Dashboard" met drie tabs (`/hypotheses`, `/decisions`, `/provenance`), een
React 19 + Tailwind + shadcn/ui frontend, en drie API-contracten.

Die specificatie is geschreven als een *kant-en-klare prompt voor een AI-coder*
— een generiek bouwvoorschrift, niet als iets dat op deze codebase is
gebaseerd. De onderstaande bevindingen zijn het gevolg van het lezen van de
werkelijke bronnen. Ze zijn geen tegenspraak in de zin van "dit kan niet"; ze
zijn een correctie van de aannames die de spec maakt, zodat een volgende
implementatie niet op een scheve basis landt.

---

## 2. Wat er werkelijk bestaat

| | Vindplaats |
|---|---|
| Hypotheses (duurzaam) | `services/cognition/` — eigen Postgres, Tailscale-only, `:8890` |
| Hypothese-schema & levenscyclus | `services/cognition/src/db/migrations/002_create_hypotheses.sql`, `src/hypotheses.js` |
| Hypothese-routes | `services/cognition/src/routes/hypotheses.js` (`/v1/banks/:bank_id/hypotheses`, `/:id/confirm`, `/:id/reject`) |
| Spiegel van het hypothese-model in Logos | `services/gaia-api/src/logos/reasonModels.js` |
| Levenscyclusbeleid in het redeneerpad | `services/gaia-api/src/reasoning/hypothesisManager.js` |
| IntentIQ/ReasonIQ-beslissingslog | `services/gaia-api/src/logos/decisionStore.js` (JSONL per dag) |
| Operator-pagina (statisch, geen build-step) | `services/gaia-api/public/admin.html` + `src/adminRoutes.js` |
| Bestaande epistemische labels | `epistemicLabel()` in `services/gaia-api/src/foundationSearch.js` |
| Client | Tauri-shell (`src-tauri/`). `frontend/` bevat geen app-bronnen. |

---

## 3. Gaps tussen de spec en de werkelijkheid

### 3.1 De API-contracten bestaan niet zoals gespecificeerd

De spec noemt `GET /api/v1/hypotheses` en `POST /api/v1/hypotheses/:id/validate`
op gaia-api. Die bestaan niet. Hypotheses zijn in deze architectuur géén
gaia-api-bezit: ze staan in `services/cognition`, achter een eigen, bank-scoped
API, op een eigen poort, buiten de gaia-api-auth om.

Dat is geen toeval maar een architectuurbesluit (`docs/architecture.md` §7:
geen component buiten Hindsight raakt opslaginternals; §6.1/§6.2: Hindsight
persisteert, Logos redeneert). `services/cognition` is bewust een
*Hindsight-adjacent opslag-sidecar*. Elke nieuwe gaia-api-route die er direct
naar wijst, is een architectuurkeuze en geen detailwerk.

### 3.2 De voorgestelde status-machine is in strijd met de bestaande

De spec voegt `voorganger_id` toe: een afgewezen hypothese kan na nieuwe feiten
weer "heropenen", met de oude hypothese als voorganger.

In `services/cognition/src/hypotheses.js` zijn `confirmed` en `rejected`
**terminal** — `VALID_TRANSITIONS.confirmed === []`. Dat is een bewuste keuze,
gebaseerd op de adoptie van Stash's concept (zie de module-comment in
`hypothesisManager.js`, waar Stash deze twee juist *wél* terminal maakt, en waar
Gaia de redenering expliciet documenteert).

Belangrijker: beide plekken moeten het met elkaar eens zijn. `reasonModels.js`
draagt de toekomstige status-vormen al wél open (daar is
`confirmed → testing` toegestaan, omdat nieuwe tegenspraak een gevestigde
overtuiging moet kunnen ondermijnen). Er bestaan dus nu **twee verschillende
levenscycli** in twee bestanden. Die spreken elkaar tegen, en de spec zou er
nog een derde bij zetten.

Dit is het belangrijkste openstaande punt in dit plan: het is een
scheidsvraag, geen implementatievraag.

### 3.3 Ontbrekende velden

`verwerp_bron` (`mens` | `consolidatie`) bestaat niet; er is `rejection_reason`
(zonder bron). `evidence` bestaat als `evidence_memory_ids TEXT[]` — een lijst
memory-ID's, geen `{ sourceId, snippet }`-objecten met citaten. Een
"uitklapbare citaten"-weergave vraagt dus om een haalactie uit Hindsight per
hypothese: een N+1-patroon dat in de UI beslist moet worden, of een
server-side batch.

### 3.4 "DecisionIQ" bestaat niet

De spec beschrijft records met `userIntent`, `chosenRoute`,
`efficiencyEvaluation`, `userFeedback`, `learnedLesson`. Geen van die velden
bestaat. Het decision-log bevat IntentIQ- en ReasonIQ-records
(`intentiq.decision`, `reasoniq.gate`, `reasoniq.result`, `llm.call`) met
tellen, tijdstippen en beslissingen — geen "efficiëntiescore" of "geleerde les".

Die velden verzinnen betekent nieuwe cognitieve semantiek invoeren: een oordeel
over of een gereedschapsaanroep "nodig" was, en een opgeslagen les. Dat is een
besluit over *hoe Gaia denkt*, niet over *hoe iets eruitziet*. Het hoort in een
eigen evolution-entry, niet in een UI-pass.

### 3.5 Er is geen web-frontend, en er zou er geen moeten komen

`frontend/` bevat `node_modules` en een `.env.local`, geen bron. De
gebruikerservaring is Tauri; de enige web-UI is de statische operatorpagina in
Gaia's eigen palet, zonder build-step.

Een nieuwe React 19 + Tailwind + shadcn app toevoegen betekent een tweede
frontend-toolchain naast de Tauri-shell, zonder dat daar een productbehoefte
voor is. `docs/roadmap.md` zegt expliciet: *geen speculatieve infrastructuur
buiten Gaia Cloud*, en V1 vraagt om een "calm, opt-in memory view" — geen
dashboard.

---

## 4. De ontbrekende beslissingen

Voordat er code komt, moeten deze vier vragen beantwoord zijn. Ze zijn niet
technisch; ze zijn Gaia's.

1. **Voor wie?** De bestaande admin-pagina is *operator tooling*: intern, achter
   een bearer-token, Tailscale-only. `docs/architecture.md` §8 beschrijft een
   *andere* weergave: een "calm, opt-in memory view" die de gebruiker zelf opent,
   met correctie- en forget-mogelijkheden. Dat zijn twee verschillende
   publiekken, met verschillende rechten. De spec verwart ze. Welk wordt dit?

2. **In het dashboard of in de conversatie?** Een "provenance-matrix" als aparte
   pagina is precies de navigatie- en dashboard-denkwijze die
   `docs/ui-principles.md` afwijst. Provenance is volgens §8 "always available
   on demand, never in the user's face". Een tweede tab betekent dat de gebruiker
   *naar een plek toe moet*.

3. **Mag de gebruiker hypotheses bevestigen en afwijzen?** §8 zegt ja ("correct
   it, accelerate or reject it"). De confirm-route in `services/cognition`
   heeft daar een consequentie: bevestigen retain't de hypothese als echte
   memory in Hindsight (`services/cognition/src/hypotheses.js:105`). Dat is een
   *duurzaam, semantisch* gevolg van een klik. Mag dat zonder Gaia erbij te
   horen? Dat raakt precies "Trust is earned, never requested".

4. **Wie beslist de levenscyclus?** Zie §3.2. Zolang twee bestanden twee
   machines beschrijven, kan geen UI ze beide consistent tonen. Dit moet eerst
   worden beslist en in `docs/architecture.md` §6.1 worden vastgelegd.

---

## 5. Voorgestelde volgorde (voorwaardelijk: eerst de beslissingen hierboven)

**Fase 0 — beslissingen.** Antwoord op §4. Documenteer in `docs/evolution.md`
met rationale, trade-offs en toekomstrichting. Niets bouwen.

**Fase 1 — één waarheid over de levenscyclus.** Kies één state machine; werk
`reasonModels.js` en `services/cognition/src/hypotheses.js` naar dezelfde
definitie toe, of maak de spiegeling expliciet en gedocumenteerd. Test beide
kanten. Dit is een echte refactor en hoort op zichzelf te staan.

**Fase 2 — lezen, pas daarna schrijven.** Een read-only epistemische weergave
(hypotheses met status, confidence, verificatieplan, en welke memory-ID's als
bewijs dienen) via de bestaande operator-pagina, met de bestaande
`epistemicLabel()`-taal en het bestaande palet. Geen nieuwe badges, geen tweede
vocabulaire. Eerst zien of het klopt, dan pas laten handelen.

**Fase 3 — schrijven.** Confirm/reject pas als het *wat* en *waarom* vastligt,
en alleen via een route die de consequentie uit §4.3 zichtbaar maakt vóór de klik.

**Fase 4 — pas daarna, en alleen als bewezen nodig, een eigen oppervlak.** Niet
vooraf.

---

## 6. Wat er nadrukkelijk niet komt

- Geen React/Tailwind/shadcn-app zonder bewezen productbehoefte.
- Geen "efficiëntiescore", `userFeedback` of `learnedLesson` zonder
  architectuurbesluit — die velden verzinnen is cognitief ontwerp, geen UI.
- Geen derde levenscyclus-definitie naast de twee die er al zijn.
- Geen provider- of service-specifieke namen in de UI-laag
  (`docs/architecture.md` §7).
- Geen dashboard-denkwijze in de gebruikerservaring; de conversatie blijft
  thuis (`agents.md`).

---

## 7. Gerelateerde documenten

- `docs/vision.md` — waarom Gaia geen dashboard is
- `docs/architecture.md` §6.1, §6.2, §7, §8 — hypotheses, opslag, provenance & user control
- `docs/roadmap.md` V1 "Should Have" — calm, opt-in memory view
- `docs/operations.md` — wat de bestaande operator-pagina vandaag bevat
- `services/cognition/README.md` — de echte hypothes-API
- `services/gaia-api/src/logos/reasonModels.js` — de spiegel die op dit moment divergeert

---

## 8. Hoe dit document te lezen

Dit is een living proposal, geen vastgesteld ontwerp. Zodra §4 beslist is,
verschijnt hier een Phase 0-entry met de uitkomst, en verandert dit document
van *plan* in *besluit*. Gebruik `docs/evolution.md` voor het verhaal van hoe
Gaia hierin veranderde — niet dit document.



