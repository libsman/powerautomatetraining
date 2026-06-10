# Modul 14 – Genehmigungen (Advanced)

## Lernziele

Nach diesem Modul kannst du:

- Mehrstufige Genehmigungen (Sequential Approvals) aufbauen
- Parallele Genehmigungen mit mehreren Genehmigern konfigurieren
- Einen Timeout einbauen und nach Ablauf eskalieren

---

## Mehrstufige Genehmigungen

Manchmal reicht ein Genehmiger nicht – der Antrag muss erst durch den direkten Vorgesetzten, dann durch die Geschäftsführung.

### Konzept: Sequential Approvals

Du baust mehrere Approval-Schritte hintereinander. Der zweite Approval startet nur wenn der erste genehmigt wurde.

### Schritt-für-Schritt

1. Öffne den Urlaubsantrag-Flow aus Modul 13 und klicke auf **Bearbeiten**
2. Ersetze den einfachen Approval-Schritt durch diese Struktur:

**Approval 1 – Vorgesetzter:**
- Aktion: **Genehmigung starten und auf Antwort warten**
- Titel: `[Urlaubsantrag von Name] – Stufe 1: Vorgesetzter`
- Zugewiesen an: Email des direkten Vorgesetzten

**Condition auf Ergebnis von Approval 1:**
- Wenn `Approve`:
  - **Approval 2 – Geschäftsführung:**
    - Titel: `[Urlaubsantrag von Name] – Stufe 2: Geschäftsführung`
    - Zugewiesen an: Email der Geschäftsführung
  - Condition auf Ergebnis von Approval 2:
    - Wenn `Approve`: Email "Endgültig genehmigt ✅"
    - Wenn `Reject`: Email "Abgelehnt durch Geschäftsführung ❌"
- Wenn `Reject`:
  - Email: "Abgelehnt durch Vorgesetzten ❌"

!!! tip "Übersichtlich bleiben"
    Bei verschachtelten Approvals wird der Flow schnell groß. Nutze **Kommentare** (drei Punkte → Notiz hinzufügen) um jeden Schritt zu beschriften.

---

## Parallele Genehmigungen

Manchmal müssen mehrere Personen gleichzeitig genehmigen – z.B. Fachabteilung UND Einkauf.

### Option 1: Alle müssen genehmigen

Nutze den Genehmigungstyp: **Genehmigen/Ablehnen – Alle müssen genehmigen**

- Alle aufgeführten Genehmiger bekommen die Anfrage gleichzeitig
- Der Flow wartet bis **alle** geantwortet haben
- Wenn auch nur einer ablehnt, gilt es als abgelehnt

### Option 2: Erster reicht

Genehmigungstyp: **Genehmigen/Ablehnen – Erster zum Antworten**

- Der Flow wartet nur auf die erste Antwort
- Wer zuerst reagiert, entscheidet

### Mehrere Empfänger eintragen

Im Feld **Zugewiesen an** kannst du mehrere Email-Adressen mit Semikolon trennen:
```
chef@firma.de;hr@firma.de;einkauf@firma.de
```

---

## Timeout und Eskalation

Was passiert wenn der Genehmiger 3 Tage nicht reagiert? Ohne Timeout wartet der Flow ewig.

### Timeout konfigurieren

1. Klicke auf die **drei Punkte** (`...`) des Approval-Schritts
2. Wähle **Einstellungen**
3. Aktiviere **Zeitlimit für Aktion**: z.B. `PT48H` (ISO 8601 – 48 Stunden)

Wenn der Timeout abläuft, **schlägt der Schritt fehl**. Das nutzen wir für die Eskalation.

### Eskalation per "Run After"

1. Füge nach dem Approval-Schritt eine neue **Parallel-Branch**-Action hinzu (oder nutze einen separaten Condition-Zweig)
2. Klicke auf den Folgeschritt → drei Punkte → **Run after konfigurieren**
3. Setze Häkchen bei **ist fehlgeschlagen** und **wurde übersprungen** (zusätzlich zu "war erfolgreich")

Im Eskalations-Zweig:
- Sende eine Email an den Antragsteller: `Dein Antrag wartet seit 48 Stunden auf Genehmigung. Der Vorgesetzte wurde erinnert.`
- Sende eine Email an den Vorgesetzten: `Erinnerung: Ein Urlaubsantrag wartet auf deine Genehmigung!`

!!! warning "ISO 8601 Zeitformat"
    Das Zeitlimit muss im ISO-8601-Format angegeben werden:
    - `PT1H` = 1 Stunde
    - `PT24H` = 24 Stunden
    - `P2D` = 2 Tage
    - `P1DT12H` = 1 Tag und 12 Stunden

---

## Challenge

!!! question "Challenge: 3-stufiger Approval mit Timeout"
    Baue einen neuen Flow für einen **Einkaufsantrag**:
    
    1. Formular: `Einkaufsantrag` mit Feldern: Name, Artikel, Betrag (Zahl), Begründung
    2. Condition bereits im Flow: Wenn Betrag > 500 → 2 Stufen (Vorgesetzter + Geschäftsführung); sonst → 1 Stufe (Vorgesetzter reicht)
    3. Jeder Approval-Schritt hat einen Timeout von 24 Stunden
    4. Bei Timeout: Erinnerungs-Email an den jeweiligen Genehmiger

??? success "Hinweis"
    Nutze eine Condition direkt nach den Antwortdetails um den Betrag zu prüfen: Expression `greater(int(triggerOutputs()?['body/Betrag']), 500)` im Ausdruck-Tab. Danach baust du zwei verschiedene Approval-Strukturen in den Ja/Nein-Zweigen.

---

Weiter zu [Modul 15 – Fehlerbehandlung](modul-15-fehlerbehandlung.md) →
