# Gaia — Herkalibratiematrix (fundamenten)

> **Voorbereiding op Phase 2. Geen enkel fundamentdocument is hiermee gewijzigd.**
>
> Deze matrix zet per fundamentele stelling drie dingen naast elkaar:
> wat het fundament vandaag beweert, wat de v3.0-documenten beweren, en welke
> vraag in de code moet worden beantwoord voordat er iets wordt herschreven.
>
> **Waarom dit bestand bestaat.** Herkalibratie is vanuit de documentatie
> alleen niet mogelijk. De enige twee documenten die zich "Definitief
> (September 2026)" noemen, zijn zeven minuten na elkaar toegevoegd, dragen
> geen versie, eigenaar of `last_updated`, en verschillen van elkaar op minimaal
> vijf inhoudelijke punten. Als de fundamenten op die twee documenten werden
> gelijkgezet, zou de documentatie consistent worden met zichzelf in plaats van
> met de code. Dat is precies wat `rules.md` verbiedt.
>
> Elke rij hieronder is dus een **openstaande vraag**, geen voorstel.

---

## Hoe dit te lezen

| Kolom | Betekenis |
|---|---|
| **Huidige bewering** | Wat een fundamentdocument nu zegt, met vindplaats. |
| **v3.0-bewering** | Wat de v3.0-documenten zeggen, of `—` als ze het niet zeggen. |
| **Conflict** | `TEGENSPRAAK` / `GAT` / `ONBEVESTIGD` / `BOTONING` / `ONSCHADUWBAAR`. |
| **Vraag voor de code** | Wat in `Gaia-Cloud` / `Foundation` moet worden nagegaan. |
| **Gevolg** | Welk document geraakt wordt als het antwoord bekend is. |

**Reikwijdte.** Alleen de fundamenten in `foundation/`. De architectuur-,
development- en operations-documenten hebben hun eigen open questions
(Q-002, Q-005, Q-006, Q-010, Q-011). Deze matrix is de *fundamenten*-doorsnede
van die problemen, niet een herhaling ervan.

---

## A. `foundation/vision.md` — de grondslag

`vision.md` is volgens zijn eigen tekst "the root of Gaia's foundation. Every
other document … exists to serve the vision stated here." Het is daarmee het
enige document dat een *autoriteitsclaim over de hele basis* maakt. Het bevat
drie stellingen die de v3.0-documenten tegenspreken, en één die de v3.0-
documenten bevestigen.

| # | Huidige bewering (vindplaats) | v3.0-bewering | Conflict | Vraag voor de code | Gevolg |
|---|---|---|---|---|---|
| A1 | "Gaia is a **desktop-first**, lifelong personal intelligence" (regel 21) | v3.0 zwijgt over desktop vs. cloud; noemt 5 repositories, waarvan `gaia-desktop` er een is naast `gaia-web` | **TEGENSPRAAK** (met regel 43 van hetzelfde document) | Waar draait SOUL? Processen in `Gaia-Cloud` of in `gaia-desktop`? | Zie Q-001. Bepaalt of de kern van het fundament blijft of verandert. |
| A2 | "SOUL, Logos, Hindsight, and Capabilities **all run in Gaia Cloud**" (regel 43) | `Gaia-Cloud`: "Het Harnas — herbergt SOUL, Logos, `gaia-api` en de orkestratielogica" | **BEVESTIGD** | — | Geen conflict. Bevestigt dat de interne tegenspraak in `vision.md` zelf zit, niet tussen de documenten. |
| A3 | Capabilities zijn o.a. "Hermes (reasoning)" (regel 40) | Hermes is **sub-agent** onder HADES, "voor executie en communicatie op meerdere apparaten" (`architecture-v3.0.md` §5). Redenering hoort nu bij Logos. | **TEGENSPRAK** | Is Hermes nog de reasoning-provider achter Logos, of is Logos reasoning rechtstreeks en Hermes iets anders? | Zie Q-009. Verwijdert Hermes óf uit de Capabilities-rij óf uit de sub-agentrol — niet beide. |
| A4 | "Swapping the reasoning engine **underneath Hermes** produces no felt discontinuity" (regel 115, succescriterium 3) | "Gaia is **niet** het onderliggende LLM; het LLM is een inwisselbaar inferentie-orgaan." Het harnas, niet Hermes, overleeft de wissel. | **TEGENSPRAK** (verouderd mechanisme) | Waar zit de provider-abstractie nu: in `Gaia-Cloud` achter Logos, of in Hermes? | Successcriterium 3 moet het object van de wissel hernoemen. Een gedragscriterium wijzigen is een productbesluit. |
| A5 | Lagen: SOUL · Logos · Hindsight · Capabilities · Gaia Desktop | Harnas (= Gaia-Cloud + Foundation) · Logos · Hindsight · Chronicle · sub-agents · clients | **TEGENSPRAK** (laagindeling) | Wat zijn de vijf lagen in de code? Bestaat er een `chronicle`-module, en waar? | Zie Q-003. Dit is de kern van de herkalibratie. |
| A6 | Geen epistemische statusmarkeringen | Vier statussen: `observation` / `interpretation`+`hypothesis` / `confirmed` / `rejected`; "Absolute Override" | **GAT** in `vision.md` | Zijn de statussen in de code enum-waarden, en waar wordt promotie afgedwongen? | Zie Q-011. De statussen horen thuis in `vision.md` (wat Gaia weet) én in de architectuur (hoe het bewaard wordt) — de verdeling moet besloten worden. |
| A7 | Succes: "Trust increases measurably in the user's own words" (regel 117) | Idem, niet herhaald | **ONSCHADUWBAAR** | — | Geen actie. |
| A8 | "Desktop is the origin, not the ceiling" (regel 78) | 5 repositories, twee clients | **ONSCHADUWBAAR** | — | Waarschijnlijk nog geldig. Lijkt op een *houding*, geen deploymentclaim — precies de onderscheiding die regel 21 mist. |

