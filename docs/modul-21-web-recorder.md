# Modul 21 – Web Recorder

## Lernziele

Nach diesem Modul kannst du:

- Den Web Recorder starten und Browser-Aktionen aufnehmen
- Die Aufnahme anpassen und robuster machen
- Daten von einer Webseite extrahieren

---

## Was ist der Web Recorder?

Der Web Recorder ist ein Tool das deine Browser-Klicks und Eingaben aufzeichnet und daraus automatisch PAD-Aktionen generiert. Du musst nicht manuell UI-Elemente suchen oder Selektoren schreiben – einfach machen, aufnehmen, fertig.

---

## Schritt 1: Web Recorder starten

1. Öffne deinen Notepad-Demo-Flow oder erstelle einen neuen Flow
2. Klicke oben im Designer auf **Recorder**
3. Wähle **Web-Recorder** (oder der Recorder öffnet sich automatisch wenn ein Browser-Schritt erkannt wird)

Alternativ:
1. Suche im Aktionskatalog nach `Webrecorder` oder `Browser`
2. Ziehe **Neue Microsoft Edge-Instanz starten** in deinen Flow
3. Klicke dann auf das Recorder-Symbol

---

## Schritt 2: Einfache Websuche aufnehmen

Wir nehmen eine Google-Suche auf:

1. Klicke auf **Aufnehmen** im Recorder-Fenster
2. Öffne Microsoft Edge (der Recorder-Browser öffnet sich automatisch oder du arbeitest im vorhandenen Fenster)
3. Navigiere zu `https://www.bing.com`
4. Klicke auf das Suchfeld und tippe `Power Automate Training`
5. Drücke Enter
6. Klicke im Recorder auf **Aufnahme beenden**

PAD hat jetzt automatisch folgende Aktionen generiert:
- Neue Edge-Instanz starten
- Zur URL navigieren
- Texteingabe in das Suchfeld
- Enter-Taste senden

---

## Schritt 3: Aufnahme anpassen

Die aufgenommenen Aktionen funktionieren meistens direkt, aber du solltest sie prüfen:

### Wartezeiten hinzufügen

Webseiten laden unterschiedlich schnell. Füge nach kritischen Schritten eine **Wartezeit** oder besser eine **Warten auf Webseiten-Element** Aktion ein.

1. Suche im Aktionskatalog nach `Warten`
2. **Auf Webseiten-Element warten**: Wähle das Element auf das du wartest und setze die maximale Wartezeit

### Selector prüfen

Klicke auf einen aufgenommenen Schritt → klicke auf den Selector (das gepunktete Rahmen-Symbol). Hier siehst du wie PAD das Element auf der Webseite identifiziert:

- **CSS Selector**: `#search_input` – schnell aber fragil
- **XPath**: `//*[@id="search"]` – robuster
- **Text**: `Suche...` – flexibel aber kann sich ändern

!!! tip "Robustere Selektoren"
    Ändere Selektoren auf stabile Attribute wie `ID` oder `data-testid`. Vermeide Selektoren die auf der Reihenfolge oder dem genauen Text basieren – die brechen schnell wenn die Seite aktualisiert wird.

---

## Schritt 4: Daten von einer Webseite extrahieren

Das mächtigste Feature des Web Recorders: **Data Extraction** – Daten aus einer Webseite in eine Variable holen.

### Beispiel: Aktuellen Text aus einer Webseite lesen

1. Im Aktionskatalog → **Webautomatisierung** → **Webseite-Details abrufen**
2. Wähle den Browser-Tab
3. **Zu abzurufende Details**: `Webseiten-Text**` oder einen spezifischen Selektor

### Tabelle aus einer Webseite extrahieren

1. Aktionskatalog → **Webautomatisierung** → **Webseite-Daten extrahieren**
2. Wähle den Bereich auf der Webseite (du klickst auf die Tabelle oder den Datenbereich)
3. PAD erkennt das Tabellenformat automatisch

Das Ergebnis ist eine **DataTable-Variable** die du in Schleifen weiterverarbeiten kannst.

---

## Praxisbeispiel: Webformular ausfüllen

Viele Unternehmensportale haben Webformulare. So automatisierst du sie:

1. Starte den Recorder
2. Navigiere zur Webseite mit dem Formular
3. Fülle jedes Feld aus – der Recorder nimmt jede Eingabe auf
4. Klicke auf **Absenden**
5. Beende die Aufnahme

Danach: Ersetze die festen Texteingaben durch **Variablen** – dann kannst du denselben Flow mit unterschiedlichen Daten ausführen.

---

## Challenge

!!! question "Challenge: Daten aus Wikipedia extrahieren"
    1. Erstelle einen neuen PAD-Flow
    2. Öffne `https://de.wikipedia.org/wiki/Power_Automate` im Browser via PAD
    3. Nutze **Webseite-Details abrufen** um den kompletten Seitentext zu lesen
    4. Speichere den Text in eine Textdatei unter `C:\Temp\Wikipedia-PA.txt` (Aktion: **Text in Datei schreiben**)
    5. Führe den Flow aus und prüfe die Datei

??? success "Hinweis"
    Für das Schreiben in eine Datei: Aktionskatalog → **Datei** → **Text in Datei schreiben**. Gib den Datei-Pfad an und wähle als Inhalt deine extrahierte Text-Variable (z.B. `%WebPageText%`). Aktiviere die Option **Vorhandene Datei überschreiben** oder **Anfügen** je nach Bedarf.

---

Weiter zu [Modul 22 – UI Automation](modul-22-ui-automation.md) →
