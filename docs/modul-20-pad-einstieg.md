# Modul 20 – Power Automate Desktop: Einstieg

## Lernziele

Nach diesem Modul kannst du:

- Power Automate Desktop installieren und starten
- Die Oberfläche von PAD navigieren
- Deinen ersten Desktop Flow erstellen und ausführen

---

## Was ist Power Automate Desktop?

Power Automate Desktop (PAD) ist die RPA-Komponente (Robotic Process Automation) von Power Automate. Es läuft direkt auf deinem Windows-PC und kann:

- Windows-Anwendungen bedienen (Klicken, Tippen, Lesen)
- Browser automatisieren (Edge, Chrome, Firefox)
- Excel, Word, Outlook direkt steuern
- Mit Dateisystem und Registry arbeiten

**Typische Anwendungsfälle:**
- Altsysteme ohne API automatisieren (SAP GUI, alte Desktop-Software)
- Formulare in Web-Anwendungen ausfüllen
- Daten aus einer Anwendung kopieren und in eine andere eintragen
- Regelmäßige Berichte aus einer Software exportieren

!!! info "Lizenz für PAD"
    Power Automate Desktop ist kostenlos zum **manuellen (Attended) Ausführen** – du startest den Flow selbst. Für **unattended** Ausführung (automatisch, ohne dass du dabei bist) brauchst du eine Premium-Lizenz.

---

## Installation

### Schritt 1: Power Automate Desktop herunterladen

**Option A – Microsoft Store (empfohlen):**
1. Öffne den Microsoft Store auf deinem Windows-PC
2. Suche nach `Power Automate`
3. Klicke auf **Herunterladen / Installieren**

**Option B – Direct Download:**
1. Gehe zu [make.powerautomate.com](https://make.powerautomate.com)
2. Klicke links auf **Meine Flows** → **Desktopflows**
3. Klicke oben auf **Power Automate Desktop herunterladen**
4. Führe die Installationsdatei aus

### Schritt 2: Browser-Extension installieren

PAD kann Browser nur automatisieren wenn eine Extension installiert ist.

1. Beim ersten Start fragt PAD automatisch
2. Oder: Einstellungen → Browser-Erweiterungen → für Edge und/oder Chrome installieren
3. Aktiviere die Extension im Browser (wird ggf. separat verlangt)

### Schritt 3: Anmeldung

1. Starte Power Automate Desktop
2. Melde dich mit deinem **Microsoft 365-Konto** an
3. Wähle deine Umgebung (Standard reicht)

---

## Die Oberfläche von Power Automate Desktop

Nach dem Start siehst du die **PAD Startseite**:

| Bereich | Inhalt |
|---------|--------|
| **Meine Flows** | Alle deine Desktop Flows |
| **Neu erstellen** | Neuen Flow anlegen |
| **Freigegebene Flows** | Von anderen geteilte Flows |

### Der Flow-Designer

Wenn du einen Flow öffnest oder neu erstellst, öffnet sich der Designer:

- **Linke Seite**: Aktionskatalog – alle verfügbaren Aktionen kategorisiert
- **Mitte**: Der Flow selbst (Schritt für Schritt)
- **Rechte Seite**: Variablen-Panel
- **Unten**: Konsolen-Ausgabe und Fehler-Log

---

## Erster Desktop Flow: Notepad automatisieren

### Schritt 1: Neuen Flow erstellen

1. Klicke auf **Neu erstellen**
2. Name: `Notepad-Demo`
3. Der Designer öffnet sich

### Schritt 2: Notepad starten

1. Suche links im Aktionskatalog nach `Notepad` oder `Anwendung`
2. Doppelklicke auf **Anwendung ausführen**

| Feld | Wert |
|------|------|
| Anwendungspfad | `notepad.exe` |
| Fenster-Sichtbarkeit | Normal |

### Schritt 3: Text eingeben

1. Suche nach `Fenster` → **Text in Fenster eingeben** oder nutze **UI Automation** → **Texteingabe simulieren**
2. Alternativ: Suche nach `Tastenanschläge senden**
3. Aktion: **Tastenanschläge senden**
4. Text: `Automatisch erstellt von Power Automate Desktop!`

!!! tip "Einfachste Methode"
    Für Notepad reicht **Tastenanschläge senden** – Notepad hat ein einziges Textfeld, da landet der Text automatisch sobald das Fenster im Fokus ist.

### Schritt 4: Datei speichern

1. Aktion: **Tastenanschläge senden** → `^s` (Ctrl+S)
2. Warte kurz: **Warten** → 1 Sekunde
3. Tastenanschläge senden: `C:\Temp\PAD-Test.txt` (Dateiname eintippen)
4. Tastenanschläge senden: `{Return}` (Enter bestätigen)

### Schritt 5: Flow ausführen

Klicke oben auf **▶ Ausführen**. PAD öffnet Notepad, tippt den Text und speichert ihn – du siehst es live auf deinem Bildschirm.

!!! success "Erster Desktop Flow läuft!"
    Du hast soeben eine Windows-Anwendung vollautomatisch bedient. Das ist das Grundprinzip aller RPA-Automatisierungen.

---

## Challenge

!!! question "Challenge: Andere Nachricht und anderer Pfad"
    Ändere den Notepad-Flow:
    
    1. Der eingetippte Text soll dein eigener Name + heutiges Datum sein
       (Nutze die PAD-Variable `%CurrentDateTime%` mit dem Format `dd.MM.yyyy`)
    2. Die Datei soll unter `C:\Temp\Mein-erster-PAD-Flow.txt` gespeichert werden
    3. Führe den Flow aus und prüfe ob die Datei erstellt wurde

??? success "Hinweis"
    In PAD werden Variablen mit `%Variablenname%` referenziert. Das aktuelle Datum bekommst du mit der Aktion **Datum und Uhrzeit abrufen** → gibt `%CurrentDateTime%` zurück. Formatieren kannst du es mit der Aktion **Datum/Uhrzeit formatieren**.

---

Weiter zu [Modul 21 – Web Recorder](modul-21-web-recorder.md) →