**Waarom A1 en A3 het zwaarst zijn.** A1 is een tegenspraak *binnen één
document*, zesentwintig regel na elkaar: "desktop-first" en "alles draait in
Gaia Cloud". Dat is geen ruis van veroudering — dat is een document dat beide
dingen tegelijkertijd beweert. Welke van de twee de intentie is, is uit de tekst
niet af te leiden.

---

## B. `foundation/lexicon.md` — het enige echte runtime-gat

Dit is de enige stelling in deze matrix die **niet** uitgesteld kan worden tot
Phase 2, om een concrete reden die buiten de documentatie om ligt.

| # | Huidige bewering (vindplaats) | v3.0-bewering | Conflict | Vraag voor de code | Gevolg |
|---|---|---|---|---|---|
| B1 | Twee vocabularen: "Human Language" en "Technical Language". Technical bevat: Hermes, Hindsight, Chronicles, PostgreSQL, MCP, API, JSON, Provider, Streaming, Embeddings, Inference, Reasoning Provider, Vector Database | Harnas, Logos, DecisionIQ, HADES, Absolute Override, `observation`, `interpretation`, `hypothesis`, `confirmed`, `rejected`, Drizzle/SQLite, `mcpServer.js`, capture-rs | **GAT — volledig** | Welke van deze termen bestaan daadwerkelijk in code en worden ze in de UI getoond? | Zie Q-015 (nieuw). |
| B2 | "When users explicitly discuss architecture or software, Gaia may freely use technical terminology" | De v3.0-termen zijn architectuur-*termen*, geen producttermen. Een gebruiker die over code praat, kent `hermes-agent` en `Tailscale`, maar niet "harnas". | **GAT** | Zijn de v3.0-concepten intern of user-facing? | Bepaalt of ze in het *Technical* register horen of een **derde** register nodig is. |
| B3 | Vertalingseisen: "If you ask 'How does Hindsight work?' / 'Hindsight is my long-term understanding system'" | Er is nu een laag vóór Hindsight (Chronicle) en een regel die bepaalt wanneer iets van elkaar onderscheiden mag (Absolute Override) | **GAT** | — | Lexicon bevat geen regel voor: hoe onderscheidt Gaia iets wat `confirmed` is van iets wat `interpretation` is, in gewone taal? Dat is een *nieuw* lexicongedrag, niet een aanpassing. |
| B4 | `lexicon.md` wordt volgens `evolution.md` (Milestone 2) en `split-plan.md` ingelezen door de Foundation Engine en belandt in `artifact.json` → system prompt | — | **GAT** (niet in de v3.0-documenten benoemd) | Wordt `lexicon.md` nog steeds in het system prompt geladen? Door wie? | **Dit maakt B1-B3 urgent.** Als het lexicon nog in de prompt zit en de v3.0-vocabulaire erin ontbreekt, heeft Gaia geen woord voor de kern van haar eigen epistemologie. Dan is dit een live defect, geen documentatieachterstand. |

