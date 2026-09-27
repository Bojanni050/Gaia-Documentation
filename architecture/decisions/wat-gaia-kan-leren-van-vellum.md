# Wat Gaia kan leren van Vellum

*Concrete, architecturale leerpunten uit vellum-ai/vellum-assistant (MIT, 856 sterren), afgezet tegen Gaia's huidige opzet (Logos, Hindsight, SOUL, Desktop/Web als representaties). Geen 1-op-1 overname-advies — Vellum en Gaia hebben deels andere doelen — maar concrete ideeën die de moeite van overwegen waard zijn.*

---

## 1\. Een expliciete "NOW"-laag naast langetermijngeheugen

**Wat Vellum doet:** naast de acht geheugentypen houdt de assistent een `NOW.md`\-scratchpad bij: actuele focus, lopende threads, wat er nu speelt — los van het langetermijngeheugen en los van SOUL (identiteit).

**Wat Gaia nu heeft:** Hindsight's Mental Models komen dichtbij (levende documenten die auto-verversen), maar zijn gericht op *geleerd inzicht*, niet op *actuele status*. Er is geen apart, lichtgewicht "waar ben ik nu mee bezig"-document.

**Aanbeveling:** een klein, snel-te-lezen `NOW`\-object (per gesprek of per dag ververst) dat Logos vóór elke intentclassificatie kan raadplegen — goedkoper dan een Hindsight-recall, en houdt de "wat speelt er vandaag"-context scherp zonder de mental-model-laag te vervuilen met vluchtige status.

---

## 2\. Proactiviteit als terugkerend zelf-onderzoek, niet als los feature

**Wat Vellum doet:** elk uur herleest de assistent zijn eigen notities, checkt op onafgeronde taken of naderende deadlines, en stuurt ongevraagd een bericht via het juiste kanaal — zonder een actief gesprek te onderbreken.

**Wat Gaia nu heeft:** proactiviteit staat nog niet als architectuurstuk vastgelegd; de focus lag tot nu toe op reactieve conversatie plus TTS-verkenning.

**Aanbeveling:** een terugkerende "self-check"-taak (bijv. via een Hermes-cronjob) die Hindsight's directives \+ mental models bevraagt op openstaande zaken, met een expliciete regel wélk kanaal (Desktop-notificatie? Telegram? niets als je in gesprek bent) — dit is een concreet, klein bouwbaar stuk dat direct op je bestaande stack aansluit.

---

## 3\. SOUL die zichzelf schrijft, niet alleen wordt ingesteld

**Wat Vellum doet:** tijdens onboarding *observeert* de assistent hoe je communiceert en schrijft zélf zijn personality-bestanden — SOUL.md is dus een levend, zelf-bijgewerkt document, geen statische config die jij eenmalig invult.

**Wat Gaia nu heeft:** SOUL bewaakt identiteit, maar er is (voor zover vastgelegd) geen mechanisme waarbij SOUL zichzelf herschrijft op basis van waargenomen interactiepatronen.

**Aanbeveling:** een periodieke reflect-taak (Hindsight kan dit al: `reflect` genereert nieuwe inzichten uit memories) die SOUL specifiek voedt met communicatiestijl-observaties — een brug tussen wat Hindsight al kan en wat SOUL nog passief is.

---

## 4\. Actor-identiteit als aparte, apart afdwingbare laag

**Wat Vellum doet:** identiteit van wie er praat (guardian, trusted, unknown) wordt één keer bepaald en overal gehandhaafd; onbekende actoren kunnen sowieso geen geheugen lezen of tools triggeren — fail-closed by design, los van waar het verzoek vandaan komt.

**Wat Gaia nu heeft:** vertrouwen zit vooral op netwerkniveau (Tailscale trust-domain) — wie op het tailnet zit, wordt impliciet vertrouwd. Er is geen aparte actor-resolutielaag daarbovenop.

