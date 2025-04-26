# Taskliste: Implementierung der Online-Peers-API

Dieses Dokument verfolgt die Schritte zur Implementierung eines HTTP-API-Endpunkts zur Abfrage von Online-Peers im RustDesk-Server (`hbbs`).

## Protokoll

- [x] **Initialisierung:** Anforderungen zusammengefasst und Taskliste erstellt. (Datum: Jetzt)
- [x] **Task 1:** Abhängigkeiten überprüft.
- [x] **Task 2:** `OnlinePeerInfo` Struktur definiert.
- [x] **Task 3:** Handler-Funktion `get_online_peers_handler` erstellt und implementiert.
- [x] **Task 4:** Sichtbarkeit von `pm` und `pm.map` angepasst (`pub(crate)`).
- [x] **Task 5:** `RendezvousServer::start` angepasst (API-Port, Arc, Router, Axum-Server Start, select!).
- [x] **Task 6:** Notwendige Imports hinzugefügt.
- [x] **Task 7:** Code erfolgreich gebaut (`cargo build --release`) nach mehreren Korrekturrunden (State-Import, Mutabilität, Features).

## Aufgaben

- [x] **Task 1: Abhängigkeiten überprüfen:** Sicherstellen, dass `axum`, `tokio`, `serde`, `serde_json` und `tower-http` in `Cargo.toml` vorhanden sind. (Status: Erledigt)
- [x] **Task 2: Datenstruktur definieren:** `struct OnlinePeerInfo` mit `serde::Serialize` in `src/rendezvous_server.rs` erstellen. (Status: Erledigt)
- [x] **Task 3: Handler-Funktion erstellen:** `async fn get_online_peers_handler` in `src/rendezvous_server.rs` implementieren. (Status: Erledigt)
    - [x] State (`Arc<RendezvousServer>`) extrahieren (via `Extension`).
    - [x] `PeerMap` aus dem State holen (`state.pm.map`).
    - [x] Read-Lock auf die Map holen.
    - [x] Über Peers iterieren.
    - [x] Online-Status anhand `last_reg_time` und `REG_TIMEOUT` prüfen.
    - [x] `OnlinePeerInfo` für Online-Peers sammeln.
    - [x] `Json(Vec<OnlinePeerInfo>)` zurückgeben.
- [x] **Task 4: Sichtbarkeit prüfen/anpassen:** Sicherstellen, dass `pm`-Feld in `RendezvousServer` und `map`-Feld in `PeerMap` für den Handler zugänglich ist (`pub(crate)`). (Status: Erledigt)
- [x] **Task 5: `RendezvousServer::start` anpassen:** (Status: Erledigt)
    - [x] API-Port definieren (z.B. `ws_port + 1`).
    - [x] `RendezvousServer`-Instanz (`rs`) in `Arc` wrappen (`server_state`).
    - [x] Geklonten `Arc` an `io_loop` übergeben.
    - [x] Axum-Router erstellen (`Router::new()`).
    - [x] Route `/api/online_peers` mit Handler hinzufügen.
    - [x] State mit `.layer(Extension(server_state.clone()))` an Router binden.
    - [x] Axum-Server binden und starten (`axum::Server::bind(...).serve(...)`).
    - [x] Axum-Server-Future zum Haupt-`tokio::select!` hinzufügen.
- [x] **Task 6: Notwendige Imports hinzufügen:** `use` Statements für `axum`, `serde`, etc. in `src/rendezvous_server.rs` ergänzen. (Status: Erledigt)
- [x] **Task 7: Code bauen:** `cargo build --release` ausführen und Kompilierfehler beheben. (Status: Erledigt)
- [ ] **Task 8: Taskliste aktualisieren:** Alle erledigten Schritte abhaken. (Status: In Arbeit) 