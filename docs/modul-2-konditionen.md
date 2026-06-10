# Modul 2 – Konditionen

## Lernziele

Nach diesem Modul kannst du:

- Einen Condition-Block (Wenn/Dann/Sonst) einbauen
- Einen Switch-Block für mehrere Fälle nutzen
- Flows abhängig von Werten unterschiedlich reagieren lassen

---

## Warum Konditionen?

Bisher läuft dein Flow immer gleich ab, egal was jemand eingibt. Aber in der Realität willst du unterschiedlich reagieren:

- Bei **"Verbesserungsbedarf"** → sofort eine Teams-Nachricht an den Vorgesetzten
- Bei **"Sehr zufrieden"** → nur eine kurze Bestätigung
- Wenn die Priorität **"Hoch"** ist → Flow läuft anders als bei **"Niedrig"**

Dafür gibt es den **Condition**-Block.

---

## Der Condition-Block

### Schritt 1: Bestehenden Flow öffnen

1. Öffne deinen `Feedback-Benachrichtigung`-Flow aus Modul 1
2. Klicke auf **Bearbeiten**

### Schritt 2: Condition hinzufügen

1. Klicke nach dem Schritt **Antwortdetails abrufen** auf das **+** Symbol → **Aktion hinzufügen**
2. Suche nach `Condition` und wähle **Condition** (unter "Steuerelemente")
3. Du siehst jetzt einen Block mit **Wenn ja** und **Wenn nein**

### Schritt 3: Bedingung konfigurieren

Klicke auf **Wert auswählen** links:

1. Klicke auf den Blitz (Dynamischer Inhalt)
2. Wähle **Wie zufrieden bist du?** (aus dem Antwortdetails-Schritt)

Setze die Bedingung:

| Feld | Wert |
|------|------|
| Linker Wert | `Wie zufrieden bist du?` (dynamisch) |
| Operator | `ist gleich` |
| Rechter Wert | `Verbesserungsbedarf` (getippt) |

### Schritt 4: Zweige befüllen

**Im "Wenn ja"-Zweig** (Bedingung trifft zu):

1. Klicke auf **Aktion hinzufügen** im "Wenn ja"-Zweig
2. Füge eine **Teams-Chatnachricht** hinzu mit dem Text:
   `⚠️ Achtung: Negatives Feedback von [Name] – bitte nachfassen!`
3. Sende es an dich selbst oder einen zuständigen Kanal

**Im "Wenn nein"-Zweig** (Bedingung trifft nicht zu):

1. Klicke auf **Aktion hinzufügen** im "Wenn nein"-Zweig
2. Füge eine **Teams-Chatnachricht** hinzu mit dem Text:
   `✅ Positives Feedback von [Name] eingegangen.`

!!! tip "Bestehende Schritte verschieben"
    Du kannst den ursprünglichen Teams-Schritt aus Modul 1 löschen und stattdessen die konditionalen Nachrichten nutzen. Oder du behältst beide – dann sendet der Flow immer die allgemeine Nachricht UND die konditionale.

---

## Mehrere Bedingungen kombinieren

Manchmal reicht eine einfache Ja/Nein-Bedingung nicht. Du kannst Bedingungen **kombinieren**:

- Klicke auf **+ Hinzufügen** innerhalb des Condition-Blocks
- Wähle **Zeile hinzufügen** für `UND` (beide müssen wahr sein)
- Wähle **Gruppe hinzufügen** für `ODER` (mindestens eine muss wahr sein)

Beispiel: Nur reagieren wenn **Name nicht leer** UND **Zufriedenheit = Verbesserungsbedarf**:

| Zeile 1 | `Name` `ist nicht gleich` ` ` (leer) |
| Zeile 2 | `Wie zufrieden bist du?` `ist gleich` `Verbesserungsbedarf` |

---

## Der Switch-Block

Wenn du mehr als zwei Fälle hast, wird ein Switch übersichtlicher als verschachtelte Conditions.

### Switch einbauen

1. Füge nach **Antwortdetails abrufen** eine neue Aktion hinzu
2. Suche nach `Switch` (unter "Steuerelemente")
3. Im **Ein**-Feld: Wähle **Wie zufrieden bist du?** (dynamisch)

Jetzt kannst du Fälle (Cases) hinzufügen:

| Case | Aktion |
|------|--------|
| `Sehr zufrieden` | Teams: `🌟 Top-Feedback von [Name]!` |
| `Zufrieden` | Teams: `👍 Feedback von [Name] – alles okay.` |
| `Verbesserungsbedarf` | Teams: `⚠️ Kritisches Feedback von [Name]!` |

Klicke auf **Fall hinzufügen** um neue Cases zu ergänzen. Der **Standard**-Case fängt alles ab was in keinen der definierten Cases passt.

---

## Challenge

!!! question "Challenge: Neues Formular, neuer Switch"
    1. Erstelle in Microsoft Forms ein neues Formular mit dem Titel `IT-Helpdesk-Ticket`
    2. Frage 1 (Text): `Beschreibung des Problems`
    3. Frage 2 (Auswahl): `Priorität` mit den Optionen `Niedrig`, `Mittel`, `Hoch`, `Kritisch`
    4. Erstelle einen neuen Flow der bei neuer Forms-Antwort einen **Switch** auf die Priorität macht
    5. Bei `Hoch` und `Kritisch`: Teams-Nachricht mit rotem Hinweis
    6. Bei `Niedrig` und `Mittel`: Teams-Nachricht als einfache Info

??? success "Hinweis"
    Du brauchst zwei Schritte vor dem Switch: den Forms-Trigger und **Antwortdetails abrufen**. Der Switch kommt erst danach, weil du die Antwortdetails brauchst um auf den Wert der Priorität-Frage zugreifen zu können.

---

Weiter zu [Modul 3 – Schleifen](modul-3-schleifen.md) →
