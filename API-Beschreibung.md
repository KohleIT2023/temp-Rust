# RustDesk Server API: Online Peers

Dieses Dokument beschreibt den API-Endpunkt zur Abfrage der aktuell verbundenen Clients (Peers) auf einem selbst gehosteten RustDesk-Server (`hbbs`).

## Endpunkt

```
GET /api/online_peers
```

## Basis-URL

Die Basis-URL hängt von der Adresse und dem konfigurierten API-Port deines `hbbs`-Servers ab. Standardmäßig wurde der API-Port auf **`9000`** gesetzt.

Beispiel: `http://<your-server-ip-or-hostname>:9000`

## Beschreibung

Dieser Endpunkt liefert eine Liste aller RustDesk-Clients, die sich innerhalb des konfigurierten Timeout-Zeitraums (standardmäßig 30 Sekunden) aktiv beim `hbbs`-Server (Signal-/Rendezvous-Server) registriert haben oder mit ihm kommuniziert haben. Er kann für Monitoring, Dashboards oder eigene Verwaltungs-Tools verwendet werden.

## Authentifizierung

Derzeit ist für diesen Endpunkt **keine** Authentifizierung erforderlich. Jeder, der den Server unter der API-URL erreichen kann, kann die Liste der Online-Peers abfragen.
*(Hinweis: Für Produktionsumgebungen sollte erwogen werden, einen Reverse Proxy mit Authentifizierung oder eine Firewall-Regel zum Schutz dieses Endpunkts hinzuzufügen.)*

## Request

*   **Methode:** `GET`
*   **Query Parameter:** Keine
*   **Request Body:** Keiner

## Response

*   **Erfolgs-Statuscode:** `200 OK`
*   **Content-Type:** `application/json`
*   **Response Body:** Ein JSON-Array von Objekten. Jedes Objekt repräsentiert einen Online-Client.

#### Struktur eines Client-Objekts im Array:

```json
{
  "id": "string",
  "ip": "string",
  "last_seen_ms": number
}
```

*   **`id`** (String): Die eindeutige RustDesk-ID des Clients.
*   **`ip`** (String): Die letzte bekannte IP-Adresse, von der sich der Client beim Server gemeldet hat. Kann IPv4 oder IPv6 sein. IPv4-Adressen können im `::ffff:<ipv4>`-Format erscheinen.
*   **`last_seen_ms`** (Number): Die Zeit in Millisekunden, die seit der letzten erkannten Aktivität (z.B. Registrierung) dieses Clients vergangen ist. Ein kleinerer Wert bedeutet eine kürzere Zeit seit der letzten Aktivität.

#### Beispiel-Response (Erfolg):

```json
[
  {
    "id": "123456789",
    "ip": "::ffff:192.168.1.10",
    "last_seen_ms": 5821
  },
  {
    "id": "987654321",
    "ip": "2001:db8::1",
    "last_seen_ms": 15302
  },
  {
    "id": "112233445",
    "ip": "::ffff:88.77.66.55",
    "last_seen_ms": 210
  }
]
```

#### Beispiel-Response (Keine Clients online):

```json
[]
```

## Beispielaufruf (curl)

```bash
curl http://<your-server-ip-or-hostname>:9000/api/online_peers
```

## Mögliche Fehler

*   **Verbindungsfehler (`Connection refused`, Timeout):** Der `hbbs`-Server läuft nicht, lauscht nicht auf dem erwarteten API-Port (9000), oder eine Firewall blockiert die Verbindung.
*   **`Empty reply from server`:** Der Server hat die Verbindung unerwartet geschlossen, bevor eine vollständige HTTP-Antwort gesendet wurde. Dies könnte auf einen internen Serverfehler hindeuten (Logs prüfen).
*   **Andere HTTP-Fehlercodes (z.B. `500 Internal Server Error`):** Ein unerwarteter Fehler ist im API-Handler aufgetreten (Server-Logs prüfen). 