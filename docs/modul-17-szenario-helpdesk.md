# Modul 17 – Szenario: IT-Helpdesk-Ticketsystem

## Lernziele

Nach diesem Modul hast du einen vollständigen IT-Helpdesk-Flow gebaut der:

- Tickets per Microsoft Forms entgegennimmt
- In SharePoint speichert und kategorisiert
- Den zuständigen IT-Mitarbeiter per Teams benachrichtigt
- Bei Lösung eine Bestätigungs-Email an den Melder schickt

---

## Übersicht

```
Forms-Einreichung
      ↓
Ticket in SharePoint speichern
      ↓
Priorität prüfen (Switch)
      ↓
IT-Team in Teams benachrichtigen (mit Adaptive Card)
      ↓
Wenn Ticket geschlossen wird (zweiter Flow)
      ↓
Email an Melder: "Ticket gelöst"
```

---

## Vorbereitung: Ressourcen anlegen

### 1. Microsoft Forms: Ticket-Formular

Erstelle ein Formular `IT-Helpdesk-Ticket` mit:

| Frage | Typ |
|-------|-----|
| Dein Name | Text |
| Deine Email-Adresse | Text |
| Kategorie | Auswahl: Hardware / Software / Netzwerk / Sonstiges |
| Priorität | Auswahl: Niedrig / Mittel / Hoch / Kritisch |
| Beschreibung des Problems | Text (lang) |

### 2. SharePoint-Liste: Tickets

Erstelle eine Liste `IT-Tickets` mit:

| Spalte | Typ |
|--------|-----|
| Titel (Standard) | Text |
| MelderName | Text |
| MelderEmail | Text |
| Kategorie | Auswahl: Hardware/Software/Netzwerk/Sonstiges |
| Priorität | Auswahl: Niedrig/Mittel/Hoch/Kritisch |
| Status | Auswahl: Offen/In Bearbeitung/Gelöst |
| Beschreibung | Text (mehrzeilig) |
| ZugeordnetAn | Text |
| TicketNummer | Zahl |

---

## Flow 1: Ticket einreichen

### Trigger + Antwortdetails

1. Neuer automatisierter Flow → Name: `Helpdesk-Ticket-erstellen`
2. Trigger: **When a new response is submitted** → Formular `IT-Helpdesk-Ticket`
3. Schritt: **Antwortdetails abrufen**

### Variable: Ticket-Nummer generieren

1. Neue Aktion: **Variable initialisieren**
   - Name: `TicketNummer`, Typ: `Ganzzahl`
   - Wert: Expression: `rand(10000, 99999)` (zufällige 5-stellige Zahl)

### SP-Element erstellen

1. Neue Aktion: **Element erstellen** (SharePoint) → Liste `IT-Tickets`

| Feld | Wert |
|------|------|
| Titel | `concat('TICKET-', string(variables('TicketNummer')))` (Expression) |
| MelderName | Dynamisch → **Dein Name** |
| MelderEmail | Dynamisch → **Deine Email-Adresse** |
| Kategorie | Dynamisch → **Kategorie** |
| Priorität | Dynamisch → **Priorität** |
| Status | `Offen` |
| Beschreibung | Dynamisch → **Beschreibung des Problems** |
| TicketNummer | Variable → **TicketNummer** |

### Teams-Benachrichtigung mit Switch

1. Neue Aktion: **Switch**
2. Ein: Dynamisch → **Priorität**

**Case Kritisch:**
- Teams-Adaptive-Card in den IT-Kanal (roter Hintergrund):
  ```json
  {
    "type": "AdaptiveCard",
    "version": "1.4",
    "body": [
      {"type": "TextBlock", "text": "🚨 KRITISCHES TICKET", "color": "Attention", "weight": "Bolder", "size": "Large"},
      {"type": "FactSet", "facts": [
        {"title": "Ticket:", "value": "@{concat('TICKET-', string(variables('TicketNummer')))}"},
        {"title": "Melder:", "value": "@{outputs('Antwortdetails_abrufen')?['body/Name']}"},
        {"title": "Problem:", "value": "@{outputs('Antwortdetails_abrufen')?['body/Beschreibung']}"}
      ]}
    ]
  }
  ```

**Case Hoch:** Adaptive Card mit `"color": "Warning"`

**Case Mittel / Niedrig:** Einfache Text-Nachricht

### Bestätigungs-Email an Melder

1. Neue Aktion: **E-Mail senden (V2)**

| Feld | Wert |
|------|------|
| An | Dynamisch → **Deine Email-Adresse** |
| Betreff | `Dein IT-Ticket wurde eingegangen: TICKET-[TicketNummer]` |
| Text | `Hallo [Name],\n\ndein Ticket wurde erfolgreich eingereicht.\n\nTicket-Nr.: TICKET-[TicketNummer]\nKategorie: [Kategorie]\nPriorität: [Priorität]\n\nDas IT-Team meldet sich so bald wie möglich.` |

---

## Flow 2: Ticket schließen

Wenn ein IT-Mitarbeiter den Status auf "Gelöst" setzt → Email an den Melder.

1. Neuer automatisierter Flow → Name: `Helpdesk-Ticket-gelöst`
2. Trigger: **When an existing item is modified** (SharePoint) → `IT-Tickets`
3. Condition: `Status eq 'Gelöst'`

**Im Ja-Zweig:**
- **E-Mail senden (V2)**:
  - An: Dynamisch → **MelderEmail**
  - Betreff: `✅ Dein Ticket wurde gelöst: [Titel]`
  - Text: `Hallo [MelderName],\n\ndein IT-Ticket [Titel] wurde als gelöst markiert.\n\nBei weiteren Fragen melde dich gerne erneut.`

---

## Challenge

!!! question "Challenge: SLA-Warnungen"
    Erweitere das System um SLA-Monitoring (Service Level Agreement):
    
    1. Erstelle einen **Scheduled Flow** der stündlich läuft
    2. Abrufen aller SP-Tickets mit Status `Offen` die vor mehr als 4 Stunden erstellt wurden
    3. Filter: Nur Tickets mit Priorität `Hoch` oder `Kritisch`
    4. Für jedes betroffene Ticket: Teams-Nachricht an den IT-Kanal mit Eskalationshinweis

??? success "Hinweis"
    Für den Zeitvergleich: `addHours(utcNow(), -4)` ergibt den Zeitpunkt von vor 4 Stunden. Im SharePoint-Filter: `Created le '@{addHours(utcNow(), -4)}' and Status eq 'Offen'`. Für die Prioritäts-Filter-Kombination brauchst du zwei separate Filter-Arrays oder einen kombinierten OData-Filter mit `or`.

---

Weiter zu [Modul 18 – Szenario: HR-Onboarding](modul-18-szenario-onboarding.md) →
