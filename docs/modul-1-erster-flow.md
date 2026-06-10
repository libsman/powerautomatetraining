# Modul 1 – Erster Flow

## Lernziele

Nach diesem Modul kannst du:

- Einen automatisierten Cloud Flow von Grund auf erstellen
- Einen Trigger und eine Action konfigurieren
- Den Flow testen und Fehler lesen

---

## Was wir bauen

Jemand füllt ein Microsoft Forms-Formular aus → Power Automate reagiert sofort → du bekommst eine **Teams-Nachricht** mit den Antworten.

Das ist das klassische Einsteiger-Beispiel: ein echter, nützlicher Flow in unter 30 Minuten.

---

## Schritt 1: Formular erstellen

Bevor der Flow funktioniert, brauchen wir ein Formular das als Trigger dient.

1. Öffne [forms.office.com](https://forms.office.com) in einem neuen Tab
2. Klicke auf **+ Neues Formular**
3. Gib dem Formular den Titel: `Feedback-Formular`
4. Klicke auf **+ Frage hinzufügen** und wähle **Text**
5. Frage: `Dein Name`
6. Klicke erneut auf **+ Frage hinzufügen**, wähle **Auswahl**
7. Frage: `Wie zufrieden bist du?` – Optionen: `Sehr zufrieden`, `Zufrieden`, `Verbesserungsbedarf`
8. Klicke oben rechts auf **Teilen** und kopiere den Link – du brauchst ihn später zum Testen

!!! tip "Formular-Link merken"
    Öffne den Link in einem anderen Browser-Tab. Du wirst ihn am Ende nutzen um den Flow zu testen.

---

## Schritt 2: Neuen Flow erstellen

1. Wechsle zurück zu [make.powerautomate.com](https://make.powerautomate.com)
2. Klicke links auf **Erstellen**
3. Wähle **Automatisierter Cloudflow**
4. Gib dem Flow den Namen: `Feedback-Benachrichtigung`
5. Suche im Suchfeld nach `Microsoft Forms`
6. Wähle den Trigger: **Wenn eine neue Antwort übermittelt wird**
7. Klicke auf **Erstellen**

---

## Schritt 3: Trigger konfigurieren

Du siehst jetzt den Flow-Designer. Der erste Block ist dein Trigger.

1. Klicke auf den Trigger-Block **Wenn eine neue Antwort übermittelt wird**
2. Klicke auf das Feld **Formular-ID** und wähle dein `Feedback-Formular` aus der Dropdown-Liste

!!! info "Verbindung erlauben"
    Falls du noch keine Forms-Connection hast, fragt Power Automate jetzt danach. Klicke auf **Anmelden** und bestätige mit deinem Microsoft 365-Konto.

---

## Schritt 4: Antwortdetails abrufen

Der Trigger sagt nur *dass* jemand geantwortet hat – nicht *was* er geantwortet hat. Dafür brauchen wir eine zweite Action.

1. Klicke auf **+ Neuen Schritt hinzufügen**
2. Suche nach `Microsoft Forms`
3. Wähle die Action: **Antwortdetails abrufen**
4. Konfiguriere sie so:

| Feld | Wert |
|------|------|
| Formular-ID | `Feedback-Formular` (aus Dropdown) |
| Antwort-ID | Klicke auf das Feld → **Dynamischer Inhalt** → **Antwort-ID** |

!!! tip "Was ist Dynamic Content?"
    Das blaue Blitz-Symbol öffnet die Liste der **dynamischen Inhalte** – Werte aus vorherigen Flow-Schritten. Hier nimmst du die `Antwort-ID` aus dem Trigger, damit Power Automate weiß welche Antwort es holen soll.

---

## Schritt 5: Teams-Nachricht senden

1. Klicke erneut auf **+ Neuen Schritt hinzufügen**
2. Suche nach `Microsoft Teams`
3. Wähle die Action: **Eine Chatnachricht senden**
4. Konfiguriere sie so:

| Feld | Wert |
|------|------|
| Nachricht senden als | `Flow-Bot` |
| An | Deine eigene Email-Adresse oder einen Teams-Channel |
| Nachricht | Klicke ins Nachrichtenfeld, dann **Dynamischer Inhalt** |

Schreibe in das Nachrichtenfeld:
```
Neues Feedback eingegangen!

Name: [Dynamischer Inhalt: Dein Name]
Zufriedenheit: [Dynamischer Inhalt: Wie zufrieden bist du?]
```

Ersetze die Klammern durch die entsprechenden dynamischen Inhalte aus dem Schritt **Antwortdetails abrufen**.

!!! warning "Richtige Verbindung prüfen"
    Stelle sicher dass du beim Teams-Connector mit deinem eigenen Konto angemeldet bist (nicht ein anderes). Klicke auf die drei Punkte `...` → **Verbindung hinzufügen** falls nötig.

---

## Schritt 6: Flow speichern und testen

1. Klicke oben rechts auf **Speichern**
2. Klicke oben auf **Testen**
3. Wähle **Manuell** → **Testen**
4. Öffne jetzt in einem anderen Tab deinen Formular-Link und fülle das Formular aus
5. Wechsle zurück zu Power Automate – du siehst den Flow laufen

!!! success "Flow läuft!"
    Nach ein paar Sekunden sollte jeder Schritt grün werden. Öffne Teams – die Nachricht ist da. Herzlichen Glückwunsch, dein erster automatischer Flow funktioniert!

---

## Was passiert wenn es rot wird?

Klicke auf den rot markierten Schritt. Du siehst den genauen Fehlertext. Häufige Ursachen:

- **Verbindung fehlt**: Klicke auf den Schritt → Verbindung anmelden
- **Formular-ID nicht gefunden**: Hast du das Formular wirklich gespeichert?
- **Teams-Konto falsch**: Überprüfe die Verbindung im Teams-Schritt

---

## Challenge

!!! question "Challenge: Email-Benachrichtigung ergänzen"
    Erweitere den Flow: Zusätzlich zur Teams-Nachricht soll eine Email an dich selbst geschickt werden.
    
    1. Füge nach dem Teams-Schritt einen neuen Schritt hinzu
    2. Suche nach `Office 365 Outlook` → **Email senden (V2)**
    3. An: Deine eigene Email-Adresse
    4. Betreff: `Neues Feedback von [Name]`
    5. Text: Die Zufriedenheitsantwort
    6. Speichern und testen

??? success "Hinweis"
    Der Outlook-Schritt heißt **E-Mail senden (V2)**. Im Betreff-Feld kannst du dynamischen Inhalt direkt per Klick einfügen – du musst nicht schreiben. Klicke einfach ins Betreff-Feld und dann auf den Blitz-Button.

---

Weiter zu [Modul 2 – Konditionen](modul-2-konditionen.md) →
