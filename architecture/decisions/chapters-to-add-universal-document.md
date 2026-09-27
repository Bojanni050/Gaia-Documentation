# Toe te voegen hoofdstukken — Universal\_Bojan\_Foundation.md

Dit zijn drie nieuwe hoofdstukken (elk met een conceptueel en, waar relevant, een technisch deel) die ontbreken in de huidige versie. Ze zijn geschreven om direct in het bestaande document te worden ingevoegd, op de aangegeven plek, zonder de bestaande hoofdstuknummering te verstoren. Waar het document al iets oplost (status-markering, Bias-Analyzer, CTM) is dat hier niet herhaald, maar aangevuld op precies het punt waar het nog een gat laat.

---

## In te voegen na Hoofdstuk 41 (Begrip, werkhypothesen en continuïteit)

## Hoofdstuk 41a – Versiegeschiedenis van Werkhypothesen

Hoofdstuk 41 stelt terecht dat een werkhypothese mag worden bijgesteld, genuanceerd of vervangen zonder dat dit als fout wordt gezien. Dat principe houdt echter alleen stand wanneer het onderscheid tussen *herziening* en *vergeten* zichtbaar blijft. Zonder dat onderscheid is een bijgestelde werkhypothese, vanuit het perspectief van de maker, niet te onderscheiden van een systeem dat simpelweg iets anders is gaan beweren. Dat zou dezelfde ontologische spanning reproduceren die elders in dit document al is uitgesloten voor de verhouding tussen vertaler en auteur: iets dat verandert zonder herkenbaar te blijven, kan zich niet langer verantwoorden als hetzelfde begrip.

De architectuur moet daarom niet alleen toestaan dat een werkhypothese verandert, maar vastleggen *dat* zij is veranderd, *waaruit* de verandering volgde, en *wanneer*. Dit is geen technisch logboek dat los van de creatieve samenwerking bestaat; het is de vorm waarin "voorlopig" een controleerbare status blijft in plaats van een vrijblijvende disclaimer. Een maker moet, wanneer gewenst, kunnen terugzien hoe een idee zich heeft ontwikkeld — niet als technische historie, maar als creatieve geschiedenis van het eigen werk.

Deze verantwoordelijkheid ligt bij het Hypothesedomein, dat al de status-markering van informatie bewaakt richting het Vertaaldomein (hoofdstuk 49), en bij het Continuïteitsdomein, dat al betekenisvolle wendingen bewaart voor toekomstige sessies (hoofdstuk 35, 41). Versiegeschiedenis is geen nieuw domein; het is de uitbreiding van hun bestaande verantwoordelijkheid over de tijd heen, niet alleen over de domeingrenzen heen.

---

## In te voegen na Hoofdstuk 67 (Vertrouwensdomein: technische handhaving)

## Hoofdstuk 67a – Technische handhaving van versiegeschiedenis

Het bestaande `IntentieRecord`\-type (hoofdstuk 67\) markeert de status van informatie, maar niet haar herkomst in de tijd. Dat wordt hier aangevuld:

interface IntentieRecord {

  inhoud: string;

  status: StatusMarkering;

  bron: string\[\];

  platform\_bias\_warning: string\[\];

  feedback\_lus: FeedbackRecord | null;

  confidence?: number;

  // Nieuw: versiegeschiedenis

  voorganger\_id?: string;       // verwijzing naar het IntentieRecord dat dit vervangt

  wijzigingsaanleiding: string; // observatie of keuze die de herziening veroorzaakte

  vervangen\_op?: string;        // ISO-tijdstip

}

Wanneer een `IntentieRecord` een eerder record vervangt, blijft het eerdere record bewaard met een verwijzing ernaartoe (`voorganger_id`), in plaats van te worden overschreven. Het Vertrouwensdomein controleert bij iedere overdracht niet alleen de status-markering (hoofdstuk 67), maar ook of een gewijzigd record zijn `wijzigingsaanleiding` draagt. Een record zonder wijzigingsaanleiding mag niet als herziening worden gepresenteerd — het is dan een nieuw record, geen opvolger.

De Maker Persona-weergave (hoofdstuk 65\) kan deze keten op verzoek tonen als leesbare geschiedenis: "dit inzicht is drie keer bijgesteld, laatst op basis van \[wijzigingsaanleiding\]." Dit blijft optioneel voor de maker om te bekijken, niet een verplicht onderdeel van elk gesprek.

---