**B4 is de enige rij in deze matrix die ik niet kan afwachten.** De rest van
`foundation/` is documentatie. `lexicon.md` is volgens de eigen documentatie een
*runtime-input*. Dat verschil is niet in het huidige fundament verwoord — en dat
is op zich een fundamenteel gat: het fundament beschrijft niet welke
fundamentele documenten build-inputs zijn.

---

## C. `principles.md`, `personality.md`, `design-language.md`, `ui-principles.md`

Deze vier heb ik getest tegen de v3.0-veranderingen. **Ze conflicteren niet.**
Dat is een bevinding, geen aanname.

| # | Document | Getest tegen | Uitkomst |
|---|---|---|---|
| C1 | `principles.md` — "Character Before Model: Models provide reasoning. Gaia provides character." | Harnas-paradigma: Gaia is het harnas, het model is een inwisselbaar orgaan | **Versterkt**, niet verzwakt. Het principe is precies wat het harnas institueert. |
| C2 | `principles.md` — "Intent First … Intent precedes action" | v3.0: "De conversatie is de ervaring. Logos is de reflectie op die ervaring." Retrospectieve intentie | **Spanningsveld, niet conflict.** Zie D1. |
| C3 | `personality.md` §2 — initiatief: "Earned, tiered, and reversible", "Silence always beats intrusion" | v3.0: DecisionIQ reflecteert op "tool-efficiëntie (bijv. of een sub-agent zoals Hermes wel of niet nodig was)" | **Onschaduwbaar.** DecisionIQ evalueert *achteraf*; personality regelt *vooraf*. Elkaar raken elkaar niet. |
| C4 | `ui-principles.md` §9 — provenance on demand, "Forgetting is real and immediate" | v3.0 Absolute Override: mens is enige autoriteit die naar `confirmed` promoveert | **Versterkt.** De v3.0-uitwerking is de architectonische vorm van wat het UI-principle al eiste. |
| C5 | `design-language.md` / `ui-principles.md` — overlap | Onderlinge overlap | **Reorganisatie, geen inhoud.** `ui-principles.md` noemt zichzelf een vertaling van `design-language.md`. Eén van beide kan de vertaling zijn, de ander het principe. Zie Q-016 (nieuw). |
| C6 | Alle vier: `last_updated: 2026-06-01`, versie 1.0.0 | v3.0: september 2026 | **Datum-onzeker, niet inhoud-onzeker.** Zie Q-010. |

**Waarom dit belangrijk is.** Er is een verleiding om vanwege de oude datum
alles in `foundation/` als hercalibratie-doelwit te behandelen. Dat zou `C1` en
`C4` — de twee stellingen die de v3.0-architectuur het best bevestigt —
onnodig-risicovol herschrijven. De correcte actie is hier: **niets wijzigen aan
inhoud, alleen de datumstatus vaststellen.**

---

## D. Openstaande spanningen die geen tegenspraak zijn

Deze horen niet in "tegenspraak", maar ze moeten beslist worden.

| # | Spanning | Beweringen | Vraag |
|---|---|---|---|
| D1 | Is redeneren synchroon of asynchroon? | `principles.md`: "Before Gaia decides what to do, she first tries to understand why." v3.0: live beurten zonder synchrone redeneervertraging, alles asynchroon. | Zie Q-013. Als het harnas correct is, is `principles.md` nog juist maar moet "understands why" een *retrospectieve* activiteit worden. Een herschrijving van een principe — niet van een feit. |
| D2 | "V3" betekent twee dingen | `roadmap.md`: **V3** = "Proactive & Multi-Surface", een volwassenheidsfase. `architecture-v3.0.md`: **v3.0** = architectuurgeneratie. | Zie Q-016. Zelfde map, zelfde label, ongerelateerde betekenis. Eén van beide moet hernoemd worden; dat is een naamkeuze, geen inhoud. |
| D3 | Wat is SOUL, en waar woont hij? | `soul.md`: de constitutie staat in `services/gaia-api/identity/soul.md`, dit bestand is een overzicht. v3.0: SOUL is "een kleine, constante constitutionele laag". | Zie Q-004. Drie beschrijvingen, geen tegenstrijdigheid, maar ook geen overeenstemming. |
| D4 | Moet `vision.md` de statussen dragen? | A6: v3.0 statussen hebben nog geen plek in het fundament. | Besluit: fundament (wat Gaia weet) of architectuur (hoe het bewaard wordt), of beide met een expliciete verwijzing. Mag niet dubbel worden. |

