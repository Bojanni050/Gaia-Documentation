---
pulse-tags: ["logos", "cognition", "memory", "skills", "hermes", "architecture"]
pulse-connections: [{"path": "architecture/architecture.md", "relation": "extends", "why": "Vertaalt Hermés' leermechanismen naar Gaia's bestaande Logos/Hindsight/Foundation-pijplijn zonder de harnasfilosofie te breken."}, {"path": "architecture/principles.md", "relation": "supports", "why": "Onderbouwt 'Understanding is earned' met concrete mechanismen: nudge-intervallen, procedureel geheugen en zelfherziening."}, {"path": "architecture/capture-chronicle.md", "relation": "supports", "why": "De Chronicle/Hindsight-gedachte krijgt met FTS-doorzoekbaarheid een concreet technisch patroon uit hermes_state_fts.py."}]
---

# Proposal: Wat Gaia kan leren van Hermes Agent

**Status:** Voorstel (oktober 2026)
**Type:** Onderzoek & Ontwerpvoorstel
**Bron:** [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) — technische deep dive op de codebase
**Auteur:** Gaia Core Team

---

## 1. Aanleiding

Hermes Agent (Nous Research) draait al achter Gaia's `gaia-hermes-proxy` als inferentie-infrastructuur. Deze deep dive onderzoekt wat Gaia kan leren van Hermés' *eigen* feature-ontwerp — bovenop de modelrouting die al loopt. De conclusie vooraf: drie mechanismen zijn direct overneembaar, twee principes zijn стратегisch waardevol, en een deel moet Gaia juist niet overnemen.

## 2. Wat Hermes technisch doet (bronanalyse)

### 2.1 Skills als procedureel geheugen (`tools/skill_manager_tool.py`, `skills/`)

Hermes splitst geheugen in twee soorten:

