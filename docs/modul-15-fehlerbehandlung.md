# Modul 15 – Fehlerbehandlung

## Lernziele

Nach diesem Modul kannst du:

- **Run After** konfigurieren um auf fehlgeschlagene Schritte zu reagieren
- Ein Try/Catch-Pattern mit **Scope** umsetzen
- Die **Retry Policy** anpassen

---

## Warum Fehlerbehandlung?

Production-Flows scheitern – das ist normal. Eine SharePoint-Seite ist kurz offline, eine externe API antwortet nicht, ein Feld ist leer das nicht leer sein darf. Ohne Fehlerbehandlung:

- Der Flow schlägt still fehl
- Niemand bemerkt es
- Daten gehen verloren oder Prozesse bleiben stecken

Mit guter Fehlerbehandlung:
- Du bekommst sofort eine Benachrichtigung
- Der Flow versucht es automatisch erneut
- Du weißt genau welcher Schritt warum fehlgeschlagen ist

---

## Run After

**Run After** kontrolliert wann ein Schritt ausgeführt wird, basierend auf dem Status des vorherigen Schritts.

### Standardverhalten

Normalerweise läuft jeder Schritt nur wenn der vorherige **erfolgreich** war.

### Run After konfigurieren

1. Klicke auf den Folgeschritt → drei Punkte (`...`) → **Run after konfigurieren**
2. Du siehst vier Optionen:

| Option | Bedeutung |
|--------|-----------|
| ✅ War erfolgreich | Standard – Schritt läuft wenn vorheriger OK war |
| ❌ Ist fehlgeschlagen | Schritt läuft wenn vorheriger einen Fehler hatte |
| ⏭️ Wurde übersprungen | Schritt läuft wenn vorheriger übersprungen wurde |
| ⏱️ Zeitüberschreitung | Schritt läuft wenn vorheriger Timeout hatte |

Du kannst **mehrere Optionen gleichzeitig** wählen.

### Praxisbeispiel: Fehler-Email

1. Baue deinen Flow normal auf
2. Füge am Ende eine **Email-Aktion** hinzu
3. Konfiguriere Run After: Nur **Ist fehlgeschlagen** und **Zeitüberschreitung**
4. Email an dich: `Flow-Fehler aufgetreten! Bitte prüfen.`

---

## Try/Catch mit Scope

Für komplexere Flows ist ein **Try/Catch**-Muster mit dem **Scope**-Block eleganter.

### Was ist ein Scope?

Ein **Scope** (unter Steuerelemente) ist ein Container der mehrere Aktionen gruppiert. Du kannst dann auf den **Status des gesamten Scopes** reagieren – nicht nur auf einzelne Schritte.

### Try/Catch aufbauen

**Schritt 1 – Try-Scope:**
1. Neue Aktion → **Bereich** (Scope)
2. Benenne ihn: `Try`
3. Füge alle Hauptschritte des Flows in diesen Scope ein

**Schritt 2 – Catch-Scope:**
1. Neue Aktion nach dem Try-Scope → **Bereich** (Scope)
2. Benenne ihn: `Catch`
3. Run After: Nur **Ist fehlgeschlagen** und **Zeitüberschreitung**
4. Innerhalb:
   - Neue Aktion: **Fehlermeldung erstellen** (Compose / Erstellen)
   - Wert: `concat('Fehler im Flow: ', result('Try')[0]?['error']?['message'])`
   - Email oder Teams-Nachricht mit dem Fehlertext

**Schritt 3 – Abschluss-Scope:**
1. Neuer Scope: `Finally` (optional)
2. Run After: **War erfolgreich**, **Ist fehlgeschlagen**, **Zeitüberschreitung**, **Wurde übersprungen** – alle Haken!
3. Läuft immer – z.B. für Cleanup-Aktionen

---

## Retry Policy

Wenn ein einzelner Schritt fehlschlägt, kann Power Automate es automatisch erneut versuchen.

### Retry Policy anpassen

1. Klicke auf den Schritt → drei Punkte → **Einstellungen**
2. **Wiederholungsrichtlinie**:

| Typ | Verhalten |
|-----|-----------|
| Standard | 4 Versuche mit exponential Backoff |
| Keine | Kein Retry |
| Fest | X Versuche mit Y Sekunden Abstand |
| Exponentiell | Wachsende Wartezeit zwischen Versuchen |

!!! tip "Wann Retry sinnvoll ist"
    Retry hilft bei **temporären Fehlern** (Netzwerk, API kurz down). Bei **dauerhaften Fehlern** (falsche Konfiguration, fehlende Berechtigung) bringt Retry nichts – lieber mit Catch behandeln.

---

## Fehler-Diagnose

Wenn ein Flow fehlschlägt, siehst du in der Ausführungshistorie genau was passiert ist:

1. **Meine Flows** → Flow anklicken → **Ausführungshistorie**
2. Fehlgeschlagene Ausführung anklicken
3. Jeden Schritt aufklappen – roter Schritt zeigt den Fehlertext

Wichtige Fehlercodes:

| Code | Bedeutung |
|------|-----------|
| 400 | Schlechte Anfrage – Konfigurationsfehler |
| 401 | Nicht autorisiert – Connection abgelaufen |
| 404 | Nicht gefunden – z.B. SharePoint-Eintrag gelöscht |
| 429 | Zu viele Anfragen – API Rate Limit |
| 500 | Interner Serverfehler beim Zieldienst |

---

## Challenge

!!! question "Challenge: Robuster Feedback-Flow"
    Nimm den Feedback-Flow aus Modul 1 und mache ihn production-ready:
    
    1. Füge einen **Try**-Scope hinzu und verschiebe alle bestehenden Schritte dort hinein
    2. Füge einen **Catch**-Scope hinzu (Run After: fehlgeschlagen)
    3. Im Catch: Sende eine Email an dich selbst mit dem Fehlertext via `result('Try')[0]?['error']?['message']`
    4. Passe die Retry Policy des Teams-Schritts auf **Fest: 3 Versuche, 30 Sekunden Abstand** an

??? success "Hinweis"
    Um Schritte in einen Scope zu verschieben: Erstelle zuerst den leeren Scope, dann schneide die Schritte aus (drei Punkte → Ausschneiden) und füge sie innerhalb des Scopes wieder ein. Alternativ: beim Erstellen neuer Schritte direkt im Scope auf **Aktion hinzufügen** klicken.

---

Weiter zu [Modul 16 – HTTP Connector](modul-16-http-connector.md) →
