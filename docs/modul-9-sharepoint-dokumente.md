# Modul 9 – SharePoint-Dokumente

## Lernziele

Nach diesem Modul kannst du:

- Einen Flow starten wenn eine Datei in SharePoint hochgeladen wird
- Metadaten einer Datei lesen und setzen
- Dateiinhalt abrufen und weiterverarbeiten

---

## SharePoint als Dokumentenablage

Neben Listen verwaltet SharePoint auch Dokumente – in Dokumentenbibliotheken. Power Automate kann auf Datei-Events reagieren: neuer Upload, Änderung, Verschiebung. So werden Dokumentenprozesse automatisch ausgelöst.

---

## Trigger: Wenn eine Datei erstellt wird

### Schritt 1: Flow erstellen

1. Erstelle einen **Automatisierten Cloudflow**
2. Name: `Dokument-Verarbeitung`
3. Suche nach `SharePoint` → Trigger: **When a file is created (properties only)**
4. Klicke auf **Erstellen**

### Schritt 2: Trigger konfigurieren

| Feld | Wert |
|------|------|
| Websiteadresse | Deine SharePoint-Site |
| Bibliotheksname | `Dokumente` (oder ein spezifischer Ordner) |

!!! info "Properties only vs. mit Inhalt"
    Der Trigger **properties only** ist leichter und startet schneller – er liefert nur Metadaten (Name, Pfad, Autor). Den eigentlichen Dateiinhalt holst du in einem separaten Schritt. Das ist effizienter als alles auf einmal zu laden.

---

## Metadaten lesen und setzen

### Datei-Properties lesen

Der Trigger liefert bereits viele Metadaten:

| Dynamic Content | Bedeutung |
|----------------|-----------|
| `Name` | Dateiname inkl. Endung |
| `Identifier` | Eindeutige ID der Datei |
| `Link` | Direktlink zur Datei |
| `Author Claims` | Wer die Datei hochgeladen hat |
| `Created` | Erstellungsdatum |

### Datei-Eigenschaften aktualisieren

Du kannst eigene Metadaten-Spalten in der Dokumentenbibliothek anlegen und diese per Flow befüllen:

1. Neue Aktion → **Dateieigenschaften aktualisieren** (SharePoint)
2. Site und Bibliothek auswählen
3. **ID**: Dynamisch → **ID** aus dem Trigger
4. Befülle die Custom-Felder (z.B. `Status`, `Abteilung`, `Freigegeben`)

---

## Dateiinhalt abrufen

Wenn du den tatsächlichen Inhalt einer Datei benötigst (z.B. zum Weiterleiten per Email):

1. Neue Aktion → **Dateiinhalt abrufen** (SharePoint)
2. Site auswählen
3. **Datei-ID**: Dynamisch → **Identifier** aus dem Trigger

Das Ergebnis ist der binäre Dateiinhalt – du kannst ihn als Email-Anhang oder in OneDrive/SharePoint weiterleiten.

---

## Praxisbeispiel: Dokument-Upload → Teams-Benachrichtigung + Metadaten

### Ziel
Jedes neue Dokument in einer bestimmten Bibliothek soll:
1. In Teams bekannt gemacht werden (mit Direktlink)
2. Automatisch den Metadaten-Status `Neu` bekommen

### Flow aufbauen

**Schritt 1** – Trigger: **When a file is created (properties only)**

**Schritt 2** – Teams-Nachricht senden:

| Feld | Wert |
|------|------|
| Kanal | Dein Abteilungs-Channel |
| Nachricht | `📄 Neue Datei hochgeladen: [Name]\n\n🔗 [Link]` |

**Schritt 3** – Metadaten setzen: **Dateieigenschaften aktualisieren**

| Feld | Wert |
|------|------|
| ID | Dynamisch → **ID** |
| Status | `Neu` |

---

## Dokument per Email versenden

Wenn du die hochgeladene Datei direkt per Email weiterschicken willst:

1. Neue Aktion → **Dateiinhalt abrufen** (SharePoint) – nutze **Identifier** als Datei-ID
2. Neue Aktion → **E-Mail senden (V2)** (Outlook)
3. Klicke auf **Erweiterte Optionen anzeigen** in der Email-Aktion
4. **Anlagen** → **+ Anlage hinzufügen**:
   - Name: Dynamisch → **Name** (aus Trigger)
   - Inhalt: Dynamisch → **Dateiinhalt** (aus Schritt 1)

---

## Challenge

!!! question "Challenge: PDF-Quarantäne"
    Erstelle einen Flow der auf neue Dateien in deiner SharePoint-Dokumentenbibliothek reagiert:
    
    1. Trigger: **When a file is created (properties only)**
    2. Condition: Prüfe ob der Dateiname mit `.pdf` endet:
       `endsWith(triggerOutputs()?['body/{FilenameWithExtension}'], '.pdf')`
    3. Wenn **Ja (PDF)**: Setze den Metadaten-Status auf `Zur Prüfung` und sende eine Teams-Nachricht mit dem Datei-Link
    4. Wenn **Nein (kein PDF)**: Sende eine Email an dich selbst: `Nicht-PDF-Datei hochgeladen: [Name]`

??? success "Hinweis"
    Das Feld für den Dateinamen heißt im Dynamic Content **Name** oder technisch `{FilenameWithExtension}`. Du findest beide Varianten im Dynamic-Content-Panel unter dem Trigger. Nutze die Expression `endsWith(...)` im Condition-Block im **Ausdruck**-Tab.

---

Weiter zu [Modul 10 – OneDrive & Excel](modul-10-onedrive-excel.md) →
