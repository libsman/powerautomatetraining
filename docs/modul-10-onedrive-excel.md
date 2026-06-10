# Modul 10 – OneDrive & Excel

## Lernziele

Nach diesem Modul kannst du:

- Einen Flow starten wenn eine neue Datei in OneDrive abgelegt wird
- Zeilen in eine Excel-Tabelle hinzufügen
- Eine Excel-Tabelle auslesen und weiterverarbeiten

---

## Warum Excel per Flow?

Excel ist in vielen Unternehmen die universelle Datenbank. Anstatt Daten manuell einzutragen, kann Power Automate das automatisch erledigen – jedes Mal wenn ein Formular ausgefüllt, eine Email empfangen oder ein SharePoint-Eintrag erstellt wird.

!!! warning "Voraussetzung: Tabelle in Excel"
    Der Excel-Connector arbeitet ausschließlich mit **formatierten Tabellen** (nicht einfach Zelleingaben). Erstelle in deiner Excel-Datei zuerst eine Tabelle: Daten markieren → **Einfügen** → **Tabelle** → Häkchen bei "Tabelle hat Überschriften". Gib der Tabelle einen Namen (z.B. `Eingaben`).

---

## OneDrive-Trigger: Wenn eine Datei erstellt wird

### Schritt 1: Flow erstellen

1. Erstelle einen **Automatisierten Cloudflow**
2. Name: `OneDrive-Datei-Verarbeitung`
3. Trigger: **When a file is created** (OneDrive for Business)
4. **Ordner**: Wähle einen Ordner in deinem OneDrive (z.B. `/Eingehend`)

---

## Excel: Zeile hinzufügen

Das ist einer der meistgenutzten Excel-Schritte: Ein neuer Eintrag landet automatisch in deiner Tabelle.

### Flow: Forms → Excel

1. Erstelle einen neuen automatisierten Flow mit dem Trigger **Wenn eine neue Antwort übermittelt wird** (Microsoft Forms)
2. Füge **Antwortdetails abrufen** hinzu (wie in Modul 1)
3. Neue Aktion → **Zeile zu Tabelle hinzufügen** (Excel Online (Business))

| Feld | Wert |
|------|------|
| Speicherort | `OneDrive for Business` |
| Dokumentbibliothek | `OneDrive` |
| Datei | Pfad zu deiner Excel-Datei (z.B. `/Daten/Auswertung.xlsx`) |
| Tabelle | Name der Tabelle (z.B. `Eingaben`) |

Danach erscheinen die Spalten deiner Tabelle als Felder – befülle sie mit Dynamic Content:

| Spalte | Wert (Dynamic Content) |
|--------|----------------------|
| Datum | `formatDateTime(utcNow(), 'dd.MM.yyyy')` (Expression) |
| Name | **Dein Name** (aus Antwortdetails) |
| Zufriedenheit | **Wie zufrieden bist du?** (aus Antwortdetails) |

---

## Excel: Tabelle auslesen

Um alle Zeilen aus einer Excel-Tabelle zu lesen:

1. Neue Aktion → **Zeilen in Tabelle auflisten** (Excel Online (Business))
2. Wähle Speicherort, Bibliothek, Datei und Tabelle

Das Ergebnis ist ein Array – du kannst es in einem `Apply to each` weiterverarbeiten oder per `Filter Array` filtern.

### Mit Filter arbeiten

Im Schritt **Zeilen in Tabelle auflisten** gibt es unter **Erweiterte Optionen**:

| Option | Zweck |
|--------|-------|
| Filter-Abfrage | OData-Filter: `Status eq 'Offen'` |
| Sortieren nach | Spaltenname |
| Maximale Zeilenanzahl | Performance-Limit |

---

## Excel: Zeile aktualisieren

1. Neue Aktion → **Zeile aktualisieren** (Excel Online (Business))
2. Du brauchst die **Schlüsselspalte** und den **Schlüsselwert** um die richtige Zeile zu identifizieren

!!! tip "Eindeutige ID-Spalte"
    Füge deiner Excel-Tabelle eine Spalte `ID` hinzu und trage dort eindeutige Werte ein (z.B. die Forms-Antwort-ID). Dann kannst du Zeilen zuverlässig aktualisieren ohne Verwechslungen.

---

## Challenge

!!! question "Challenge: Feedback-Datenbank in Excel"
    Baue auf dem Feedback-Flow aus Modul 1 auf:
    
    1. Erstelle eine Excel-Datei `Feedback.xlsx` in deinem OneDrive mit einer Tabelle `Feedback` und den Spalten: `Datum`, `Name`, `Zufriedenheit`
    2. Erweitere den Feedback-Flow so dass nach dem Abrufen der Antwortdetails eine neue Zeile in der Excel-Tabelle angelegt wird
    3. Datum: Aktuelles Datum via Expression `formatDateTime(utcNow(), 'dd.MM.yyyy')`
    4. Teste den Flow 3x mit verschiedenen Antworten – die Einträge sollen in Excel erscheinen

??? success "Hinweis"
    Achte darauf dass die Excel-Tabelle wirklich als formatierte Tabelle angelegt ist (nicht nur Zeilen mit Text). Wenn der Connector die Tabelle nicht findet, klicke auf das Datei-Feld → navigiere zur Datei per Ordnernavigation statt den Pfad einzutippen.

---

Weiter zu [Modul 11 – Scheduled Flows](modul-11-scheduled-flows.md) →
