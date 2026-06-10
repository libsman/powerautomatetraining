# Modul 6 – Outlook

## Lernziele

Nach diesem Modul kannst du:

- Einen Flow starten wenn eine neue Email ankommt
- Emails automatisch auswerten und weiterleiten
- Anhänge aus Emails automatisch in SharePoint speichern

---

## Outlook als Automatisierungs-Zentrale

Viele Arbeitsprozesse beginnen mit einer Email: ein Kunde schreibt, eine Bewerbung kommt rein, eine Bestellung wird bestätigt. Power Automate kann auf jede eingehende Email reagieren – und das deutlich flexibler als die eingebauten Outlook-Regeln.

---

## Trigger: Wenn eine neue Email ankommt

### Schritt 1: Neuen Flow erstellen

1. Gehe zu [make.powerautomate.com](https://make.powerautomate.com) → **Erstellen**
2. Wähle **Automatisierter Cloudflow**
3. Name: `Email-zu-SharePoint`
4. Suche nach `Office 365 Outlook` und wähle den Trigger **Wenn eine neue E-Mail eingeht (V3)**
5. Klicke auf **Erstellen**

### Schritt 2: Trigger konfigurieren

| Feld | Wert |
|------|------|
| Ordner | `Posteingang` |
| Nur mit Anlagen | `Ja` |
| Anlagen einschließen | `Ja` |

!!! tip "Emails filtern"
    Klappe den Trigger auf (Klick auf **Erweiterte Optionen anzeigen**). Du kannst nach Absender (`Von`), Betreff-Enthält oder Empfänger filtern – so reagiert der Flow nur auf relevante Emails.

---

## Anhänge in SharePoint speichern

### Schritt 3: Apply to each für Anhänge

Emails können mehrere Anhänge haben. Deshalb brauchst du eine Schleife.

1. Klicke auf **+ Neuen Schritt**
2. Suche nach `Apply to each`
3. Im **Ausgabe**-Feld: Klicke auf Blitz → **Anlagen** (aus dem Outlook-Trigger)

### Schritt 4: Datei in SharePoint speichern

Füge innerhalb des `Apply to each`-Blocks eine Aktion hinzu:

1. Suche nach `SharePoint` → **Datei erstellen**
2. Konfiguriere:

| Feld | Wert |
|------|------|
| Websiteadresse | Deine SharePoint-Site |
| Ordnerpfad | `/Freigegebene Dokumente/Email-Anhänge` |
| Dateiname | Dynamisch: **Name der Anlage** |
| Dateiinhalt | Dynamisch: **Inhalt der Anlage** |

!!! success "Fertig"
    Jede eingehende Email mit Anhang wird jetzt automatisch in SharePoint archiviert. Kein manuelles Speichern mehr.

---

## Email senden und weiterleiten

### Email senden (V2)

Neben dem Trigger kannst du Outlook auch als Action nutzen:

1. Neue Aktion → **E-Mail senden (V2)**

| Feld | Wert |
|------|------|
| An | Empfänger (dynamisch oder fest) |
| Betreff | Text + Dynamic Content |
| Text | HTML oder Plaintext |
| CC/BCC | Optional |

!!! tip "HTML in Emails"
    Im Text-Feld kannst du HTML verwenden. Klicke auf `</>` um zwischen Klartext und HTML umzuschalten. So kannst du Tabellen, fette Texte oder Farben einbauen.

### Email weiterleiten

1. Neue Aktion → **E-Mail weiterleiten (V2)**
2. Message-ID: Dynamisch aus dem Trigger → **Message-ID**
3. An: Weiterleitungsempfänger

---

## Outlook-Kategorie setzen

Du kannst Emails automatisch kategorisieren – das ersetzt manuelle Outlook-Regeln:

1. Neue Aktion → **E-Mail kategorisieren (V2)**
2. Message-ID: Dynamisch → **Message-ID**
3. Kategorien: z.B. `Wichtig` oder `Zu bearbeiten`

---

## Challenge

!!! question "Challenge: Automatisches Ticket-Routing"
    Erstelle einen Flow der auf neue Emails im Posteingang reagiert:
    
    1. Trigger: **Wenn eine neue E-Mail eingeht (V3)**, filter auf Emails mit dem Betreff-Enthält: `[TICKET]`
    2. Extrahiere Betreff und Absender
    3. Condition: Wenn der Absender eine bestimmte Domain hat (z.B. `@firma.de`), sende eine Teams-Nachricht: `Internes Ticket von [Absender]: [Betreff]`
    4. Sonst: Sende eine Bestätigungs-Email an den Absender: `Ihr Ticket wurde eingegangen.`

??? success "Hinweis"
    Für die Domain-Prüfung nutze die Expression `contains(triggerOutputs()?['body/from'], '@firma.de')` im Condition-Block. Ersetze `@firma.de` durch eine echte Domain deiner Wahl.

---

Weiter zu [Modul 7 – Teams](modul-7-teams.md) →
