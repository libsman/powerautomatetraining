# Modul 13 – Genehmigungen (Grundlagen)

## Lernziele

Nach diesem Modul kannst du:

- Einen einfachen Genehmigungsflow mit einem Genehmiger aufbauen
- Auf Approve/Reject unterschiedlich reagieren
- Das Ergebnis in SharePoint speichern und per Email bestätigen

---

## Was sind Approval-Flows?

Genehmigungsprozesse gehören zu den häufigsten Workflows in Unternehmen: Urlaubsanträge, Bestellfreigaben, Kostenerstattungen, Inhaltsfreigaben. Ohne Digitalisierung laufen diese per Email hin und her – unstrukturiert und schwer nachverfolgbar.

Mit dem **Approvals**-Connector baut Power Automate einen strukturierten Genehmigungsprozess:

1. Antragsteller füllt ein Formular aus
2. Der Genehmiger bekommt eine Approval-Anfrage per Email (oder in Teams)
3. Der Genehmiger klickt auf **Genehmigen** oder **Ablehnen**
4. Power Automate reagiert automatisch auf die Entscheidung

---

## Schritt 1: Formular erstellen

Erstelle in Microsoft Forms ein neues Formular:

- Titel: `Urlaubsantrag`
- Frage 1 (Text): `Dein Name`
- Frage 2 (Datum): `Von` (Startdatum)
- Frage 3 (Datum): `Bis` (Enddatum)
- Frage 4 (Text, optional): `Bemerkung`

---

## Schritt 2: Flow erstellen

1. Neuer automatisierter Flow → Name: `Urlaubsantrag-Flow`
2. Trigger: **When a new response is submitted** (Microsoft Forms)
3. Wähle dein Formular `Urlaubsantrag`

---

## Schritt 3: Antwortdetails abrufen

Füge den Schritt **Antwortdetails abrufen** hinzu (wie in Modul 1).

---

## Schritt 4: Genehmigung starten

1. Neue Aktion → suche nach `Approvals`
2. Wähle: **Genehmigung starten und auf Antwort warten**

| Feld | Wert |
|------|------|
| Genehmigungstyp | `Genehmigen/Ablehnen – Erster zum Antworten` |
| Titel | `Urlaubsantrag von [Name]` |
| Zugewiesen an | Email-Adresse des Genehmigers (fest oder dynamisch) |
| Details | `Von: [Von-Datum] bis [Bis-Datum]\nBemerkung: [Bemerkung]` |
| Anfrageseite | Optional: Link zum Formular oder SharePoint-Eintrag |

!!! warning "Flow wartet hier"
    Der Schritt **Starten und warten** pausiert den Flow bis der Genehmiger antwortet. Das kann Minuten, Stunden oder Tage dauern. Der Flow läuft im Hintergrund – er verbraucht keine Ressourcen während er wartet.

!!! info "Genehmigung in Teams sehen"
    Der Genehmiger bekommt eine Email UND eine Teams-Nachricht im **Approvals**-Tab in Teams (Teams → Linkes Menü → Approvals). Dort kann er direkt genehmigen oder ablehnen.

---

## Schritt 5: Auf die Entscheidung reagieren

Nach dem Approval-Schritt ist das Ergebnis in **Ergebnis** (Dynamic Content) verfügbar.

1. Neue Aktion → **Condition**
2. Linker Wert: Dynamisch → **Ergebnis** (aus dem Approval-Schritt)
3. Operator: `ist gleich`
4. Rechter Wert: `Approve` (Achtung: englisch, auch wenn die Oberfläche auf Deutsch ist)

**Im "Wenn ja"-Zweig (Genehmigt):**
- **Email senden** an den Antragsteller: `Dein Urlaubsantrag wurde genehmigt! ✅`

**Im "Wenn nein"-Zweig (Abgelehnt):**
- **Email senden** an den Antragsteller: `Dein Urlaubsantrag wurde leider abgelehnt. ❌\n\nKommentar: [Kommentare]`

!!! tip "Kommentar des Genehmigers"
    Im Dynamic Content des Approval-Schritts findest du **Kommentare** – das ist was der Genehmiger optional eingetragen hat. Füge es in die Ablehnungs-Email ein.

---

## Schritt 6: Ergebnis in SharePoint speichern

Damit du alle Anträge nachverfolgen kannst, speichere das Ergebnis in einer SharePoint-Liste.

1. Erstelle eine SharePoint-Liste `Urlaubsanträge` mit den Spalten: `Antragsteller`, `VonDatum`, `BisDatum`, `Status`, `Kommentar`
2. Füge **sowohl im "Ja"- als auch im "Nein"-Zweig** einen Schritt **Element erstellen** (SharePoint) hinzu:

| Feld | Wert |
|------|------|
| Antragsteller | Dynamisch → **Name** (aus Formular) |
| VonDatum | Dynamisch → **Von** (aus Formular) |
| BisDatum | Dynamisch → **Bis** (aus Formular) |
| Status | Im Ja-Zweig: `Genehmigt`, im Nein-Zweig: `Abgelehnt` |
| Kommentar | Dynamisch → **Kommentare** (aus Approval) |

---

## Challenge

!!! question "Challenge: Bestätigungs-Email an den Genehmiger"
    Erweitere den Flow:
    
    Nach der Entscheidung (egal ob Ja oder Nein) soll der Genehmiger eine Bestätigungs-Email bekommen:
    `Du hast den Urlaubsantrag von [Name] [genehmigt / abgelehnt].`
    
    Tipp: Nutze eine Expression für den Text:
    `if(equals(outputs('Genehmigung_starten')?['body/outcome'], 'Approve'), 'genehmigt', 'abgelehnt')`

??? success "Hinweis"
    Die Email-Adresse des Genehmigers bekommst du aus dem Approval-Schritt via Dynamischer Inhalt → **Zugewiesen an Email** oder du nimmst die fest eingetragene Email-Adresse.

---

Weiter zu [Modul 14 – Genehmigungen (Advanced)](modul-14-approvals-advanced.md) →
