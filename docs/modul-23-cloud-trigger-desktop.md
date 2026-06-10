# Modul 23 – Cloud trifft Desktop

## Lernziele

Nach diesem Modul kannst du:

- Einen Desktop Flow aus einem Cloud Flow heraus aufrufen
- Input-Parameter vom Cloud Flow an den Desktop Flow übergeben
- Das Ergebnis des Desktop Flows zurück an den Cloud Flow geben

---

## Attended vs. Unattended

Bevor wir starten: Es gibt zwei Arten wie ein Desktop Flow laufen kann.

| Modus | Beschreibung | Lizenz |
|-------|-------------|--------|
| **Attended** | Läuft auf deinem PC während du dabei bist | Kostenlos / M365 |
| **Unattended** | Läuft im Hintergrund ohne Benutzer-Anmeldung | Premium |

In diesem Modul nutzen wir **Attended** – du sitzt am PC und der Cloud Flow startet den Desktop Flow auf deinem Rechner.

---

## Schritt 1: Desktop Flow mit Input/Output vorbereiten

### Input-Variablen definieren

1. Öffne deinen PAD-Designer (beliebiger Flow oder neuer Flow `Cloud-Desktop-Demo`)
2. Klicke rechts oben auf das **Variablen-Panel** (x² Symbol)
3. Klicke auf **+** bei **Eingabevariablen**:

| Variable | Typ | Wert (Standard) |
|----------|-----|----------------|
| `InputName` | Text | `Testname` |
| `InputAbteilung` | Text | `IT` |

Eingabevariablen können vom Cloud Flow befüllt werden.

### Flow-Logik aufbauen

Nutze die Variablen im Flow:
1. **Anwendung ausführen**: `notepad.exe`
2. **Tastenanschläge senden**: `Willkommen, %InputName% aus der Abteilung %InputAbteilung%!`
3. Speichere die Datei

### Output-Variablen definieren

1. Klicke auf **+** bei **Ausgabevariablen**:

| Variable | Typ |
|----------|-----|
| `ErgebnisStatus` | Text |

2. Füge am Ende des Flows eine Aktion ein: **Variable festlegen** → `ErgebnisStatus` = `Erfolgreich ausgeführt`

### Flow speichern

Klicke auf **Speichern** in PAD.

---

## Schritt 2: Cloud Flow mit Desktop Flow verbinden

1. Gehe zu [make.powerautomate.com](https://make.powerautomate.com)
2. Erstelle einen neuen **manuellen Cloud Flow** → Name: `Cloud-startet-Desktop`
3. Trigger: **Manuell einen Flow auslösen**
4. Füge zwei Eingabe-Parameter hinzu: `Name` (Text) und `Abteilung` (Text)

### Desktop Flow aufrufen

1. Neue Aktion → suche nach `Desktop`
2. Wähle: **Einen Desktopflow ausführen** (unter Power Automate Desktop)
3. **Desktopflow**: Wähle deinen `Cloud-Desktop-Demo`-Flow
4. **Ausführungsmodus**: `Attended – im Vordergrund ausführen`
5. Du siehst jetzt die Input-Felder deines Desktop Flows:

| Feld | Wert |
|------|------|
| InputName | Dynamisch → **Name** (aus dem manuellen Trigger) |
| InputAbteilung | Dynamisch → **Abteilung** (aus dem manuellen Trigger) |

### Output nutzen

Nach dem Desktop-Flow-Schritt steht `ErgebnisStatus` als Dynamic Content bereit.

1. Neue Aktion → **Teams-Nachricht senden**
2. Nachricht: `Desktop Flow abgeschlossen. Status: [ErgebnisStatus]`

---

## Schritt 3: Flow testen

1. Stelle sicher dass dein PC an ist und Power Automate Desktop **geöffnet** ist (PAD muss im Hintergrund laufen für Attended-Ausführung)
2. Klicke im Cloud Flow auf **Testen** → Manuell
3. Gib `Name` und `Abteilung` ein und starte den Test
4. Du siehst wie auf deinem PC automatisch Notepad geöffnet und befüllt wird
5. Der Cloud Flow wartet bis der Desktop Flow fertig ist und zeigt dann den Status

!!! warning "PC muss aktiv sein"
    Für Attended-Flows muss dein Computer entsperrt und Power Automate Desktop läuft. Wenn der PC gesperrt ist oder PAD nicht läuft, schlägt der Desktop-Flow-Schritt fehl.

---

## Praxisbeispiel: Automatische Daten-Eingabe

**Szenario**: Ein Cloud Flow empfängt Bestelldaten per Email und übergibt sie an einen Desktop Flow der sie in ein lokales ERP-System einträgt (das keine API hat).

```
Email mit Bestelldaten empfangen (Cloud Flow)
    ↓
JSON parsen (Artikelnummer, Menge, Lieferant)
    ↓
Desktop Flow aufrufen mit Parametern:
  - Artikelnummer
  - Menge
  - Lieferant
    ↓
ERP-Anwendung öffnen (Desktop Flow)
    ↓
Felder ausfüllen (Desktop Flow)
    ↓
Bestätigen + schließen (Desktop Flow)
    ↓
Status zurück an Cloud Flow: "Eingetragen" (Output)
    ↓
Email-Bestätigung an Absender (Cloud Flow)
```

Das ist das Kernkonzept von Enterprise RPA: Cloud-Trigger, Desktop-Ausführung, Cloud-Abschluss.

---

## Challenge

!!! question "Challenge: Formular-zu-Desktop"
    1. Erstelle ein Microsoft Forms-Formular mit Feldern: `Benutzer`, `Begrüßungstext`
    2. Erstelle einen PAD-Flow der:
       - Input: `Benutzer` (Text), `Text` (Text)
       - Notepad öffnet und `Hallo %Benutzer%: %Text%` einträgt
       - Output: `Status` = `OK`
    3. Erstelle einen Cloud Flow: Forms-Trigger → Antwortdetails → **Desktop Flow ausführen** → Teams-Bestätigung mit dem Output-Status

??? success "Hinweis"
    Stelle sicher dass PAD auf deinem PC läuft wenn du den Cloud Flow testest. Du kannst den PAD-Flow auch lokal in PAD testen bevor du ihn aus dem Cloud Flow aufrufst. Im Cloud Flow erscheinen die Input-Felder des Desktop Flows erst wenn du den Flow in PAD gespeichert hast.

---

Weiter zu [Modul 24 – Aufräumen](modul-24-aufräumen.md) →
