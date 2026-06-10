# Modul 8 – SharePoint-Listen

## Lernziele

Nach diesem Modul kannst du:

- Einen Flow starten wenn ein neues SharePoint-Listenelement erstellt wird
- Elemente lesen, schreiben und aktualisieren
- Auf SharePoint-Ereignisse wie Änderungen reagieren

---

## SharePoint-Listen als Flow-Grundlage

SharePoint-Listen sind das Herzstück vieler Power Automate-Workflows im Unternehmen. Sie dienen gleichzeitig als Datenspeicher und als Trigger – sobald jemand einen Eintrag anlegt oder ändert, kann Power Automate automatisch reagieren.

---

## Trigger: Wenn ein neues Element erstellt wird

### Schritt 1: Flow erstellen

1. Erstelle einen **Automatisierten Cloudflow**
2. Name: `SP-Liste-Benachrichtigung`
3. Trigger: **When an item is created** (SharePoint)
4. Klicke auf **Erstellen**

### Schritt 2: Trigger konfigurieren

| Feld | Wert |
|------|------|
| Websiteadresse | URL deiner SharePoint-Site (aus Dropdown oder tippen) |
| Listenname | Name deiner Liste (aus Dropdown) |

!!! info "Welche Liste soll ich nutzen?"
    Erstelle für dieses Modul eine neue SharePoint-Liste mit dem Namen `Aufgaben` und den Spalten: `Titel` (Standard), `Zuständig` (Person), `Priorität` (Auswahl: Niedrig/Mittel/Hoch), `Status` (Auswahl: Offen/In Bearbeitung/Erledigt).

---

## Elemente lesen

Um bestehende Elemente aus einer Liste zu lesen:

1. Neue Aktion → **Elemente abrufen** (SharePoint)
2. Site und Liste auswählen
3. Klicke auf **Erweiterte Optionen anzeigen**:

| Feld | Wert (Beispiel) |
|------|----------------|
| Filter-Abfrage | `Status eq 'Offen'` |
| Sortieren nach | `Priorität` |
| Maximale Anzahl | `100` |

!!! tip "OData-Filter"
    Die Filtersprache heißt OData. Wichtige Operatoren:
    - `eq` = gleich, `ne` = ungleich
    - `and`, `or` für Kombinationen
    - `startswith(Titel, 'Bug')` für Textsuche
    
    Beispiel: `Status eq 'Offen' and Priorität eq 'Hoch'`

---

## Elemente schreiben

### Neues Element erstellen

1. Neue Aktion → **Element erstellen** (SharePoint)
2. Site und Liste auswählen
3. Alle Felder befüllen (Dynamic Content oder feste Werte)

### Vorhandenes Element aktualisieren

1. Neue Aktion → **Element aktualisieren** (SharePoint)
2. Site und Liste auswählen
3. **ID**: Die ID des zu aktualisierenden Elements (aus einem vorherigen Schritt)
4. Ändere nur die Felder die du aktualisieren willst – alle anderen bleiben unverändert

!!! warning "ID ist Pflicht"
    Beim Aktualisieren brauchst du immer die **ID** des Elements. Die bekommst du entweder aus dem Trigger (wenn der Flow durch ein Listenelement ausgelöst wurde) oder aus einem vorherigen **Elemente abrufen**-Schritt.

---

## Praxisbeispiel: Aufgaben-Benachrichtigung

### Ziel
Wenn ein neues Element in der Aufgaben-Liste angelegt wird → Benachrichtigung an die zuständige Person per Email.

### Flow aufbauen

1. Trigger: **When an item is created** → deine Aufgaben-Liste
2. Neue Aktion: **E-Mail senden (V2)** (Outlook)

| Feld | Wert |
|------|------|
| An | Dynamisch: **Zuständig Email** (aus dem SP-Trigger) |
| Betreff | `Neue Aufgabe zugewiesen: [Titel]` |
| Text | `Hallo,\n\nes wurde eine neue Aufgabe für dich angelegt:\n\nAufgabe: [Titel]\nPriorität: [Priorität]\n\nBitte schau sie dir an.` |

!!! tip "Personen-Felder in SharePoint"
    Wenn eine Spalte vom Typ **Person** ist, liefert SharePoint ein Objekt mit `.DisplayName`, `.Email` und mehr. Im Dynamic-Content-Panel siehst du beide Optionen – wähle **Email** für die Email-Adresse.

---

## Trigger: Wenn ein Element geändert wird

Wenn du auf **Änderungen** reagieren willst (nicht nur auf neue Einträge):

1. Trigger: **When an existing item is modified** (SharePoint)
2. Konfiguriere Site und Liste genauso wie beim Erstell-Trigger

!!! info "Beide Trigger kombinieren?"
    Du kannst nicht einen einzigen Flow auf beide Trigger legen. Erstelle zwei separate Flows oder nutze den Trigger **When an item is created or modified**.

---

## Challenge

!!! question "Challenge: Status-Änderung benachrichtigen"
    1. Erstelle einen Flow der auf **Änderungen** in deiner Aufgaben-Liste reagiert
    2. Füge eine Bedingung hinzu: Nur wenn der neue `Status`-Wert = `Erledigt` ist
    3. Sende eine Teams-Nachricht: `✅ Aufgabe [Titel] wurde von [Zuständig] als erledigt markiert!`
    4. Setze zusätzlich ein Textfeld `AbgeschlossenAm` in der Liste auf das aktuelle Datum

??? success "Hinweis"
    Das aktuelle Datum als ISO-String bekommst du mit `utcNow()`. Für das Datums-Feld in SharePoint nutze das Format `utcNow('yyyy-MM-ddTHH:mm:ssZ')`. Das Setzen des Feldes machst du mit der Aktion **Element aktualisieren** – die ID nimmst du aus dem Trigger.

---

Weiter zu [Modul 9 – SharePoint-Dokumente](modul-9-sharepoint-dokumente.md) →