- **Declaratief:** `MEMORY.md` (~2200 chars) en `USER.md` (~1375 chars) — feiten over de wereld en de gebruiker. Bewust kleine karakterbudgetten zodat de prompt niet opzwelt.
- **Procedureel:** *skills* — herbruikbare "hoe doe ik X"-instructies in `~/.hermes/skills/[categorie]/<skill>/SKILL.md`, met frontmatter (`name`, `version`, `platforms`) en optionele `references/`, `templates/`, `scripts/`-mappen. Compatibel met de [agentskills.io](https://agentskills.io)-standaard.

De cruciale eigenschap: de agent **schrijft en herzien deze skills zelf** via een `skill_manage`-tool, tijdens en na taken. Er is een opt-in security-scan voor agent-gegenereerde skills en "background review"-guards (lees-voor-schrijf, delete-bescherming, pinned skills). Skills verbeteren dus *tijdens gebruik* — dat is de "closed learning loop" uit de README.

### 2.2 Nudge-intervallen (`agent/agent_init.py:1215-1288`)

- `_memory_nudge_interval` (default 10 beurten): een periodieke systeem-herinnering om geleerde kennis in `MEMORY.md` te persisteren.
- `_skill_nudge_interval` (`skills.creation_nudge_interval`, default 10): idem, voor het aanmaken van skills na complexe taken.

Eenvoudig mechanisme, groot effect: kennisgroei zonder het gesprek te verstoren. De nudge is asynchroon en zwevend, niet synchroon in de beurt.

### 2.3 Sessie-zoekbaarheid (`hermes_state_fts.py`)

Full-text search over álle sessies via SQLite FTS5 (external-content index over een view die tool-rijen uitsluit), trigram-tokenizer plus een zelfgeschreven CJK-bigram-tokenizer (~250 regels, geen dependencies) voor Aziatische zoektermen. Corruptie-veilige trigger-gating zodat een stale index nooit writes breekt. LLM-samenvatting bovenop de resultaten geeft cross-session recall.

### 2.4 Pluggable geheugenproviders (`agent/memory_manager.py`)

De ingebouwde bestandsgeheugens kunnen worden uitgebreid met precies **één** externe provider tegelijk (byterover, retaindb) — expliciet om tool-schema-bloat en conflicterende backends te voorkomen. Egress-scrubbing van secrets gebeurt altijd, ook wanneer lokale redactie uitstaat. Pre-compress checkpointsborgen dat contextcompressie nooit geheugenverlies veroorzaakt.

### 2.5 Twee architectonische invarianten (uit `AGENTS.md`)

1. **"Per-conversation prompt caching is sacred."** Niets muteren wat de cached prefix breekt (geheugen herladen, toolsets wisselen mid-gesprek) — het verveelvoudigt de kosten. Één uitzondering: compressie.
2. **"The core is a narrow waist; capability lives at the edges."** Nieuwe capaciteit via een footprint-ladder: bestaande code uitbreiden → CLI+skill → service-gated tool → plugin → MCP-server → pas dan een core-tool.

## 3. Vertaling naar Gaia

### 3.1 Direct overneembaar (klein, hoog rendement)

| # | Voorstel | Hermes-mechanisme | Gaia-plaats |
|---|----------|-------------------|-------------|
| 1 | **Nudge-interval in Logos' achtergrondreflectie** | `_memory_nudge_interval` | Logos draait al asynchroon achtergrondreflectie; voeg een periodieke prompt toe die vaststelt welke kennis _nog niet_ gepersisteerd is in Hindsight |
| 2 | **Procedureel geheugen als first-class citizen** | Skills (`SKILL.md`-structuur) | services/cognition kristalliseert hypotheses/patterns tot herbruikbare procedures — zelfde formaat (markdown + frontmatter), opgeslagen in de derived store |
| 3 | **Zelfherziening met guards** | background review + lees-voor-schrijf | Gaia's `/cognition/*` review-surface is er al; uitbreiden zodat Gaia haar eigen procedures herziet na gebruik, met een review-stap eromheen |

Deze drie passen binnen de bestaande pijplijn `Chronicle → Hindsight/Insight → Logos` en voegen er een **vierde fase** aan toe:

$$\text{Chronicle} \longrightarrow \text{Hindsight} \longrightarrow \text{Logos} \longrightarrow \text{Procedures (nieuw)}$$

### 3.2 Strategisch waardevol (groter, later)

- **Doorzoekbare eigen geschiedenis.** Het idee leeft al in Chronicle-Gaia (embedding + semantisch zoeken). Het Hermes-patroon (FTS5 external-content index, tool-rijen uitgesloten) is een lichter en robuuster alternatief voor de Foundation-raw-laag. Complementair aan embeddings: FTS voor exacte recall, embeddings voor betekenis.
- **De twee invarianten als expliciete ontwerpregels** in `docs/architecture.md`. Vooral de eerste ("prompt caching is sacred") is voor Gaia's turn-orchestration direct relevant: de huidige regels (SOUL server-side, contracts) beschermen identiteit; caching-discipline beschermt het *bedrag*.
- **Natuurlijke-taal automatisering** op schema (Hermes' cron): Kairos-episodes en Eleos-check-ins plannen in gewone taal — kleine stap, beide diensten bestaan al als feature-flags.

### 3.3 Wat Gaia juist níét moet overnemen

- **Terminal-first UX en slash-commands** — Gaia is conversation-first met identiteit als kern; Hermés' TUI-filosofie past daar niet.
- **Brede tooling-surface (40+ tools, "everything everywhere")** — Gaia's kracht is beperking: technische acties pas via de desktop-confirmation-flow, keuze en betekenis server-side. Hermés' omgekeerde filosofie zou dat verdunnen.
- **"Any model, no lock-in" als identiteit** — voor Gaia is het model een inwisselbaar inferentie-orgaan, geen verkoopargument. Hermes is en blijft infrastructuur (de proxy).

## 4. Voorstel tot actie

1. **Korte termijn:** voeg nudge-gedrag toe aan Logos' achtergrondreflectie (config: `reflection.nudge_interval`, default 10 beurten). Kleinste mogelijke stap, onmiddellijk leereffect.
2. **Middellang:** definieer het **procedureformaat** (markdown + frontmatter, naar het agentskills.io-voorbeeld) en laat services/cognition hypotheses/patterns daarin kristalliseren — met epistemische status (onbewezen → bevestigd) conform de bestaande epistemic-status-regels.
3. **Langer:** FTS-doorzoekbaarheid over de Foundation-raw-laag; evaluatie tegen de bestaande embedding-aanpak uit Chronicle.
4. **Doorlopend:** neem de twee invarianten op als ontwerpregels in de architecture-documentatie.

## 5. Risico's

- **Procedurevervuiling:** zelfgeschreven procedures kunnen fouten versterken. Mitigatie: de bestaande epistemic-status-regels (brondata alleen uit Chronicle) en een review-stap in `/cognition/*` vóór een procedure van "onbewezen" naar "bevestigd" gaat.
- **Contextgewicht:** te veel skills/persistent geheugen zwelt de prompt op. Mitigatie: Hermés' karakterbudgetten overnemen; per procedure een korte beschrijving, volledige inhoud alleen on demand.
- **Filosofie-drift:** de leer-loop moet de harnasfilosofie dienen, niet Hermes' generiek-agent-karakter kopiëren. Dit proposal kiest daarom bewust voor integratie in Logos/Cognition in plaats van een losse skills-map.
