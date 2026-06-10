# Modul 22 – UI Automation

## Lernziele

Nach diesem Modul kannst du:

- Eine Windows-Desktop-Anwendung durch PAD steuern
- Fenster, Buttons und Eingabefelder programmatisch bedienen
- Den Windows-Taschenrechner vollständig automatisieren

---

## Was ist UI Automation?

UI Automation (User Interface Automation) erlaubt PAD den Zugriff auf Windows-Anwendungen über ihre Bedienoberfläche – genauso wie ein menschlicher Benutzer es tun würde, aber schneller und zuverlässiger.

PAD nutzt dafür Windows-Accessibility-APIs (UI Automation Framework von Microsoft). Fast jede Windows-Anwendung unterstützt das.

---

## Den Recorder für Desktop-Apps nutzen

### Schritt 1: Flow erstellen

Erstelle einen neuen PAD-Flow mit dem Namen `Taschenrechner-Demo`.

### Schritt 2: Taschenrechner starten

1. Aktionskatalog → **Systemsteuerung** → **Anwendung ausführen**
2. Anwendungspfad: `calc.exe`
3. Warte 1 Sekunde: **Verzögerung** → 1000 Millisekunden

### Schritt 3: Recorder für Desktop starten

1. Klicke oben auf **Recorder**
2. Wähle **Desktop-Recorder**
3. Klicke auf **Aufnehmen**
4. Klicke im Taschenrechner: `5` → `+` → `3` → `=`
5. Beende die Aufnahme

PAD hat die Klicks aufgezeichnet. Jetzt siehst du Aktionen wie **Auf UI-Element klicken** für jeden Button.

### Schritt 4: Ergebnis auslesen

1. Aktionskatalog → **UI-Automatisierung** → **Details von UI-Element abrufen**
2. Klicke auf das Ergebnis-Feld im Taschenrechner
3. **Abzurufende Details**: `Eigener Text` oder `Wert`
4. Das Ergebnis landet in einer Variable (z.B. `%Ergebnis%`)

### Schritt 5: Ergebnis anzeigen

1. Aktionskatalog → **Meldungsfeld**
2. Nachricht: `Das Ergebnis ist: %Ergebnis%`

---

## Direktzugriff auf UI-Elemente

Ohne Recorder kannst du UI-Elemente auch direkt ansprechen:

### UI-Elemente hinzufügen

1. Aktionskatalog → **UI-Automatisierung** → **Auf UI-Element klicken**
2. Klicke auf **UI-Element hinzufügen** im Aktionsdialog
3. Bewege die Maus über das gewünschte Element in der App
4. Drücke **Strg+Linksklick** um es auszuwählen (oder den beschrifteten Button im Recorder)
5. Klicke auf **Fertig**

PAD speichert jetzt den Selector für dieses Element.

---

## Wichtige UI-Automation-Aktionen

| Aktion | Zweck |
|--------|-------|
| **Auf UI-Element klicken** | Button, Menüeintrag, Checkbox klicken |
| **Text in UI-Element eingeben** | Textfeld befüllen |
| **Details von UI-Element abrufen** | Wert oder Text aus Feld lesen |
| **Fenster abrufen** | Fenster-Handle für weitere Operationen |
| **Fenster in den Vordergrund bringen** | Fenster fokussieren |
| **Fenster schließen** | Anwendung schließen |
| **Fenster verschieben/skalieren** | Fenstergröße und -position steuern |

---

## Fehler abfangen in Desktop Flows

Ähnlich wie in Cloud Flows gibt es in PAD eine Fehlerbehandlung:

### On Block Error

1. Klicke auf eine Aktion → **Fehlerbehandlung hinzufügen** (oder via Einstellungen)
2. **Bei Fehler**: Weiter mit nächster Aktion / Goto Bezeichnung / Aktion wiederholen / Flow abbrechen

### Retry-Mechanismus

1. Aktionskatalog → **Fehlerbehandlung** → **Erneut versuchen**
2. Stelle ein: X Versuche mit Y Sekunden Wartezeit

---

## Challenge

!!! question "Challenge: Excel über UI Automation öffnen und befüllen"
    Automatisiere Excel über UI Automation (ohne Excel-Connector):
    
    1. Starte Excel (`excel.exe`) via **Anwendung ausführen**
    2. Erstelle eine neue Datei: `Strg+N`
    3. Klicke auf Zelle A1 und tippe `Name`
    4. Tab-Taste → tippe in B1: `Datum`
    5. Tab-Taste → tippe in C1: `Wert`
    6. Klicke auf A2, tippe deinen Namen, Tab, tippe das heutige Datum, Tab, tippe `42`
    7. Speichere mit `Strg+S` unter `C:\Temp\PAD-Excel-Test.xlsx`
    8. Führe den Flow aus

??? success "Hinweis"
    Für Tab und Enter nutze **Tastenanschläge senden** mit den Sonderzeichen `{Tab}` und `{Return}`. Für das Speichern-Dialog: Nach `Strg+S` kommt ggf. ein Speichern-Dialog – füge eine Wartezeit ein und sende dann den Dateipfad + Enter als Tastenanschläge.

---

Weiter zu [Modul 23 – Cloud trifft Desktop](modul-23-cloud-trigger-desktop.md) →
