# Modul 25 – Projekt: Vollständiger Urlaubsantragsprozess

## Lernziele

In diesem Abschlussprojekt baust du einen kompletten, produktionsreifen Urlaubsantragsprozess der alle Konzepte aus den vorherigen Lernpfaden vereint.

---

## Was wir bauen

```
Mitarbeiter füllt Formular aus
         ↓
Antrag in SharePoint gespeichert (mit eindeutiger Antragsnummer)
         ↓
Automatische Eingangsbestätigung per Email
         ↓
Manager erhält Approval-Anfrage (Timeout: 48h)
  Kein Antwort → Eskalation an HR
         ↓
Genehmigt:
  → Status in SharePoint → "Genehmigt"
  → Bestätigungs-Email an Mitarbeiter
  → Teams-Ankündigung im Team-Kanal
  → Eintrag in Excel-Kalender
         ↓
Abgelehnt:
  → Status in SharePoint → "Abgelehnt"
  → Ablehnungs-Email mit Begründung
```

---

## Vorbereitung

### Microsoft Forms: Urlaubsantrag

Erstelle `Urlaubsantrag (Vollversion)` mit:

| Frage | Typ |
|-------|-----|
| Dein vollständiger Name | Text |
| Deine Email-Adresse | Text |
| Email deines Managers | Text |
| Urlaubsbeginn | Datum |
| Urlaubsende | Datum |
| Art des Urlaubs | Auswahl: Jahresurlaub / Sonderurlaub / Unbezahlter Urlaub |
| Notiz / Vertretung | Text (optional) |

### SharePoint-Liste: Urlaubsanträge

Erstelle `Urlaubsanträge` mit:

| Spalte | Typ |
|--------|-----|
| Titel (Standard) | Text (= Antragsnummer) |
| Antragsteller | Text |
| Email | Text |
| ManagerEmail | Text |
| Von | Datum |
| Bis | Datum |
| Art | Auswahl |
| AnzahlTage | Zahl |
| Status | Auswahl: Eingegangen / Genehmigt / Abgelehnt / Eskaliert |
| Notiz | Text (mehrzeilig) |

### Excel: Urlaubs-Kalender

Erstelle `Urlaubskalender.xlsx` in OneDrive mit einer Tabelle `Kalender` und Spalten: `Antragsteller`, `Von`, `Bis`, `Tage`, `Status`.

---

## Flow aufbauen

### Trigger und Antwortdetails

1. Neuer automatisierter Flow → Name: `Urlaubsantrag-Vollversion`
2. Trigger: Forms → `Urlaubsantrag (Vollversion)`
3. **Antwortdetails abrufen**

### Variablen initialisieren

```
Variable "AntragNr" (String):
  concat('URL-', formatDateTime(utcNow(), 'yyyyMMdd-HHmm'))

Variable "AnzahlUrlaubstage" (Ganzzahl):
  Berechnung über: div(sub(ticks(triggerOutputs?['Bis']), ticks(triggerOutputs?['Von'])), 864000000000)
```

!!! info "Tagesdifferenz berechnen"
    Die Expression `div(sub(ticks('Bis-Datum'), ticks('Von-Datum')), 864000000000)` berechnet die Tagesdifferenz. Nutze den Ausdruck-Editor und ersetze die Datumswerte durch das entsprechende Dynamic Content.

### SP-Element erstellen

**Element erstellen** in `Urlaubsanträge`:

| Feld | Wert |
|------|------|
| Titel | Variable `AntragNr` |
| Antragsteller | Dynamisch → **Dein vollständiger Name** |
| Email | Dynamisch → **Deine Email-Adresse** |
| ManagerEmail | Dynamisch → **Email deines Managers** |
| Von | Dynamisch → **Urlaubsbeginn** |
| Bis | Dynamisch → **Urlaubsende** |
| Art | Dynamisch → **Art des Urlaubs** |
| AnzahlTage | Variable `AnzahlUrlaubstage` |
| Status | `Eingegangen` |
| Notiz | Dynamisch → **Notiz / Vertretung** |

