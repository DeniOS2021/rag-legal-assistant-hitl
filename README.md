# RAG-Rechtsassistent mit Freigabe durch Menschen

**[DE](#deutsch) · [EN](#english)** · Fallstudie auf [denpilot.de](https://denpilot.de/#projekt-rag-agent)

> Bereinigte Kopien: ohne Zugangsdaten, IDs und Kontaktdaten. Fallstudien mit fiktiven Unternehmen, keine Kundendaten.

## Deutsch

### Aufgabe
Mitarbeitende eines IT-Dienstleisters fragen die Rechtsabteilung immer wieder nach denselben internen Regeln – etwa Unterschriftsgrenzen für Verträge. Bekannte Fragen sollen sofort mit Quelle beantwortet, Handlungsrelevantes freigegeben und Unbekanntes eskaliert werden; die Wissensbasis soll wachsen.

### Architektur
3 n8n-Workflows (54 Knoten): Agent mit RAG-Tool und strukturierter Ausgabe, Upload des Handbuchs, Fehler-Workflow. Stack: n8n · gpt-4o via OpenRouter · Qdrant · text-embedding-3-small · Telegram Send-and-Wait · Gmail · Google Sheets. Diagramm: [`docs/architecture.md`](docs/architecture.md).

### Wer macht was
| KI | Code | Mensch |
|---|---|---|
| Frage verstehen, passende Stelle finden, Antwort formulieren, Sicherheit einschätzen | Drei Ausgänge, Fallback immer Eskalation, Duplikatprüfung ≥ 0,92 vor dem Speichern | Freigabe oder Ablehnung mit Begründung; Antwort auf unbekannte Fragen |

### Zuverlässigkeit
- Fehler in der Weiterleitung führen immer zur Eskalation, nie zu einer ungeprüften Antwort.
- Zeitlimit beim Warten auf die Rechtsabteilung; der Grund einer Ablehnung wird protokolliert.
- Eigener Fehler-Workflow meldet Ausfälle sofort.
- Eine nur teilweise passende Stelle gilt nicht als Bestätigung.

### DSGVO / KI-Verordnung
Bewusste Grenze: Der Agent informiert über interne Regeln, entscheidet aber nichts über einzelne Mitarbeitende. Protokolle dürfen nicht zur Leistungsbewertung genutzt werden – sonst Hochrisiko nach Anhang III KI-Verordnung und Mitbestimmung nach § 87 BetrVG.

### Ergebnisse
Alle **6 Testfälle im Live-Betrieb bestanden**: Antwort, Freigabe, Ablehnung mit Begründung, Eskalation, Guardrail, Zeitlimit.

### Demo starten
1. n8n (self-hosted, aktuelle 1.x/2.x) starten.
2. **Neuen, leeren** Workflow anlegen → Menü **⋯ → Import from File** → JSON aus `workflows/` wählen.
   Wichtig: Import in einen bereits gefüllten Workflow fügt Knoten hinzu, statt ihn zu ersetzen.
3. Zugangsdaten (Credentials) in n8n anlegen und an den markierten Knoten auswählen – im JSON sind sie bewusst leer.
4. Platzhalter ersetzen (siehe `.env.example`): `YOUR_LOCAL_HOST`, `YOUR_CHAT_ID`, `YOUR_SHEET_ID` usw.
5. Erst testen, dann aktivieren. Alle Workflows sind im Export **inaktiv**.
6. Qdrant-Collection `legal` anlegen, eigenes Handbuch über `P9_Legal-Guide-Upload` hochladen.
7. In den Workflow-Einstellungen von `P9_Legal-Navigator_MAIN` **Error workflow** = `P9_Error-Handler` setzen.

### Grenzen
- Handbuch und Testdaten sind nicht enthalten (Kursmaterial).
- Das Modell läuft in der Cloud; für echte Rechtsdokumente ist ein Auftragsverarbeitungsvertrag bzw. ein lokales Modell nötig.

---

## English

### Task
Employees of an IT service provider keep asking the legal team about the same internal rules – e.g. signing limits for contracts. Known questions should be answered immediately with a source, action-relevant ones approved, unknown ones escalated – and the knowledge base should grow.

### Architecture
3 n8n workflows (54 nodes): agent with RAG tool and structured output, handbook upload, error workflow. Stack: n8n · gpt-4o via OpenRouter · Qdrant · text-embedding-3-small · Telegram send-and-wait · Gmail · Google Sheets. Diagram: [`docs/architecture.md`](docs/architecture.md).

### Who does what
| AI | Code | Human |
|---|---|---|
| Understand the question, find the passage, write the answer, estimate confidence | Three outcomes, fallback always escalation, duplicate check ≥ 0.92 before saving | Approve or reject with a reason; answer unknown questions |

### Reliability
- Routing errors always lead to escalation, never to an unchecked answer.
- Timeout while waiting for legal; the reason for a rejection is logged.
- A dedicated error workflow reports failures immediately.
- A partially matching passage does not count as confirmation.

### GDPR / AI Act
Deliberate boundary: the agent informs about internal rules but decides nothing about individual employees. Logs must not be used for performance evaluation – otherwise high-risk under Annex III AI Act and co-determination under § 87 BetrVG.

### Results
All **6 test cases passed in live operation**: answer, approval, rejection with reason, escalation, guardrail, timeout.

### Run the demo
1. Start n8n (self-hosted, current 1.x/2.x).
2. Create a **new, empty** workflow → menu **⋯ → Import from File** → pick a JSON from `workflows/`.
   Note: importing into a non-empty workflow adds nodes instead of replacing them.
3. Create the credentials in n8n and select them on the marked nodes – they are deliberately empty in the JSON.
4. Replace the placeholders (see `.env.example`): `YOUR_LOCAL_HOST`, `YOUR_CHAT_ID`, `YOUR_SHEET_ID`, etc.
5. Test first, then activate. All workflows are exported **inactive**.
6. Create the Qdrant collection `legal`, upload your own handbook via `P9_Legal-Guide-Upload`.
7. In the workflow settings of `P9_Legal-Navigator_MAIN` set **Error workflow** = `P9_Error-Handler`.

### Limitations
- Handbook and test data are not included (course material).
- The model runs in the cloud; real legal documents need a data processing agreement or a local model.

---
Cleaned copies: no credentials, IDs or contact details. Case studies with fictitious companies, no customer data. · Licence: MIT · [LinkedIn](https://www.linkedin.com/in/denys-kopyl-ai-automation/)
