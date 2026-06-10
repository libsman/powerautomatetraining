# Modul 18 – Szenario: HR-Onboarding-Prozess

## Lernziele

Nach diesem Modul hast du einen vollständigen HR-Onboarding-Flow gebaut der:

- Neue Mitarbeiter in SharePoint und Teams anlegt
- Den Manager um Freigabe von Zugängen bittet (Approval)
- Begrüßungs-Email und Welcome-Teams-Nachricht verschickt
- Eine Checkliste für HR in SharePoint erstellt

---

## Übersicht

```
HR füllt Onboarding-Formular aus
      ↓
MA-Daten in SharePoint speichern
      ↓
Approval: Manager genehmigt Zugangsberechtigungen
      ↓
Wenn genehmigt:
  → Begrüßungs-Email an neuen MA
  → Teams-Willkommensnachricht im Team-Kanal
  → Onboarding-Checkliste in SharePoint erstellen
Wenn abgelehnt:
  → Info-Email an HR
```

---

## Vorbereitung: Ressourcen anlegen

### 1. Microsoft Forms: Onboarding-Formular

Erstelle `Neuer Mitarbeiter – Onboarding` mit:

| Frage | Typ |
|-------|-----|
| Vorname | Text |
| Nachname | Text |
| Email (neue MA-Adresse) | Text |
| Startdatum | Datum |
| Abteilung | Auswahl: IT/HR/Vertrieb/Marketing/Finanzen |
| Manager Email | Text |
| Benötigte Systeme | Auswahl (Mehrfach): Office 365/Teams/SharePoint/CRM/ERP |

### 2. SharePoint-Liste: Mitarbeiter

Erstelle eine Liste `Mitarbeiter-Onboarding` mit:

| Spalte | Typ |
|--------|-----|
| Titel (Standard) | Text (= Vollständiger Name) |
| Email | Text |
| Startdatum | Datum |
| Abteilung | Auswahl |
| ManagerEmail | Text |
| Status | Auswahl: Ausstehend/Genehmigt/Abgelehnt |
| Systeme | Text (mehrzeilig) |

### 3. SharePoint-Liste: Onboarding-Checkliste

Erstelle eine Liste `Onboarding-Checkliste` mit:

| Spalte | Typ |
|--------|-----|
| Titel | Text (= Aufgabe) |
| Zuständig | Text |
| Status | Auswahl: Offen/Erledigt |
| Mitarbeiter | Text (Name des neuen MA) |

---

## Flow aufbauen

### Trigger + Antwortdetails

1. Neuer Flow → Name: `HR-Onboarding`
2. Trigger: Forms → `Neuer Mitarbeiter – Onboarding`
3. **Antwortdetails abrufen**

### Variable: Vollständiger Name

1. **Variable initialisieren**: `VollName`, Typ: String
2. **Variable festlegen**: Expression:
   `concat(outputs('Antwortdetails_abrufen')?['body/Vorname'], ' ', outputs('Antwortdetails_abrufen')?['body/Nachname'])`

### SP-Element: Mitarbeiter anlegen

**Element erstellen** in `Mitarbeiter-Onboarding`:

| Feld | Wert |
|------|------|
| Titel | Variable → **VollName** |
| Email | Dynamisch → **Email** |
| Startdatum | Dynamisch → **Startdatum** |
| Abteilung | Dynamisch → **Abteilung** |
| ManagerEmail | Dynamisch → **Manager Email** |
| Status | `Ausstehend` |

### Approval: Manager genehmigt

**Genehmigung starten und auf Antwort warten**:

| Feld | Wert |
|------|------|
| Titel | `Zugangsberechtigungen für neuen MA: [VollName]` |
| Zugewiesen an | Dynamisch → **Manager Email** |
| Details | `Startdatum: [Startdatum]\nAbteilung: [Abteilung]\nBenötigte Systeme: [Benötigte Systeme]` |

### Condition: Genehmigt?

**Wenn ja (Approve):**

1. **SP Element aktualisieren** → Status: `Genehmigt`

2. **E-Mail senden (V2)** – Willkommens-Email an neuen MA:
   - An: Dynamisch → **Email**
   - Betreff: `Willkommen bei uns, [Vorname]! 🎉`
   - Text: 
     ```
     Hallo [Vorname],
     
     wir freuen uns dich bald in unserem Team willkommen zu heißen!
     
     Dein Startdatum: [Startdatum]
     Deine Abteilung: [Abteilung]
     
     Dein Manager meldet sich in Kürze mit weiteren Informationen.
     
     Viel Erfolg!
     ```

3. **Teams-Nachricht** im Abteilungs-Kanal:
   `👋 Neues Teammitglied ab [Startdatum]: **[VollName]** stößt zur Abteilung [Abteilung] dazu. Herzlich willkommen!`

4. **Apply to each**: Onboarding-Checkliste erstellen
   
   Erstelle eine Liste mit Standard-Onboarding-Aufgaben. Für dieses Beispiel nutze ein **Array initialisieren** mit dem Wert:
   ```json
   ["Ausweis-Kopie einreichen", "Vertrag unterzeichnen", "IT-Equipment ausgeben", "Büro-Führung", "Team-Meeting am ersten Tag"]
   ```
   
   Im Apply to each → **Element erstellen** in `Onboarding-Checkliste`:
   - Titel: Aktuelles Array-Element
   - Zuständig: `HR`
   - Status: `Offen`
   - Mitarbeiter: Variable **VollName**

**Wenn nein (Reject):**

1. **SP Element aktualisieren** → Status: `Abgelehnt`

2. **E-Mail an HR**: `Der Onboarding-Antrag für [VollName] wurde vom Manager abgelehnt. Kommentar: [Kommentare]`

---

## Challenge

!!! question "Challenge: Startdatum-Erinnerung"
    Ergänze den Flow um eine automatische Erinnerung:
    
    Erstelle einen **Scheduled Flow** der täglich läuft:
    1. Lese alle Mitarbeiter aus `Mitarbeiter-Onboarding` mit Status `Genehmigt`
    2. Prüfe für jeden ob das Startdatum **morgen** ist (Vergleich mit `addDays(utcNow(), 1)`)
    3. Wenn ja: Sende eine Teams-Nachricht an den Manager: `Erinnerung: [VollName] startet morgen. Ist alles vorbereitet?`

??? success "Hinweis"
    Für den Datumsvergleich: Formatiere sowohl das SP-Startdatum als auch `addDays(utcNow(), 1)` auf dasselbe Format (`yyyy-MM-dd`) und vergleiche die Strings. Im Condition-Block im **Ausdruck**-Tab: `equals(formatDateTime(items('Apply_to_each')?['Startdatum'], 'yyyy-MM-dd'), formatDateTime(addDays(utcNow(), 1), 'yyyy-MM-dd'))`.

---

Weiter zu [Modul 19 – Aufräumen](modul-19-aufräumen.md) →
