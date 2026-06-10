# Modul 7 – Teams

## Lernziele

Nach diesem Modul kannst du:

- Nachrichten in Teams-Kanäle und Chats senden
- Adaptive Cards erstellen und versenden
- Einen Benutzer in einer Teams-Nachricht erwähnen (@mention)

---

## Teams als Kommunikationskanal

Power Automate und Microsoft Teams sind perfekte Partner. Anstatt Emails zu schicken (die im Posteingang verschwinden), kannst du direkt in den richtigen Teams-Kanal posten – genau dort wo das Team ohnehin arbeitet.

---

## Nachricht in einen Kanal posten

### Schritt 1: Neuen Flow erstellen

1. Erstelle einen manuellen Flow (zum Testen)
2. Klicke auf **+ Neuen Schritt** → suche nach `Microsoft Teams`
3. Wähle die Aktion: **Eine Nachricht in einem Kanal posten (V3)**

### Schritt 2: Aktion konfigurieren

| Feld | Wert |
|------|------|
| Posten als | `Flow bot` oder `Benutzer` |
| Posten in | `Kanal` |
| Team | Dein Team aus der Dropdown-Liste |
| Kanal | z.B. `Allgemein` |
| Nachricht | Dein Text (kann Dynamic Content enthalten) |

!!! info "Flow bot vs. Benutzer"
    **Flow bot** postet als "Power Automate" und braucht keine weiteren Berechtigungen. **Benutzer** postet im Namen deines Kontos – dann erscheint es als wärst du es selbst.

---

## Chat-Nachricht senden

Für direkte Nachrichten an eine Person:

1. Neue Aktion → **Eine Chatnachricht senden**
2. **Nachricht senden als**: `Flow bot`
3. **An**: Email-Adresse des Empfängers

---

## @mention in Nachrichten

Um eine Person in einer Teams-Nachricht zu erwähnen:

1. Neue Aktion → **@mention eines Benutzers in einem Kanal** (oder "in einem Chat")
2. Konfiguriere Team, Kanal und den zu erwähnenden Benutzer

Der Benutzer bekommt eine Benachrichtigung genau wie bei einem manuellen @mention.

---

## Adaptive Cards

Adaptive Cards sind strukturierte, interaktive Nachrichten – viel schöner als einfacher Text.

### Was sind Adaptive Cards?

Eine Adaptive Card kann enthalten:
- Überschriften und formatierte Texte
- Bilder und Icons
- Tabellen und Listen
- Buttons (Actions)

### Schritt 1: Card per Flow posten

1. Neue Aktion → **Adaptive Karte in einem Kanal posten (V2)**

Für den **Card Body** brauchst du ein JSON-Objekt. Ein einfaches Beispiel:

```json
{
  "type": "AdaptiveCard",
  "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
  "version": "1.4",
  "body": [
    {
      "type": "TextBlock",
      "text": "📋 Neues Ticket eingegangen",
      "weight": "Bolder",
      "size": "Large"
    },
    {
      "type": "FactSet",
      "facts": [
        { "title": "Von:", "value": "Max Mustermann" },
        { "title": "Priorität:", "value": "Hoch" },
        { "title": "Datum:", "value": "10.06.2026" }
      ]
    }
  ]
}
```

!!! tip "Adaptive Card Designer"
    Unter [adaptivecards.io/designer](https://adaptivecards.io/designer/) kannst du Cards visuell bauen und das JSON direkt kopieren. Ersetze dann die festen Werte durch Dynamic Content.

### Dynamischen Inhalt in Cards einfügen

Im Card-JSON-Feld kannst du Dynamic Content direkt per Klick einfügen:

```json
"value": "@{outputs('Antwortdetails_abrufen')?['body/Name']}"
```

Oder du klickst ins JSON-Feld, positionierst den Cursor beim gewünschten Wert, und wählst aus dem Dynamic-Content-Panel das richtige Feld.

---

## Challenge

!!! question "Challenge: Feedback-Karte"
    Erweitere den Feedback-Flow aus Modul 1:
    
    1. Ersetze die einfache Teams-Textnachricht durch eine **Adaptive Card**
    2. Die Card soll enthalten:
        - Überschrift: `Neues Feedback`
        - FactSet mit: Name, Zufriedenheit und aktuellem Datum (via `formatDateTime`)
        - Je nach Zufriedenheit eine farbige Hintergrundfarbe oder ein passendes Emoji
    3. Poste die Card in einen Teams-Kanal deiner Wahl

??? success "Hinweis"
    Das Datum bekommst du mit der Expression `formatDateTime(utcNow(), 'dd.MM.yyyy')`. Füge es als festes Feld in das JSON ein oder nutze den Expression-Tab im Dynamic-Content-Panel. Für Farben kannst du im Card-JSON `"style": "good"` (grün), `"style": "attention"` (rot) oder `"style": "warning"` (orange) bei TextBlock oder Container verwenden.

---

Weiter zu [Modul 8 – SharePoint-Listen](modul-8-sharepoint-listen.md) →
