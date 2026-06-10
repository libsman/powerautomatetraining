# Modul 26 – Projekt: Wöchentlicher Status-Report

## Lernziele

In diesem Abschlussprojekt baust du einen vollautomatischen wöchentlichen Report der jeden Montag morgen verschickt wird – ohne dass jemand etwas tun muss.

---

## Was wir bauen

```
Jeden Montag 07:30 Uhr (Scheduled Flow)
         ↓
SharePoint-Aufgabenliste auslesen
  → Nur Einträge der letzten 7 Tage
         ↓
Statistiken berechnen:
  - Anzahl Offen / In Bearbeitung / Erledigt
  - Anzahl kritische Aufgaben
         ↓
HTML-Report-Email zusammenstellen
         ↓
Email-Versand an Team-Verteiler
         ↓
Teams-Zusammenfassung als Adaptive Card posten
```

---

## Vorbereitung

Nutze die SharePoint-Aufgabenliste `Aufgaben` aus Modul 8 (Spalten: `Titel`, `Status`, `Priorität`, `Zuständig`, `Created`).

Falls nicht vorhanden: Erstelle die Liste neu mit diesen Spalten und füge 8–10 Testeinträge mit verschiedenen Status und Prioritäten ein.

---

## Flow aufbauen

### Trigger: Recurrence

1. Neuer Flow → **Geplanter Cloudflow** → Name: `Wöchentlicher-Report`
2. Recurrence:
   - Intervall: `1`, Häufigkeit: `Woche`
   - Zeitzone: `(UTC+01:00) Amsterdam, Berlin`
   - An diesen Tagen: `Montag`
   - Startzeit: `2026-06-15T07:30:00`

### Variablen initialisieren

```
AnzahlOffen (Ganzzahl): 0
AnzahlInBearbeitung (Ganzzahl): 0  
AnzahlErledigt (Ganzzahl): 0
AnzahlKritisch (Ganzzahl): 0
ReportZeilen (String): ""
```

### SharePoint: Aufgaben abrufen

**Elemente abrufen** (SharePoint):
- Liste: `Aufgaben`
- Filter: `Created ge '@{addDays(utcNow(), -7)}'`
- Sortieren nach: `Priorität`

### Apply to each: Statistiken zählen

Für jedes Element:

**Condition 1: Status zählen**
- Switch auf `Status`:
  - `Offen` → **Zu Variablen hinzufügen**: `AnzahlOffen` + 1
  - `In Bearbeitung` → **Zu Variablen hinzufügen**: `AnzahlInBearbeitung` + 1
  - `Erledigt` → **Zu Variablen hinzufügen**: `AnzahlErledigt` + 1

**Condition 2: Priorität prüfen**
- Wenn `Priorität` = `Kritisch` oder `Hoch`: **Zu Variablen hinzufügen**: `AnzahlKritisch` + 1

**Zeile für Report aufbauen:**
- **Variable anfügen** an `ReportZeilen`:
  ```
  concat('<tr><td>', items('Apply_to_each')?['Titel'], '</td><td>', items('Apply_to_each')?['Status'], '</td><td>', items('Apply_to_each')?['Priorität'], '</td><td>', items('Apply_to_each')?['Zuständig']?['DisplayName'], '</td></tr>')
  ```

### HTML-Report zusammenbauen

**Variable "ReportHTML" initialisieren** (String) nach dem Apply to each:

Setze auf (nutze **Variable festlegen** mit einer Expression oder einem großen String):

```html
<!DOCTYPE html>
<html>
<body style="font-family: Arial, sans-serif;">
<h2>📊 Wöchentlicher Aufgaben-Report</h2>
<p>Zeitraum: letzte 7 Tage | Erstellt: {DATUM}</p>

<h3>Zusammenfassung</h3>
<table border="1" cellpadding="5" style="border-collapse: collapse;">
  <tr><th>Status</th><th>Anzahl</th></tr>
  <tr><td>🟢 Erledigt</td><td>{ERLEDIGT}</td></tr>
  <tr><td>🟡 In Bearbeitung</td><td>{IN_BEARBEITUNG}</td></tr>
  <tr><td>🔴 Offen</td><td>{OFFEN}</td></tr>
  <tr><td>⚡ Hoch/Kritisch</td><td>{KRITISCH}</td></tr>
</table>

<h3>Details</h3>
<table border="1" cellpadding="5" style="border-collapse: collapse; width: 100%;">
  <tr style="background:#f0f0f0;"><th>Aufgabe</th><th>Status</th><th>Priorität</th><th>Zuständig</th></tr>
  {ZEILEN}
</table>
</body>
</html>
```

Ersetze die Platzhalter via Expressions:
- `{DATUM}` → `formatDateTime(utcNow(), 'dd.MM.yyyy')`
- `{ERLEDIGT}` → Variable `AnzahlErledigt`
- `{IN_BEARBEITUNG}` → Variable `AnzahlInBearbeitung`
- `{OFFEN}` → Variable `AnzahlOffen`
- `{KRITISCH}` → Variable `AnzahlKritisch`
- `{ZEILEN}` → Variable `ReportZeilen`

Nutze **concat(...)** um alles zusammenzufügen, oder baue den HTML-String direkt im **Variable festlegen**-Schritt auf.

### Email-Versand

**E-Mail senden (V2)**:

| Feld | Wert |
|------|------|
| An | Deine Email oder ein Verteiler |
| Betreff | `📊 Wöchentlicher Report KW [formatDateTime(utcNow(), 'WW')] – [AnzahlOffen] offen, [AnzahlKritisch] kritisch` |
| Text | Variable `ReportHTML` |
| Ist HTML | `Ja` |

!!! tip "HTML-Emails"
    Aktiviere im Email-Schritt die Option **Ist HTML** (unter Erweiterte Optionen) damit deine Tabellen und Farben korrekt dargestellt werden.

### Teams Adaptive Card

**Adaptive Card in Kanal posten (V2)**:

```json
{
  "type": "AdaptiveCard",
  "version": "1.4",
  "body": [
    {
      "type": "TextBlock",
      "text": "📊 Wöchentlicher Report",
      "weight": "Bolder",
      "size": "Large"
    },
    {
      "type": "FactSet",
      "facts": [
        {"title": "Erledigt:", "value": "@{variables('AnzahlErledigt')}"},
        {"title": "In Bearbeitung:", "value": "@{variables('AnzahlInBearbeitung')}"},
        {"title": "Offen:", "value": "@{variables('AnzahlOffen')}"},
        {"title": "Hoch/Kritisch:", "value": "@{variables('AnzahlKritisch')}"}
      ]
    },
    {
      "type": "TextBlock",
      "text": "Vollständiger Report wurde per Email versandt.",
      "isSubtle": true
    }
  ]
}
```

---

## Testen

Da der Scheduled Trigger erst am Montag läuft, teste via **Manuell ausführen**:

1. Klicke im Flow-Designer oben auf **Testen** → **Manuell** → **Testen**
2. Prüfe Email-Eingang und Teams-Kanal

---

## 🎉 Abschlussprojekt 2 fertig!

Du hast kombiniert:

- Scheduled Flows mit Recurrence (LP 2)
- SharePoint-Listendaten mit Filter (LP 2)
- Variablen zum Akkumulieren von Werten (LP 1)
- HTML-Email mit dynamischen Inhalten (LP 2)
- Adaptive Card in Teams (LP 2)
- Expressions für Datumsberechnungen (LP 1)

---

Weiter zu [Modul 27 – Projekt: IT-Ticketsystem](modul-27-projekt-ticketsystem.md) →