## In te voegen bij Hoofdstuk 52 (Vertaaldomein) en Hoofdstuk 47/48 (Begripsdomein)

## Hoofdstuk 52a – Culturele grounding bij de overdracht naar het Vertaaldomein

De Bias-Analyzer (hoofdstuk 52, 66, 68\) berekent de mediumbias van het gekozen platform en signaleert dit richting het Keuzedomein. Dat lost een deel van het probleem op — de bias van het platform zelf — maar niet de vraag of het referentiekader van de maker onderweg is blijven bestaan. Een vertaling kan technisch geslaagd zijn en toch het culturele frame van de maker hebben vervangen door de dichtstbijzijnde conventie die het platform wél goed kent.

Daarom draagt de overdracht van het Begripsdomein naar het Vertaaldomein een extra, expliciete controle: voordat een werkhypothese het Vertaaldomein bereikt, moet zichtbaar zijn of het referentiekader van de maker behouden is gebleven, of dat de vertaling heeft moeten uitwijken naar een dominante conventie omdat er geen preciezer downstream-equivalent bestond. Dit is geen aparte component naast de Bias-Analyzer, maar een aanvullend controlepunt dat vóór de vertaling plaatsvindt in plaats van erna: de Bias-Analyzer signaleert bias van het platform; deze controle signaleert verlies van het frame van de maker.

Dit voorkomt niet dat afvlakking ooit gebeurt — geen enkele architectuur kan dat garanderen — maar het maakt zichtbaar wanneer het gebeurt, en aan wie: aan de maker, niet alleen in een achterliggend logboek. Het geeft de SMART-achtige vertaalstap tussen Hypothesedomein en Vertaaldomein (hoofdstuk 42\) een tweede toetssteen naast technische haalbaarheid: een parameterset is niet pas "opgelost" omdat zij geldig is voor het platform, maar pas wanneer ook is nagegaan of zij het oorspronkelijke culturele frame heeft bewaard of afgevlakt.

---

## In te voegen bij Hoofdstuk 50 (Verkennings- en Keuzedomein)

## Hoofdstuk 50a – Exit-criteria voor het Verkenningsdomein

Hoofdstuk 27 en 50 stellen terecht dat het Verkenningsdomein ambiguïteit niet voortijdig mag reduceren tot één verklaring. Dat principe beschermt tegen te vroege zekerheid, maar beschermt niet vanzelf tegen het omgekeerde risico: eindeloos verkennen zonder dat de maker ooit hoeft — of mag — kiezen. Zonder een expliciet signaal voor "voldoende verkend" is gestructureerde onzekerheid van binnenuit niet te onderscheiden van besluiteloosheid.

Het Verkenningsdomein moet daarom, in samenspraak met de maker, een expliciet en herzienbaar criterium vastleggen voor wanneer verkenning voldoende is geweest. Dat criterium kan drie vormen aannemen:

- **Maker-geïnitieerd:** de maker geeft zelf aan dat hij nu wil kiezen.  
- **Patroon-geïnitieerd:** dezelfde werkhypothese is onafhankelijk van elkaar in meerdere verkenningen bevestigd.  
- **Tijd-gebonden:** de maker heeft voor deze sessie een doel gesteld dat om convergentie vraagt.

Welke vorm ook gekozen wordt, het criterium moet zichtbaar zijn vóórdat de verkenning begint, niet pas stilzwijgend worden toegepast halverwege. Dit maakt de overgang van Verkennings- naar Keuzedomein (hoofdstuk 31, 50\) — die het document al als vanzelfsprekend behandelt — tot een expliciete stap in plaats van een impliciete. Het lost daarmee het risico op besluiteloosheid op zonder de vroege, geforceerde zekerheid terug te brengen die hoofdstuk 27 juist afwijst: het exit-criterium wordt door de maker gekozen, niet als systeemstandaard opgelegd.

---

## Waarom dit drie losse toevoegingen zijn, geen apart hoofdstuk-cluster

Elk van deze drie punten hoort thuis bij een bestaand hoofdstuk, niet naast het document als geheel. Versiegeschiedenis is de tijd-dimensie van wat het Hypothesedomein al doet. Culturele grounding is een extra controlepunt op een overdracht die het Vertaaldomein al kent. Exit-criteria formaliseren een overgang die het Verkennings-/Keuzedomein al veronderstelt. Geen van de drie introduceert een nieuw domein of een nieuwe filosofie; ze maken alleen zichtbaar en toetsbaar wat het document tot nu toe als vanzelfsprekend liet.  