**Aanbeveling:** dit is de moeite van overwegen waard als Gaia ooit toegankelijk wordt voor iemand anders dan jijzelf (bijv. gedeeld gezinsgebruik) — netwerktoegang alléén onderscheidt dan niet wie er precies praat. Voor nu, als single-user systeem, is dit minder urgent, maar wel iets om als ontwerpprincipe vast te leggen vóórdat het nodig is.

---

## 5\. Skills als geformaliseerd, sandboxed pakketformaat

**Wat Vellum doet:** plugins volgen een vast format — een `SKILL.md` (instructies) plus `TOOLS.json` (toolschema's) — sandboxed, installeerbaar uit een catalogus of los in de workspace gezet.

**Wat Gaia nu heeft:** Hermes, Melodiq en SongCompanion zijn "instrumenten" die Gaia kan inzetten, maar zonder vastgelegd, herbruikbaar manifestformaat — elke integratie lijkt vooralsnog los gebouwd.

**Aanbeveling:** een lichte conventie (zelfs alleen intern) voor hoe een nieuw instrument zich aan Gaia voorstelt — naam, wat het doet, welke tools het toevoegt — bespaart je bij elk volgend instrument opnieuw uitvogelen hoe het aan te haken.

---

## 6\. Multi-provider-abstractie als resilience, niet als keuzevrijheid

**Wat Vellum doet:** werkt met Anthropic, OpenAI, Gemini, Fireworks, OpenRouter, MiniMax en lokale Ollama-modellen door elkaar; embeddings draaien standaard lokaal (ONNX) met automatische fallback naar cloud.

**Wat Gaia nu heeft:** vaste modeltoewijzingen per component (ministral-8b voor intent, mistral-large voor conversatie, deepseek-v4-flash voor Hindsight) — functioneel, maar zonder fallback als één provider hikt. Je hebt dit al één keer gevoeld: de HTTP 429's op Hindsight's `reflect_tool_call` kwamen precies hierdoor.

**Aanbeveling:** geen noodzaak om meteen multi-provider te worden, maar een eenvoudige fallback-regel per component (bijv. "als model X 3x achter elkaar faalt, val terug op Y") had die storing kunnen voorkomen zonder de architectuur overhoop te gooien.

---

## 7\. Granulaire toestemming per actie, niet blanket-permissions

**Wat Vellum doet:** elke actie is permission-gated met drie standen: eenmalig toestaan, 10 minuten toestaan, of altijd toestaan.

**Wat Gaia nu heeft:** Tauri's capability-systeem regelt dit op OS-niveau, maar of dat ook per-actie, tijdelijke toestemming aan de gebruikerskant kent (in plaats van alles-of-niets bij installatie) is niet vastgelegd.

**Aanbeveling:** de moeite waard om te checken of Tauri's permission-model dit al ondersteunt — zo niet, is dit een kleine UX-laag die veel vertrouwen oplevert zodra Gaia meer autonome acties gaat uitvoeren (denk: bestanden aanraken, mails versturen).

---

## Wat je waarschijnlijk *niet* hoeft over te nemen

- **De 8-type geheugentaxonomie** — dit is een andere filosofie dan Hindsight's biomimetische wereld/ervaring/mentaal-model-indeling, niet per se een verbetering. Hindsight's reflect-gedreven aanpak past beter bij Logos' intent-eerst-redeneren-later-opzet.  
- **Cloud-first met self-host als optie** — precies andersom dan jouw bewuste local-first-met-selectieve-sync-keuze; geen reden om die om te draaien.

---

## Samenvattend

De grootste winst zit niet in geheugenarchitectuur (daar is Hindsight's aanpak al minstens zo doordacht), maar in drie operationele lagen die Gaia nog mist: een **actuele-status-laag** (NOW), een **terugkerend proactiviteitsmechanisme**, en **resilience tegen provider-uitval**. Alle drie zijn relatief klein te bouwen bovenop wat je al hebt staan.

&nbsp;