### Eingangsbestätigung

**E-Mail senden (V2)**:
- An: Dynamisch → **Deine Email-Adresse**
- Betreff: `[AntragNr] Dein Urlaubsantrag wurde eingegangen`
- Text (HTML einschalten):
  ```html
  <p>Hallo <strong>[Name]</strong>,</p>
  <p>dein Urlaubsantrag wurde erfolgreich eingereicht:</p>
  <table>
    <tr><td><b>Antragsnummer:</b></td><td>[AntragNr]</td></tr>
    <tr><td><b>Zeitraum:</b></td><td>[Von] bis [Bis] ([AnzahlTage] Tage)</td></tr>
    <tr><td><b>Art:</b></td><td>[Art]</td></tr>
  </table>
  <p>Dein Manager wird benachrichtigt.</p>
  ```

### Approval mit Timeout

**Genehmigung starten und auf Antwort warten**:

| Feld | Wert |
|------|------|
| Typ | `Genehmigen/Ablehnen – Erster zum Antworten` |
| Titel | `[AntragNr]: Urlaubsantrag von [Name]` |
| Zugewiesen an | Dynamisch → **Email deines Managers** |
| Details | `Zeitraum: [Von] bis [Bis] ([AnzahlTage] Tage)\nArt: [Art]\nVertretung: [Notiz]` |
| Anfrage-Item-Link | Direkt-Link zum SP-Eintrag (Dynamic Content) |

**Einstellungen** (drei Punkte → Einstellungen): Zeitlimit: `PT48H`

### Condition + Eskalation (Run After)

Baue nach dem Approval eine **Condition** auf das Ergebnis.

**Außerdem**: Füge einen weiteren Schritt nach dem Approval hinzu mit **Run After: Ist fehlgeschlagen + Zeitüberschreitung**:
- SP Element aktualisieren → Status: `Eskaliert`
- Email an HR (feste Adresse): `Approval für [AntragNr] nicht beantwortet. Bitte manuell prüfen.`
- Email an Antragsteller: `Dein Antrag wurde an HR eskaliert.`

### Wenn Genehmigt:

1. **SP aktualisieren**: Status → `Genehmigt`
2. **Email an Mitarbeiter**: `✅ Dein Urlaubsantrag [AntragNr] wurde genehmigt!`
3. **Teams-Nachricht** im Team-Kanal: `🏖️ [Name] ist vom [Von] bis [Bis] im Urlaub.`
4. **Zeile in Excel hinzufügen** (Urlaubskalender):
   - Antragsteller, Von, Bis, Tage, Status: `Genehmigt`

### Wenn Abgelehnt:

1. **SP aktualisieren**: Status → `Abgelehnt`
2. **Email an Mitarbeiter**:
   ```
   ❌ Dein Urlaubsantrag [AntragNr] wurde leider abgelehnt.
   
   Begründung: [Kommentare]
   
   Bitte wende dich bei Fragen an deinen Manager.
   ```

---

## Testen

1. Formular ausfüllen
2. Sicherstellen dass du als "Manager" erreichbar bist (nutze deine eigene Email)
3. Approval-Email öffnen und Genehmigen/Ablehnen klicken
4. SharePoint-Liste und Excel-Kalender prüfen

---

## 🎉 Abschlussprojekt 1 fertig!

Du hast einen vollständigen, produktionsreifen Urlaubsantragsprozess gebaut der Folgendes kombiniert:

- Forms Trigger (LP 1)
- Variablen & Expressions (LP 1)
- SharePoint-Listen (LP 2)
- Email & Teams (LP 2)
- Excel-Integration (LP 2)
- Approval-Flow mit Timeout & Eskalation (LP 3)
- Fehlerbehandlung via Run After (LP 3)

---

Weiter zu [Modul 26 – Projekt: Wöchentlicher Report](modul-26-projekt-reporting.md) →
