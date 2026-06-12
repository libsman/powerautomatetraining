# Modul 4 – Variablen & Expressions

## Lernziele

Nach diesem Modul kannst du:

- Variablen initialisieren, setzen und in Flows nutzen
- Einfache Expressions mit `concat`, `formatDateTime` und `if` schreiben
- Den Unterschied zwischen Dynamic Content und Expressions erklären
- `triggerBody()` und `body()` zum Zugriff auf JSON-Daten nutzen

---

## Variablen

Manchmal brauchst du einen Zwischenspeicher: du sammelst Werte in einer Schleife, baust einen Text Stück für Stück zusammen oder merkst dir ob ein Fehler aufgetreten ist. Dafür gibt es **Variablen**.

### Variable initialisieren

Eine Variable muss **vor** der ersten Nutzung initialisiert werden – also möglichst zu Beginn des Flows, noch vor allen Schleifen.

1. Erstelle einen neuen manuellen Flow
2. Klicke auf **+ Neuen Schritt** → suche nach `Variable`
3. Wähle **Variable initialisieren**

| Feld | Wert |
|------|------|
| Name | `AnzahlFehler` |
| Typ | `Ganzzahl` |
| Wert | `0` |

### Variable setzen und erhöhen

Innerhalb einer Schleife kannst du den Wert ändern:

- **Variable festlegen**: Setzt einen neuen festen Wert
- **Zu Variablen hinzufügen**: Zählt einen Wert dazu (nur bei Ganzzahl)
- **Variable anfügen**: Hängt Text an (nur bei String)

### Verfügbare Typen

| Typ | Wofür |
|-----|-------|
| String | Text |
| Ganzzahl | Ganze Zahlen |
| Gleitkommazahl | Dezimalzahlen |
| Boolesch | Wahr / Falsch |
| Objekt | JSON-Objekt |
| Array | Liste von Werten |

---

## Expressions

Expressions sind Formeln. Im Gegensatz zu Dynamic Content berechnest du hier selbst Werte, statt sie nur weiterzugeben.

### Wo schreibt man Expressions?

Klicke in ein Eingabefeld, dann auf **Dynamischer Inhalt** → Wechsle zum Tab **Ausdruck**.

### Die wichtigsten Expressions

#### Text zusammensetzen: `concat`

```
concat('Hallo ', triggerBody()?['Name'], '!')
```

Ergebnis: `Hallo Max!`

#### Datum formatieren: `formatDateTime`

```
formatDateTime(utcNow(), 'dd.MM.yyyy HH:mm')
```

Ergebnis: `10.06.2026 14:30`

!!! tip "Deutsche Datumsbeschriftung"
    Power Automate liefert Datumsangaben im ISO-Format. Mit `formatDateTime` kannst du sie in jedes gewünschte Format umwandeln.

#### Bedingter Wert: `if`

```
if(equals(triggerBody()?['Priorität'], 'Hoch'), '🔴 DRINGEND', '🟢 Normal')
```

Ergebnis: Wenn Priorität = "Hoch", dann `🔴 DRINGEND`, sonst `🟢 Normal`.

#### Text in Großbuchstaben: `toUpper` / `toLower`

```
toUpper(triggerBody()?['Name'])
```

#### Länge prüfen: `length`

```
length(triggerBody()?['Beschreibung'])
```

Gibt die Zeichenzahl zurück – nützlich um zu prüfen ob ein Feld leer ist.

---

## Dynamic Content vs. Expressions

| | Dynamic Content | Expression |
|--|----------------|------------|
| Woher | Aus einem vorherigen Schritt | Du schreibst die Formel selbst |
| Wann | Wert einfach weitergeben | Wert berechnen, umwandeln, kombinieren |
| Beispiel | `Betreff der Email` | `concat('Re: ', triggerBody()?['subject'])` |

Du kannst Dynamic Content **innerhalb** einer Expression verwenden:

```
concat('Ticket von ', outputs('Antwortdetails_abrufen')?['body/Name'])
```

---

## JSON verstehen: `triggerBody()` und `body()`

Jeder Trigger und jede Action gibt JSON zurück. Mit `triggerBody()` greifst du auf die Daten des Triggers zu, mit `body('Schrittname')` auf einen bestimmten Schritt.

Beispiel: Der Forms-Trigger liefert:

```json
{
  "resourceData": {
    "responseId": "abc123"
  }
}
```

Zugriff: `triggerBody()?['resourceData']?['responseId']`

Das `?` ist wichtig: Es verhindert einen Fehler wenn das Feld nicht existiert (gibt dann `null` zurück statt einen Flow-Fehler).

!!! info "Expressions im Expressions-Tab testen"
    Schreibe deine Expression, klicke auf **OK** und speichere den Flow. Beim nächsten Testlauf siehst du in den Schritt-Details den berechneten Wert. So kannst du Expressions Schritt für Schritt debuggen.

---

## Praktisches Beispiel

Erweitere den Feedback-Flow aus Modul 1:

1. Füge zu Beginn eine **String-Variable** `ZusammenfassungsText` hinzu, initialisiert mit leerem Text
2. Nachdem die Antwortdetails abgerufen wurden, setze die Variable auf:
   ```
   concat('Feedback von ', outputs('Antwortdetails_abrufen')?['body/r4e10918e71334b3790add1d7e52b47e2'], ' am ', formatDateTime(utcNow(), 'dd.MM.yyyy'))
   ```
   *(Die kryptische ID ist der interne Feldname von Forms – Power Automate zeigt dir den richtigen Namen im Dynamic Content an)*

3. Nutze die Variable `ZusammenfassungsText` in der Teams-Nachricht

---

## Challenge

!!! question "Challenge: Datum und Priorität kombinieren"
    Erstelle einen manuellen Flow mit zwei Eingabe-Parametern (Textfelder im Trigger):
    
    - `Name` (Text)
    - `Priorität` (Text, einer von: Niedrig / Hoch)
    
    Der Flow soll:
    1. Eine Variable `BetreffZeile` erstellen vom Typ String
    2. Die Variable auf einen Wert setzen der Priorität, Name und Datum kombiniert – nutze `concat`, `if`, `equals` und `formatDateTime`
    3. Die Variable als Betreff einer Email an dich selbst nutzen

??? success "Hinweis"
    Beim Trigger **Manuell einen Flow auslösen** kannst du Eingabefelder definieren. Klicke auf **Eingabe hinzufügen** und wähle **Text**.
    
    Die Expression hat diese Grundstruktur – ersetze die `triggerBody()?['UNBEKANNT']`-Stellen durch den **Dynamic Content** des jeweiligen Eingabefelds:
    
    ```
    concat('[', if(equals(triggerBody()?['UNBEKANNT'], 'Hoch'), '🔴 HOCH', '🟢 NORMAL'), '] Anfrage von ', triggerBody()?['UNBEKANNT'], ' – ', formatDateTime(utcNow(), 'dd.MM.yyyy'))
    ```
    
    **So ersetzt du `UNBEKANNT` korrekt**: Die internen Feldnamen vergibt Power Automate automatisch – du kennst sie nicht im Voraus. Tippe die Expression bis zur Stelle wo `triggerBody()?['UNBEKANNT']` stehen soll, wechsle dann kurz auf den Tab **Dynamischer Inhalt** und wähle dort das gewünschte Feld (`Priorität` bzw. `Name`) aus. Power Automate fügt den richtigen Ausdruck automatisch ein.

---

Weiter zu [Modul 5 – Aufräumen](modul-5-aufräumen.md) →
