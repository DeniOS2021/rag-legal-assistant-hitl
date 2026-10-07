# Architektur / Architecture

```mermaid
flowchart LR
  Q[Frage · Telegram] --> AG[Agent · strukturierte Ausgabe<br/>RAG-Tool: Qdrant]
  AG --> SW{Sicherheit · Handlungsbedarf}
  SW -- A --> A[Antwort mit Quelle] --> LA[(Protokoll Antworten)]
  SW -- B --> B[Freigabe durch Rechtsabteilung<br/>Send-and-Wait]:::human
  B -- Approve --> MAIL[E-Mail senden] --> LB[(Protokoll Freigaben)]
  B -- Reject --> WHY[Begründung erfragen] --> LB
  SW -- C / Fehler --> C[Eskalation an Jurist:in]:::human
  C -- Antwort --> KB[Neues Wissen → Qdrant<br/>Duplikatprüfung ≥ 0,92]
  C -- Zeitlimit --> TO[Hinweis an Mitarbeitende]
  U[Upload-Formular] --> KB
  classDef human stroke-dasharray: 5 5
```

| Workflow | Aufgabe / Purpose |
|---|---|
| `P9_Legal-Navigator_MAIN` | Agent, drei Ausgänge, Freigabe, Eskalation, Lernen / agent with three outcomes |
| `P9_Legal-Guide-Upload` | Handbuch (PDF/DOCX) → Text → Qdrant |
| `P9_Error-Handler` | meldet Ausfälle an den Admin / reports failures |
