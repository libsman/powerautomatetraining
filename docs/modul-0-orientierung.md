# Modul 0 – Orientierung

## Lernziele

Nach diesem Modul kannst du:

- make.powerautomate.com sicher navigieren
- Die drei Flow-Typen unterscheiden und erklären
- Eine Connection zu einem Dienst anlegen

---

## Was ist Power Automate?

Stell dir vor: Jedes Mal wenn jemand ein Formular ausfüllt, tipps du manuell eine Email, trägst es in eine Liste ein und postest eine Nachricht in Teams. Das gleiche jeden Tag, fünfmal, zehnmal.

**Power Automate übernimmt genau diese Routinearbeit für dich.**

Du beschreibst einmal: "Wenn *das* passiert, dann mach *folgendes*." Power Automate läuft dann 24/7 im Hintergrund und erledigt es automatisch – auch wenn du gerade im Meeting bist oder schläfst.

---

## Die drei Flow-Typen

Power Automate kennt drei grundlegend verschiedene Arten von Flows:

| Typ | Wo läuft er? | Wofür? |
|-----|-------------|--------|
| **Cloud Flow** | In der Microsoft Cloud | Alles was mit M365, Web-Diensten und APIs zu tun hat |
| **Desktop Flow** | Auf deinem Windows-PC | Apps automatisieren die keine API haben (Legacy-Software, Browser) |
| **Business Process Flow** | Im Browser | Nutzer durch mehrstufige Prozesse führen (z.B. Angebots-Workflow) |

!!! info "Was wir hauptsächlich nutzen"
    In diesem Training liegt der Fokus auf **Cloud Flows** (Lernpfad 1–3) und **Desktop Flows** (Lernpfad 4). Business Process Flows sind ein Spezialthema für Power Platform-Profis.

---

## Das Interface von make.powerautomate.com

Öffne [make.powerautomate.com](https://make.powerautomate.com) im Browser und melde dich mit deinem Microsoft 365-Konto an.

### Schritt 1: Die Startseite

Du siehst die **Startseite (Home)**. Die wichtigsten Bereiche:

- **Linkes Menü**: Navigation zu deinen Flows, Connections, Lösungen und mehr
- **Hauptbereich**: Empfohlene Vorlagen und zuletzt verwendete Flows
- **Suchleiste oben**: Vorlagen und Connectors finden

!!! tip "Tipp: Umgebung prüfen"
    Oben rechts siehst du deine aktuelle **Umgebung** (z.B. `(default)`). Für dieses Training reicht die Standard-Umgebung vollkommen aus.

### Schritt 2: Deine Flows

Klicke links auf **Meine Flows**. Hier landen alle Flows die du erstellst. Aktuell ist die Liste noch leer – das ändert sich ab Modul 1.

Drei Unterkategorien:
- **Cloudflows**: Automatisierte, geplante und sofortige Flows
- **Desktopflows**: Flows aus Power Automate Desktop
- **Freigegebene Flows**: Flows die andere mit dir geteilt haben

### Schritt 3: Connections

Klicke links auf **Verbindungen** (Connections).

Connections sind wie Anmelde-Tokens: Power Automate braucht deine Erlaubnis, um in deinem Namen auf Outlook, Teams oder SharePoint zuzugreifen. Einmal eingerichtet, werden sie von allen deinen Flows automatisch genutzt.

**So legst du eine Connection an:**

1. Klicke auf **+ Neue Verbindung**
2. Suche nach `Office 365 Outlook`
3. Klicke auf **Erstellen**
4. Melde dich mit deinem Microsoft 365-Konto an und bestätige die Berechtigungen
5. Die Connection erscheint jetzt in deiner Liste – Status: **Verbunden**

!!! success "Connection angelegt"
    Du hast deine erste Connection eingerichtet. Power Automate kann jetzt in deinem Namen auf Outlook zugreifen. Du kannst weitere Connections für Teams, SharePoint, OneDrive etc. genauso anlegen – oder du erlaubst es beim ersten Verwenden eines Connectors im Flow.

---

## Die wichtigsten Begriffe

| Begriff | Bedeutung |
|---------|-----------|
| **Flow** | Die Automatisierung selbst – enthält Trigger und Actions |
| **Trigger** | Das Auslöseereignis: "Wenn eine neue Email ankommt..." |
| **Action** | Was dann passiert: "...dann poste eine Teams-Nachricht" |
| **Connector** | Die Verbindung zu einem Dienst (Outlook, Teams, SharePoint...) |
| **Connection** | Die konkrete Anmeldung zu einem Connector mit deinem Konto |
| **Dynamic Content** | Daten aus vorherigen Schritten, z.B. der Betreff der Email |
| **Expression** | Eine Formel die Werte berechnet, z.B. `formatDateTime(...)` |
| **Environment** | Dein Arbeitsbereich in Power Platform (Standard reicht) |

---

## Challenge

!!! question "Challenge: Interface erkunden"
    1. Öffne [make.powerautomate.com](https://make.powerautomate.com) und navigiere zu **Vorlagen**
    2. Suche nach einer Vorlage die dich interessiert (z.B. "SharePoint" oder "Teams")
    3. Öffne sie und schau dir an, wie der Flow aufgebaut ist – klicke auf jeden Schritt
    4. Erstelle eine **Outlook-Connection** unter Verbindungen (falls noch nicht geschehen)

??? success "Hinweis"
    Vorlagen sind fertige Flows die du direkt anpassen kannst. Du musst aber nichts speichern oder aktivieren – es geht nur ums Schauen und Verstehen. Klicke einfach auf eine Vorlage und dann auf **Schritt für Schritt** oder **Diese Vorlage verwenden**.

---

Weiter zu [Modul 1 – Erster Flow](modul-1-erster-flow.md) →