---

## E. Wat hiermee *niet* is opgelost

Deze matrix lost niets op. Ze ordent. Wat nog ontbreekt:

1. **Geen enkele stelling is geverifieerd tegen code.** `rules.md` §1 zet
   huidige implementatie bovenaan de bewijsladder; deze matrix gebruikt
   uitsluitend documentatie, dus is bewijs niveau 5–6. Dat is per definitie
   onvoldoende om iets te herschrijven.
2. **Q-002 en Q-014 staan vóór alles.** Zolang onbekend is welk v3.0-document
   kanoniek is, is elke herkalibratie een gok op de juiste helft.
3. **B4 is de enige regel die om een besluit vraagt zonder code.**
4. De matrix zegt niets over `roadmap.md` §1 (V1 "calm desktop conversation"),
   `personality.md` §10 of de ontbrekende v3.0-paragraaf in `index.md` —
   alle drie te klein om hier op te nemen, alle drie Phase 2.

---

## F. Nieuwe open questions die deze matrix oplevert

Aanvullend op Q-001 t/m Q-014 in `open-questions.md`:

### Q-015 — Zit de v3.0-vocabulaire in het system prompt, en zo ja, waarom ontbreekt hij?

**Status:** Open

**Vraag:**
Wordt `foundation/lexicon.md` nog ingelezen als system-prompt-input, en bevat
het de v3.0-vocabulaire? Zo niet: hoe noemt Gaia dan `harnas`, `DecisionIQ`,
`HADES`, `Absolute Override` en de vier epistemische statussen?

**Bewijs:**
- `architecture/evolution.md` Milestone 2: de Foundation Engine laadt
  `soul.md`, `principles.md`, `lexicon.md`, `architecture.md` en `evolution.md`
  in `artifact.json`.
- `development/split-plan.md`: "Both web and desktop builds depend on `docs/`
  being present."
- `foundation/lexicon.md` bevat geen van de v3.0-termen.
- Geen van de v3.0-documenten noemt `lexicon.md` of de Foundation Engine.

**Waarom het telt:**
Als het lexicon nog in de prompt zit, is dit een live defect: Gaia mist de
woorden voor haar eigen epistemologie. Als het er niet meer in zit, is
`lexicon.md` een leeggevonden document en moet dat besloten worden. Beide
antwoorden vragen om een besluit; het uitstellen ervan is zelf een besluit.

**Vereist besluit:** Ja

### Q-016 — Twee documenten, één map, één label: is "V3" de roadmapfase of de architectuurgeneratie?

**Status:** Open

**Vraag:**
Wanneer iemand in deze repository "V3" schrijft, bedoelt die dan de
volwassenheidsfase uit `roadmap.md` of de architectuurgeneratie v3.0? En geldt
hetzelfde voor de nummering van toekomstige architectuurrevisies?

**Bewijs:**
- `foundation/roadmap.md` §1: "| **V3** | Proactive & Multi-Surface | Mature (proactive, trusted) | Welcome initiative + Gaia extends beyond desktop |"
- `architecture/architecture-v3.0.md` en `foundation/v3.0-foundation.md`: v3.0
  als architectuurversie, "Definitief (September 2026)".
- Beide staan nu in dezelfde map, `foundation/`.
- `foundation/roadmap.md` dateert van 2026-06-01 en kent geen v3.0-architectuur.

**Waarom het telt:**
Dit is geen cosmetiek. Een lezer die "V3" leest, bouwt de verkeerde
verwachting op over wat er wanneer ship. Het risico groeit zodra er een
roadmap-V4 of een architectuur-v4.0 bijkomt.

**Vereist besluit:** Ja
