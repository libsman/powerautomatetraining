# Modul 11 – Scheduled Flows

## Lernziele

Nach diesem Modul kannst du:

- Einen Flow zu einem festen Zeitpunkt ausführen (täglich, wöchentlich, monatlich)
- Die Zeitzone korrekt konfigurieren
- Einen automatischen Report oder eine Erinnerung aufbauen

---

## Was sind Scheduled Flows?

Scheduled Flows (Zeitplan-Flows) werden nicht durch ein Ereignis ausgelöst sondern durch die **Uhr**. Klassische Anwendungsfälle:

- Jeden Montag um 8 Uhr: Status-Report per Email
- Täglich um 17 Uhr: Erinnerung an offene Tickets in Teams
- Monatlich am 1.: Excel-Auswertung erstellen und versenden
- Jede Stunde: Externe API prüfen

---

## Recurrence Trigger

### Schritt 1: Neuen Flow erstellen

1. Erstelle einen **Geplanten Cloudflow**
2. Name: `Montags-Erinnerung`
3. Du siehst sofort den **Recurrence**-Trigger mit Zeitplan-Optionen

### Schritt 2: Zeitplan konfigurieren

Klicke auf den Recurrence-Block:

| Feld | Wert (Beispiel) |
|------|----------------|
| Intervall | `1` |
| Häufigkeit | `Woche` |
| Startzeit | `2026-06-15T08:00:00Z` |
| Zeitzone | `(UTC+01:00) Amsterdam, Berlin, Bern...` |
| An diesen Tagen | `Montag` |

!!! warning "Zeitzone immer explizit setzen"
    Power Automate rechnet standardmäßig in UTC. Ohne Zeitzone-Einstellung läuft ein "8 Uhr"-Flow im Winter um 9 Uhr MEZ. Klicke auf **Erweiterte Optionen anzeigen** und setze die Zeitzone explizit.

### Häufigkeits-Optionen

| Häufigkeit | Intervall 1 = |
|-----------|--------------|
| Sekunde | jede Sekunde |
| Minute | jede Minute |
| Stunde | jede Stunde |
| Tag | täglich |
| Woche | wöchentlich |
| Monat | monatlich |

Für "jeden zweiten Tag": Häufigkeit = `Tag`, Intervall = `2`.

---

## Praxisbeispiel: Wöchentlicher Aufgaben-Report

### Ziel
Jeden Montag um 8 Uhr einen Überblick über alle offenen Aufgaben per Email erhalten.

### Flow aufbauen

**Schritt 1** – Trigger: Recurrence
- Intervall: `1`, Häufigkeit: `Woche`, Zeitzone: Berlin, An diesen Tagen: `Montag`, Startzeit: `..T08:00:00`

**Schritt 2** – SP-Elemente abrufen: **Elemente abrufen** (SharePoint)
- Deine Aufgaben-Liste, Filter: `Status eq 'Offen'`

**Schritt 3** – Variable initialisieren
- Name: `ReportText`, Typ: `String`, Wert: `Offene Aufgaben diese Woche:\n\n`

**Schritt 4** – Apply to each (über die SP-Elemente)
- Innerhalb: **Variable anfügen** → füge an `ReportText` an:
  `concat('• ', items('Apply_to_each')?['Titel'], ' (', items('Apply_to_each')?['Priorität'], ')\n')`

**Schritt 5** – Email senden (V2)
- An: Deine Email-Adresse
- Betreff: `Aufgaben-Report KW [formatDateTime(utcNow(), 'WW')]`
- Text: Variable `ReportText`

---

## Datumsberechnungen im Scheduled Flow

Manchmal willst du nur Elemente der letzten 7 Tage einbeziehen. Dafür gibt es `addDays`:

```
addDays(utcNow(), -7)
```

Als OData-Filter für SharePoint:
```
Created ge '@{addDays(utcNow(), -7)}'
```

!!! tip "Datum in OData-Filter"
    Wenn du einen Datums-Vergleich in SharePoint-Filtern einbaust, musst du den Ausdruck in `@{...}` einwickeln um ihn als dynamischen Wert zu markieren. Der Filter-Bereich in **Elemente abrufen** akzeptiert diese Syntax.

---

## Challenge

!!! question "Challenge: Tägliche Teams-Erinnerung"
    Erstelle einen Flow der:
    
    1. Täglich um 17:00 Uhr ausgeführt wird (Zeitzone Berlin)
    2. Alle SharePoint-Aufgaben mit Status `In Bearbeitung` und Priorität `Hoch` abruft
    3. Wenn es solche Aufgaben gibt (Condition: Länge der Liste > 0): Eine Teams-Nachricht posten mit den Aufgabentiteln
    4. Wenn keine solchen Aufgaben vorhanden sind: keine Aktion (leerer "Wenn nein"-Zweig)

??? success "Hinweis"
    Die Anzahl der zurückgegebenen Elemente bekommst du mit `length(outputs('Elemente_abrufen')?['body/value'])`. Nutze diesen Ausdruck im Condition-Block (Operator: `ist größer als`, Wert: `0`).

---

Weiter zu [Modul 12 – Aufräumen](modul-12-aufräumen.md) →
