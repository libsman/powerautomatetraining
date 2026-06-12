# Modul 3 – Schleifen

## Lernziele

Nach diesem Modul kannst du:

- Mit **Apply to each** über eine Liste von Elementen iterieren
- Mit **Do until** einen Flow wiederholen bis eine Bedingung erfüllt ist
- Arrays mit **Filter Array** und **Select** gezielt bearbeiten

---

## Warum Schleifen?

Viele Flows arbeiten nicht mit einzelnen Werten sondern mit **Listen**:

- Eine SharePoint-Liste hat 50 Einträge → bearbeite jeden einzeln
- Eine Email-Anlage enthält mehrere Dateien → verarbeite jede
- Du möchtest alle Teilnehmer eines Meetings per Email benachrichtigen

Ohne Schleifen müsstest du jeden Schritt manuell für jedes Element einfügen. Mit **Apply to each** geht das in einem einzigen Block.

---

## Apply to each

`Apply to each` (deutsch: *Auf jedes Element anwenden*) ist die häufigste Schleife in Power Automate.

### Beispiel: Alle SP-Einträge per Email benachrichtigen

**Szenario**: Du hast eine SharePoint-Liste mit Projektmitgliedern und willst jedem eine Reminder-Email schicken.

#### Schritt 1: Neuen Flow erstellen

1. Gehe zu [make.powerautomate.com](https://make.powerautomate.com) → **Erstellen**
2. Wähle **Sofortiger Cloudflow** (manuell auslösen)
3. Name: `Schleife-Demo`
4. Trigger: **Manuell einen Flow auslösen**

#### Schritt 2: SP-Elemente holen

1. Klicke auf **+ Neuen Schritt hinzufügen**
2. Suche nach `SharePoint` → **Elemente abrufen**
3. Wähle deine SharePoint-Site und eine Liste (z.B. deine Projektmitglieder-Liste)

!!! info "Keine SharePoint-Liste zur Hand?"
    Erstelle kurz eine einfache SharePoint-Liste mit zwei Spalten: `Name` (Text) und `Email` (Text). Füge 3–4 Testeinträge ein. Die Site-URL findest du in deinem SharePoint-Bereich.

#### Schritt 3: Apply to each

1. Klicke auf **+ Neuen Schritt**
2. Suche nach `Apply to each`
3. Im Feld **Ausgabe von vorherigen Schritten auswählen**: Klicke auf den Blitz → **Wert** (aus dem SharePoint-Schritt)

Jetzt ist alles innerhalb des `Apply to each`-Blocks wird für jedes Listenelement einmal ausgeführt.

#### Schritt 4: Action innerhalb der Schleife

1. Klicke **Aktion hinzufügen** innerhalb des Blocks
2. Füge **Email senden (V2)** hinzu

| Feld | Wert |
|------|------|
| An | Dynamisch: **Email** (aus SharePoint-Element) |
| Betreff | `Erinnerung für [Name]` |
| Text | `Hallo [Name], bitte prüfe deine offenen Aufgaben.` |

!!! warning "Massen-Emails im Test vermeiden"
    Nutze beim Testen deine eigene Email-Adresse im **An**-Feld statt echter Empfänger. Ändere es erst wenn der Flow korrekt funktioniert.

---

## Filter Array

Oft willst du nicht **alle** Elemente einer Liste verarbeiten, sondern nur bestimmte. Dafür gibt es **Filter Array**.

### Vorbereitung: Liste erweitern

Wir bauen auf der SharePoint-Liste aus dem vorherigen Beispiel (`Name`, `Email`) auf. Füge dort eine dritte Spalte hinzu:

1. Öffne die SharePoint-Liste im Browser
2. Klicke auf **+ Spalte hinzufügen** → **Auswahl**
3. Spaltenname: `Priorität`
4. Füge als Auswahlwerte `Kritisch` und `Normal` ein
5. Aktualisiere deine bestehenden Testeinträge: weise einigen `Kritisch` und anderen `Normal` zu

### Beispiel: Nur "Kritisch"-Einträge verarbeiten

Ergänze den Flow `Schleife-Demo` – füge **zwischen** dem SharePoint-Schritt und dem `Apply to each` einen neuen Schritt ein:

1. Klicke auf das **+** zwischen den beiden Schritten → **Aktion hinzufügen**
2. Suche nach `Filter Array` (unter "Datenoperationen")
3. **Von**: Klicke auf den Blitz → **Wert** (aus dem SharePoint-Schritt „Elemente abrufen")
4. **Bedingung**:

| Feld | Wert |
|------|------|
| Linker Wert | Klicke auf Blitz → wähle **Priorität** |
| Operator | `ist gleich` |
| Rechter Wert | `Kritisch` |

5. Im `Apply to each`-Schritt darunter: aktualisiere das Eingabefeld **Ausgabe von vorherigen Schritten** auf den Output des **Filter Array**-Schritts (statt direkt den SharePoint-Wert)

Das Ergebnis ist eine gefilterte Liste – der `Apply to each` verarbeitet jetzt nur noch die Einträge mit Priorität `Kritisch`.

---

## Select

**Select** (unter "Datenoperationen") transformiert jedes Element einer Liste in ein neues Format – du wählst nur die Felder die du brauchst.

Beispiel: Du hast eine SP-Liste mit 10 Spalten, willst aber nur `Name` und `Email` weiterverarbeiten:

1. Neue Aktion → **Auswählen** (Select)
2. **Von**: Deine Liste
3. **Zuordnen** (Map):
   - Links: `name` (selbst getippt – das ist der neue Feldname)
   - Rechts: Blitz → **Name** (aus der SP-Liste)
   - Klicke auf **+ Neues Element** und wiederhole für `email` → **Email**

---

## Do until

`Do until` wiederholt einen Block so lange bis eine Bedingung wahr wird. Nützlich z.B. wenn du auf eine externe Antwort wartest.

!!! warning "Vorsicht mit Do until"
    `Do until` kann in Endlosschleifen enden wenn die Bedingung nie erfüllt wird. Setze immer ein **Limit** (z.B. max. 10 Durchläufe oder Timeout nach 1 Stunde) im Block-Einstellungen (drei Punkte → Einstellungen).

Typische Nutzung: Warte bis ein Status-Feld in SharePoint auf "Abgeschlossen" gesetzt wird.

---

## Challenge

!!! question "Challenge: Gefilterte Schleife"
    1. Erstelle eine SharePoint-Liste mit den Spalten `Aufgabe` (Text), `Status` (Auswahl: Offen/Erledigt) und `Zuständig` (Text)
    2. Füge mindestens 4 Einträge ein, davon 2 mit Status `Offen`
    3. Erstelle einen manuellen Flow der:
        - Alle Listenelemente abruft
        - Per **Filter Array** nur die Einträge mit Status `Offen` behält
        - Mit **Apply to each** über die gefilterten Einträge läuft
        - Für jeden offenen Eintrag eine Email an dich selbst schickt mit dem Aufgabentext

??? success "Hinweis"
    Denke daran: Nach dem Filter Array liefert der Block ein **Array** – das ist der richtige Input für das `Apply to each`. Wenn du dynamischen Inhalt im `Apply to each`-Feld auswählst, erscheint der Filter-Array-Output unter dem Namen des Filter-Schritts.

---

Weiter zu [Modul 4 – Variablen & Expressions](modul-4-variablen-expressions.md) →
