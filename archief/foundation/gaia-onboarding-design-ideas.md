---
pulse-tags: ["onboarding", "ux-design", "trust-ladder", "dogfooding", "hindsight"]
pulse-connections: [{"path": "architecture/decisions/chronicle-hindsight-considerations.md", "relation": "relates-to", "why": "Builds on the Chronicle and Hindsight trade-offs established in the architectural decisions document."}, {"path": "foundation/personality.md", "relation": "supports", "why": "Applies Gaia's calm, non-intrusive personality traits and earned initiative to the onboarding flow."}]
---

# Gaia Onboarding — Ontwerpideeën

> Status: ideeënfase · 19 september 2026
> Context: Gaia's karakter (kalm, eerlijk, geen vleierij, "rust is een feature"), trust-gated roadmap, Hindsight/Chronicle-architectuur zoals vastgelegd in het afwegingen-document.

## Kerninzicht: twee trajecten

| | Bo (eerste gebruiker) | Nieuwe klanten |
|---|---|---|
| Startpositie | Heeft archief (AI-chats e.d.) | Koude start, niets |
| Dag 1 | Inlezen, niet kennismaken | Aankomen, geen wizard |
| Geheugen | Gezaaid uit verleden | Groeit vanaf nul |
| Karakter | Straks vol begrip | Draagt de eerste weken (karakter vóór kennis) |
| Flow | Eenmalig, géén template | Schaalbaar ontwerp |
| Volgorde | Eérst bouwen | Pas ontwerpen na leeropbrengst Bo-traject |

## Bo-traject: "aankomen met baggage"

1. **Dag 1 = inlezen.** Chronicle-archief importeren, Hindsight erover laten reflecteren, Gaia koppelt terug: *"Dit is wat ik uit je verleden begrijp — klopt dat?"*
2. **Validatie-lus.** Bo bevestigt/corrigeert → eerste echte Absolute Override-momenten; het Vertrouwensdomein wordt direct in de praktijk getest.
3. **Dogfooding.** De import loopt door de eigen ingestie-architectuur (streams → gateway → ruisfilter → statusmarkering) — de eerste testcase van het systeem zelf.
4. Import-flow niet exposen aan klanten; dit is eenmalig gereedschap.

## Nieuwe klanten: de relatie vervangt het geheugen

Vroeg in de relatie heeft Gaia geen data — alleen hoe ze zich gedraagt.

### Principes

1. **Geen wizard — een aankomst.** De eerste gesprekken zíjn de onboarding zonder er zo uit te zien. Eerste pagina = eerste vraag van Gaia, geen formulier: *"Ik ben er. Ik weet nog niets over je — en dat is oké. Waar zullen we beginnen?"*
2. **Stilte is een antwoord.** Alleen rondkijken is legitiem. Geen duwende tooltips.
3. **Karakter vóór kennis.** Eerste indruk: "iemand met wie ik wil praten", niet "ze weet veel over me".
4. **Geen intake-theater.** Geen "vertel me over jezelf"-vragenlijst; begrip als bijproduct van échte gesprekken (week-1 werkprobleem leert haar meer dan een profielformulier).
5. **Begrip groeit zichtbaar.** "Wat ik understand"-view (incl. "Let go") laat wekelijks zien dat er iets nieuws begrepen wordt; bewijst ook dat vergeten écht vergeten is.
6. **Eerlijkheid als verwachtingsmanagement.** Eén kalm moment: *"Ik ken je nog niet — dat duurt even, en zo werk ik."* Selfselectie is een feature; wie dit niet accepteert is geen Gaia-gebruiker.
7. **Nederlands als thuistaal vanaf regel één**; geen jargon om vertrouwd te lijken; "ik weet dit nog niet" als sterk vertrouwenssignaal.

### Trust-ladder (earned, tiered, reversible)

| Fase | Gaia doet | Gebruiker leert |
|---|---|---|
| **1. Aanwezig** | Praat; bewaart alleen expliciet gedeelde dingen | Ze luistert, oordeelt niet |
| **2. Spiegel** | Reflecteert terug wat ze begrijpt, afwijsbaar | Haar begrip is inzichtelijk én corrigeerbaar — eerste terugspiegeling die waar voelt = aankoop-rechtvaardiging |
| **3. Initiatief (verdiend)** | Stelt iets voor / herinnert iets | Nuttig zonder opdringerig |

Initiatief nooit in week 1.

### Randvoorwaarden uit de architectuur

- **SOUL-constitutie als onboarding-grens**: wat Gaia vraagt moet binnen haar waarden vallen (geen persoonlijkheidsquiz).
- **Privacy-moment**: één rustige uitleg van wat ze bewaart en wat niet, gekoppeld aan de Memory-view. Geen cookie-banner-energie.
- **Hindsight-afhankelijkheid**: fase-1-begrip draait op expliciet gedeelde informatie (`observation`); reflecties vallen onder de Hindsight→Logos-adapterregel (altijd `interpretation` tot onderbouwd).

### Meetpunt voor succes

Onboarding is geslaagd op het moment dat de gebruiker voor het eerst een reflectie van Gaia **corrigeert én die correctie blijft staan**. Niet tijd-in-app, niet aantal berichten.

## Volgorde van bouwen

1. Bo-traject (archief-import + validatie-lus) — levert ook de eerste architectuur-testcase.
2. Leren van wat Gaia na tientallen gesprekken waard is.
3. Pas dan het klant-traject ontwerpen.