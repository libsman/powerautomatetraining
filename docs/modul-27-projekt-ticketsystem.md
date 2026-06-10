# Modul 27 – Projekt: IT-Ticketsystem mit Status-Tracking

## Lernziele

In diesem Abschlussprojekt baust du ein vollständiges IT-Ticketsystem das alle Lernpfade vereint: Formulareinreichung, Datenbankablage, Teams-Benachrichtigungen, Status-Updates, Eskalation und abschließende Bestätigung.

---

## Was wir bauen

```
[FLOW 1 – Ticket erstellen]
Mitarbeiter füllt Ticket-Formular aus
         ↓
Ticket in SharePoint + eindeutige Ticketnummer
         ↓
Eingangsbestätigung an Melder
         ↓
Adaptive Card im IT-Kanal (nach Priorität gefärbt)
         ↓
Bei Kritisch: Approval an IT-Leitung

[FLOW 2 – Status-Update]
IT-Mitarbeiter ändert Status in SharePoint
         ↓
Email an Melder mit aktuellem Status
         ↓
Bei Status "In Bearbeitung": Teams-Update im IT-Kanal

[FLOW 3 – Ticket gelöst]
Status wird auf "Gelöst" gesetzt
         ↓
Abschluss-Email an Melder mit Lösungsbeschreibung
         ↓
SLA-Zeit berechnen und in SP speichern
         ↓
Teams-Abschluss-Nachricht

[FLOW 4 – SLA-Monitoring (Scheduled)]
Stündlich: Prüfe überfällige Tickets
         ↓
Bei Überschreitung: Eskalations-Teams-Nachricht
```

---

## Vorbereitung

### SharePoint-Liste: IT-Tickets-Pro

Erstelle `IT-Tickets-Pro` mit:

| Spalte | Typ |
|--------|-----|
| Titel | Text (= Ticketnummer, z.B. `TKT-2026-0042`) |
| Betreff | Text |
| MelderName | Text |
| MelderEmail | Text |
| Kategorie | Auswahl: Hardware/Software/Netzwerk/Zugriff/Sonstiges |
| Priorität | Auswahl: Niedrig/Mittel/Hoch/Kritisch |
| Status | Auswahl: Offen/In Bearbeitung/Warten auf Rückmeldung/Gelöst/Geschlossen |
| Beschreibung | Text (mehrzeilig) |
| ZugeordnetAn | Text |
| Loesung | Text (mehrzeilig) |
| Erstellt | Datum+Uhrzeit |
| Geloest | Datum+Uhrzeit |
| SLA_Stunden | Zahl |

### Microsoft Forms: Ticket-Einreichung

Formular `IT-Ticket einreichen` (aus Modul 17 anpassen oder neu):
- Name, Email, Kategorie, Priorität, Betreff (Text, kurz), Beschreibung (Text, lang)

---

## Flow 1 – Ticket erstellen

### Trigger + Details + Ticketnummer

1. Forms-Trigger → Antwortdetails
2. Variable `TicketNr`:
   ```
   concat('TKT-', formatDateTime(utcNow(), 'yyyy'), '-', padLeft(string(rand(1000, 9999)), 4, '0'))
   ```

### SP-Element erstellen

Alle Felder befüllen (wie in Modul 17 – jetzt mit dem Feld `Erstellt = utcNow()`).

### Eingangsbestätigung

HTML-Email an Melder mit Ticket-Zusammenfassung und Ticket-Nummer.

### Teams-Adaptive Card (nach Priorität)

Switch auf Priorität → verschiedene Card-Farben:

```json
{
  "type": "AdaptiveCard", "version": "1.4",
  "body": [
    {"type": "TextBlock", "text": "🎫 Neues Ticket: @{variables('TicketNr')}", 
     "weight": "Bolder", "size": "Large",
     "color": "Attention"},
    {"type": "FactSet", "facts": [
      {"title": "Melder:", "value": "@{outputs('Antwortdetails_abrufen')?['body/MelderName']}"},
      {"title": "Kategorie:", "value": "@{outputs('Antwortdetails_abrufen')?['body/Kategorie']}"},
      {"title": "Priorität:", "value": "@{outputs('Antwortdetails_abrufen')?['body/Priorität']}"},
      {"title": "Betreff:", "value": "@{outputs('Antwortdetails_abrufen')?['body/Betreff']}"}
    ]}
  ]
}
```

### Condition: Kritisches Ticket → Approval

Wenn Priorität = `Kritisch`:
- Approval an IT-Leitung: `Kritisches Ticket [TicketNr] erfordert deine sofortige Aufmerksamkeit.`
- Wenn genehmigt (= zur Kenntnis genommen): SP Status → `In Bearbeitung`

