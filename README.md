# AI Ticket Triage & Shadow Mode System

## Projectoverzicht
Dit project demonstreert een geautomatiseerd IT-service desk systeem gebouwd in n8n. Het systeem classificeert inkomende supporttickets met behulp van AI (OpenAI), past data-minimalisatie toe voor security, en genereert managementrapportages (Pareto-analyse) en operationele lijsten voor misroutes.

## Architectuur & Workflow
De workflow volgt deze stappen:
1.  **Intake**: Inlezen van tickets via HTTP/CSV.
2.  **Traceerbaarheid (P8)**: Elke run krijgt een unieke `run_id` (`{{ $execution.id }}`).
3.  **Security & Redaction (S4)**: PII (namen, e-mails, telefoonnummers) wordt geanonimiseerd voordat het naar de LLM gaat.
4.  **AI Triage (P1-P4)**: Een LLM classificeert het ticket op basis van `category_id`, `queue_id` en genereert een `draft_reply`.
5.  **Data-Minimalisatie (S1)**: Gevoelige ticket-inhoud wordt verwijderd uit de rapportage-output.
6.  **Rapportage (P5 & P7)**:
    -   `latest.md`: Een Pareto-overzicht (Top-N) van de meest voorkomende categorieën.
    -   `routed_tickets.json`: Een lijst met tickets die naar externe loketten (HR/Finance) moeten worden doorgezet.

## Gebruikte AI Prompts
De AI is geïnstrueerd om uitsluitend in JSON-formaat te antwoorden met de volgende velden:
- `category_id`: De specifieke probleemcategorie.
- `queue_id`: Het doel-loket (bijv. `Q_HR` of `Q_FINANCE_PROCUREMENT`).
- `confidence`: Betrouwbaarheidsscore van de classificatie.
- `draft_reply`: Een professioneel antwoord in de taal van de gebruiker.

## Security & Privacy (Shadow Mode)
- **S1 (Minimale data)**: In de finale exports zijn de originele ticket-bodies verwijderd. Alleen noodzakelijke metadata blijft bewaard.
- **S4 (Redaction)**: PII wordt proactief vervangen door placeholders om te voorkomen dat persoonsgegevens worden gedeeld met externe AI-modellen.

## Hoe te gebruiken
1. Importeer `workflow-export.json` in n8n.
2. Voer de workflow uit.
3. Download de gegenereerde bestanden uit de laatste nodes en plaats deze in de `/reports` map.

---
*Ontwikkeld voor de AI Service Desk opdracht - Mei 2026*
