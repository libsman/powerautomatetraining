# Modul 16 – HTTP Connector

## Lernziele

Nach diesem Modul kannst du:

- Den HTTP-Connector für GET- und POST-Requests nutzen
- Eine öffentliche REST-API ansprechen
- Die JSON-Antwort parsen und weiterverarbeiten

---

!!! warning "Premium-Connector"
    Der HTTP-Connector ist ein **Premium-Connector** und erfordert eine **Power Automate Premium-Lizenz** (früher: Plan 1 oder Plan 2) oder eine kostenlose 90-Tage-Testlizenz.
    
    Teste ihn während der Testzeitraum aktiv ist oder frage deinen Administrator. Alle anderen Module in diesem Training laufen ohne Premium.

---

## Was ist der HTTP Connector?

Der HTTP-Connector macht Power Automate universell: Jeder Dienst der eine REST-API hat, kann angesprochen werden – auch wenn es keinen fertigen Connector gibt.

Anwendungsfälle:
- Wetterdaten von einer öffentlichen API holen
- Einen eigenen Backend-Service ansprechen
- SAP, Jira, externe CRM-Systeme die keine fertigen Connectors haben
- Webhook-Empfänger triggern

---

## HTTP GET: Daten holen

### Beispiel: Öffentliche Wetter-API

Wir nutzen die kostenlose API von [Open-Meteo](https://open-meteo.com/) – keine Registrierung nötig.

#### Schritt 1: Manuellen Flow erstellen

1. Neuer manueller Flow → Name: `HTTP-Demo`

#### Schritt 2: HTTP-Action hinzufügen

1. Neue Aktion → suche nach `HTTP`
2. Wähle **HTTP** (unter dem Premium-Banner)

| Feld | Wert |
|------|------|
| Methode | `GET` |
| URI | `https://api.open-meteo.com/v1/forecast?latitude=52.52&longitude=13.41&current_weather=true` |

Das ist Berlin. Die API gibt sofort JSON zurück ohne Authentifizierung.

#### Schritt 3: JSON parsen

Die HTTP-Action liefert einen rohen JSON-String. Um die Felder als Dynamic Content nutzen zu können, musst du ihn parsen.

1. Neue Aktion → **JSON analysieren** (unter "Datenoperationen")
2. **Inhalt**: Dynamisch → **Text** (aus dem HTTP-Schritt)
3. **Schema**: Klicke auf **Beispielnutzlast zum Generieren des Schemas verwenden** und füge dieses Beispiel-JSON ein:

```json
{
  "current_weather": {
    "temperature": 18.5,
    "windspeed": 12.3,
    "weathercode": 0,
    "time": "2026-06-10T14:00"
  }
}
```

Klicke auf **Fertig** – Power Automate generiert das Schema automatisch.

#### Schritt 4: Ergebnis verwenden

Jetzt sind `temperature`, `windspeed` etc. als Dynamic Content verfügbar.

1. Neue Aktion → **Teams-Nachricht senden**
2. Nachricht:
   ```
   ☀️ Wetter in Berlin:
   Temperatur: [temperature] °C
   Wind: [windspeed] km/h
   ```

---

## HTTP POST: Daten senden

Wenn du Daten an eine API schicken willst, nutze POST.

### Konfiguration

| Feld | Wert |
|------|------|
| Methode | `POST` |
| URI | `https://deine-api.example.com/endpoint` |
| Headers | `Content-Type: application/json` |
| Text | JSON-Payload (Dynamic Content möglich) |

**Headers hinzufügen:**
1. Klicke auf **Header hinzufügen**
2. Key: `Content-Type`, Value: `application/json`

**Body:**
```json
{
  "name": "@{triggerInputs()?['text/Name']}",
  "message": "Hallo aus Power Automate!"
}
```

---

## Authentifizierung

Viele APIs erfordern eine Authentifizierung. Im HTTP-Connector:

Klicke auf **Erweiterte Optionen anzeigen**:

| Auth-Typ | Wann |
|----------|------|
| Keine | Öffentliche APIs |
| Einfach | Username/Password (Basic Auth) |
| Client-Anmeldeinformationen | OAuth 2.0 Client Credentials |
| Dienstprinzipal | Azure AD / Entra ID |
| Raw | Beliebiger Auth-Header (z.B. `Bearer TOKEN`) |

!!! tip "API-Schlüssel per Raw"
    Viele einfache APIs nutzen einen API-Key im Header. Wähle **Raw** und gib ein:
    - Header-Name: `Authorization`  
    - Header-Wert: `ApiKey DEIN-KEY` oder `Bearer TOKEN`

---

## Challenge

!!! question "Challenge: Tägliche Wetter-Email"
    Baue einen Scheduled Flow der täglich um 7 Uhr morgens läuft:
    
    1. Rufe die Open-Meteo-API für deinen Standort ab (Koordinaten findest du auf [latlong.net](https://www.latlong.net/))
    2. Parse die JSON-Antwort
    3. Sende eine Email mit der aktuellen Temperatur und Windgeschwindigkeit
    4. Füge eine Condition hinzu: Wenn Temperatur < 10 °C → füge einen Hinweis "🧥 Jacke nicht vergessen!" ein

??? success "Hinweis"
    Den Breitengrad (latitude) und Längengrad (longitude) für deinen Ort findest du auf latlong.net. Ersetze die Koordinaten in der URI. Für die Temperatur-Condition: `less(float(body('JSON_analysieren')?['current_weather']?['temperature']), 10)` als Expression.

---

Weiter zu [Modul 17 – Szenario: IT-Helpdesk](modul-17-szenario-helpdesk.md) →