---

## Flow 2 – Status-Update

1. Trigger: **When an existing item is modified** → `IT-Tickets-Pro`
2. Condition: Status wurde geändert UND ist NICHT `Gelöst` (das übernimmt Flow 3)
   ```
   not(equals(triggerOutputs()?['body/Status/Value'], 'Gelöst'))
   ```
3. Email an `MelderEmail`:
   ```
   Hallo [MelderName],
   
   dein Ticket [Titel] hat einen neuen Status:
   → [Status]
   
   Zugeordnet an: [ZugeordnetAn]
   ```
4. Wenn Status = `In Bearbeitung`: Teams-Nachricht im IT-Kanal: `🔧 [Titel] wird jetzt von [ZugeordnetAn] bearbeitet.`

---

## Flow 3 – Ticket gelöst

1. Trigger: **When an existing item is modified** → `IT-Tickets-Pro`
2. Condition: `Status eq 'Gelöst'`

**SLA berechnen:**
```
div(sub(ticks(utcNow()), ticks(triggerOutputs()?['body/Erstellt'])), 36000000000)
```
Ergibt die Bearbeitungszeit in Stunden → in Variable `SLAStunden` speichern.

**SP aktualisieren:**
- `Geloest`: `utcNow()`
- `SLA_Stunden`: Variable `SLAStunden`

**Abschluss-Email an Melder** (HTML):
```html
<h2>✅ Dein Ticket wurde gelöst</h2>
<p><b>Ticket:</b> [Titel]</p>
<p><b>Betreff:</b> [Betreff]</p>
<p><b>Lösung:</b><br>[Loesung]</p>
<p><b>Bearbeitungszeit:</b> [SLAStunden] Stunden</p>
<p>Vielen Dank für deine Geduld!</p>
```

**Teams-Abschluss:**
```
✅ Ticket [Titel] wurde gelöst. Bearbeitungszeit: [SLAStunden]h
```

---

## Flow 4 – SLA-Monitoring

1. Trigger: Recurrence – stündlich
2. SP Elemente abrufen: Status `Offen` oder `In Bearbeitung`, erstellt vor mehr als X Stunden:
   - Kritisch: SLA 2h → Filter: `Erstellt le '@{addHours(utcNow(), -2)}' and Priorität eq 'Kritisch' and Status ne 'Gelöst'`
   - Hoch: SLA 4h
   - Mittel: SLA 8h
3. Apply to each → Teams-Eskalation pro überfälligem Ticket:
   `⏰ SLA-Warnung: Ticket [Titel] (Priorität: [Priorität]) ist seit [X]h offen und überschreitet das SLA!`

---

## Vollständig testen

1. Formular ausfüllen → Ticket erscheint in SP + Teams-Karte + Bestätigungs-Email
2. In SP: Status auf `In Bearbeitung` ändern → Status-Update-Email
3. In SP: `Loesung` befüllen + Status auf `Gelöst` setzen → Abschluss-Email mit SLA-Zeit
4. Einen Testlauf des SLA-Monitoring-Flows manuell triggern

---

## 🎉 Training abgeschlossen!

Du hast das vollständige Power Automate Training absolviert!

### Was du jetzt beherrschst:

| Kompetenz | Gelernt in |
|-----------|-----------|
| Cloud Flows erstellen und debuggen | LP 1 |
| Konditionen, Schleifen, Variablen, Expressions | LP 1 |
| Outlook, Teams, SharePoint, OneDrive, Excel | LP 2 |
| Scheduled Flows und Recurrence | LP 2 |
| Approval-Flows (einstufig, mehrstufig, parallel) | LP 3 |
| Fehlerbehandlung (Run After, Try/Catch, Retry) | LP 3 |
| HTTP Connector (externe APIs) | LP 3 |
| Power Automate Desktop (Installation, Recorder, UI Automation) | LP 4 |
| Cloud Flow + Desktop Flow verbinden (RPA) | LP 4 |
| Vollständige Business-Prozesse end-to-end | LP 5 |

### Was kommt als nächstes?

- 🔗 [Microsoft Power Platform Learn](https://learn.microsoft.com/de-de/power-automate/)
- 🏆 [Power Automate Zertifizierung (PL-900)](https://learn.microsoft.com/de-de/certifications/power-platform-fundamentals/)
- 🔧 Eigene Szenarien in deinem Unternehmen automatisieren

**Herzlichen Glückwunsch – du hast Power Automate gemeistert!** 🎉
