# FSD-TR-001 – Übersetzungsassistent mit AMOLED-Dialog

- Status: Entwurf / Spike-Baseline
- Version: 0.1
- Zugehörige System-FSD: [FSD-001](https://github.com/mcuw/physis-x-system-manager/blob/main/documents/FSD-001-system.md)

## 1. Zweck und Nutzungsszenario

Der Benutzer befindet sich im Ausland und spricht Deutsch. Das Gerät erkennt die Sprecherseite und zeigt die Übersetzung auf dem AMOLED-Display um 180 Grad gedreht zur gegenüberstehenden Person. Antwortet die andere Person, wird deren Sprache erkannt/zugeordnet, die Richtung bestimmt und der übersetzte Text normal ausgerichtet für den Benutzer angezeigt.

Die Sprachauswahl erfolgt am AMOLED. Internetzugang erfolgt über einen Smartphone-Hotspot.

## 2. Systemgrenze und Annahmen

- Zielplattform und konkrete Boardrevision müssen vor Implementierung verbindlich festgelegt werden.
- AMOLED-UI und Sprachauswahl liegen auf dem Embedded-Gerät.
- Der Smartphone-Hotspot stellt den Internet-Uplink bereit.
- STT und Übersetzung können zunächst über externe Dienste erfolgen.
- Audio Direction wird in der ersten Phase durch Test-/Simulationsdaten ersetzt.
- Die Live-Richtungserkennung wird über den Vertrag aus FSD-AUDIO-001 integriert.

## 3. Funktionale Anforderungen

- TR-F-001: Benutzer MUSS Quell- und Zielsprache auf dem AMOLED auswählen können.
- TR-F-002: Die UI MUSS die aktive Sprachkonfiguration sichtbar darstellen.
- TR-F-003: Das System MUSS Audio erfassen und an die konfigurierte Speech-to-Text-Pipeline übergeben können.
- TR-F-004: Erkannter Text MUSS in die ausgewählte Zielsprache übersetzt werden.
- TR-F-005: Übersetzter Text MUSS abhängig von der erkannten Sprecherseite normal oder um 180 Grad gedreht angezeigt werden.
- TR-F-006: Sprecher-/Richtungswechsel MÜSSEN als Zustandswechsel verarbeitet werden.
- TR-F-007: Bei unsicherer Richtung MUSS ein definierter neutraler/mehrdeutiger UI-Zustand möglich sein.
- TR-F-008: Netzwerk-, API-, STT- und Übersetzungsfehler MÜSSEN verständlich angezeigt werden.
- TR-F-009: Bei Hotspot-Unterbrechung MUSS die Anwendung Verbindungsverlust erkennen und einen Wiederverbindungszustand anzeigen.
- TR-F-010: Vertrauliche Zugangsdaten und API-Schlüssel DÜRFEN nicht in Firmware-Logs oder öffentlichen Repositories hinterlegt werden.

## 4. Dialog- und Anzeigeverhalten

Vorgesehene Zustände:
- `idle`: bereit, keine aktive Äußerung;
- `listening_user`: Benutzer spricht;
- `processing_user`: STT/Übersetzung für Benutzeräußerung;
- `show_translation_other`: Übersetzung um 180 Grad für Gegenüber;
- `listening_other`: Gegenüber spricht;
- `processing_other`: STT/Übersetzung für Gegenüber;
- `show_translation_user`: Übersetzung normal für Benutzer;
- `direction_uncertain`: Sprecherseite nicht sicher;
- `network_unavailable`: Hotspot/Uplink nicht verfügbar;
- `service_error`: externer Dienst fehlgeschlagen.

Die konkrete Zustandsmaschine muss verhindern, dass ein alter Übersetzungstext nach einem Sprecherwechsel in falscher Orientierung angezeigt wird. Jede Äußerung erhält eine eindeutige Turn-ID.

## 5. Audio- und Übersetzungspipeline

Logischer Ablauf:
1. Audioaufnahme und Segmentierung/VAD.
2. Sprecher-/Richtungszuordnung.
3. Speech-to-Text.
4. Übersetzung in die konfigurierte Zielsprache.
5. Ergebnisvalidierung und Zuordnung zur Turn-ID.
6. Anzeige mit richtiger Orientierung.

Die Reihenfolge kann abhängig vom gewählten Anbieter optimiert werden. Zu messen sind Ende-zu-Ende-Latenz, Erkennungsqualität, Übersetzungsqualität, Verbindungsstabilität und Kosten.

## 6. Netzwerk und Datenschutz

- WLAN-Verbindung über den Smartphone-Hotspot.
- Reconnect- und Timeout-Verhalten sind zu spezifizieren.
- API-Schlüssel sind außerhalb des Quellcodes sicher zu konfigurieren.
- Zu klären sind Audioübertragung, Speicherung, Aufbewahrung, Anbietertraining und regionale Datenverarbeitung.
- Wo möglich, Audio nur so lange und in dem Umfang übertragen, wie für die Funktion erforderlich.
- Ein Offline-/Fallback-Verhalten ist zu entscheiden; es darf nicht als verfügbar angenommen werden, bevor es implementiert und getestet ist.

## 7. Abnahmetests

| ID | Test | Kriterium |
|---|---|---|
| TR-AT-001 | Language Selection | Quell-/Zielsprache am AMOLED auswählbar und sichtbar |
| TR-AT-002 | Test Audio | Vorgegebene Audioaufnahmen durchlaufen STT und Übersetzung |
| TR-AT-003 | Orientation A | Benutzeräußerung wird mit 180°-Orientierung für Gegenüber angezeigt |
| TR-AT-004 | Orientation B | Antwort des Gegenübers wird normal für Benutzer angezeigt |
| TR-AT-005 | Turn Association | Ergebnisse werden korrekter Äußerung/Turn-ID zugeordnet |
| TR-AT-006 | Direction Uncertain | Unsichere Richtung führt nicht zu falscher sicherer Zuordnung |
| TR-AT-007 | Hotspot Loss | Verbindungsabbruch wird angezeigt und Wiederverbindung behandelt |
| TR-AT-008 | Service Failure | STT-/Übersetzungsfehler führen zu definiertem UI-Zustand |
| TR-AT-009 | Latency | Ende-zu-Ende-Latenz wird mit reproduzierbarem Testset gemessen |
| TR-AT-010 | Privacy | Keine Secrets in Repository/Logs; Datenfluss dokumentiert |

Numerische Latenz- und Qualitätsziele werden nach einem ersten Anbieter-/Hardwarevergleich festgelegt.

## 8. Abhängigkeiten und Integrationsreihenfolge

- TR-1 Übersetzungsdienst-Pipeline kann mit Testaudio beginnen.
- TR-2 AMOLED-Dialog kann simulierte Sprecher-/Richtungsereignisse nutzen.
- Live-Richtungsintegration hängt von FSD-AUDIO-001 und dessen validiertem Ausgabevertrag ab.
- Hardwareentscheidung und konkrete Audioaufnahme müssen vor Produktintegration bestätigt sein.

## 9. Offene Entscheidungen

- Zielboard und genaue AMOLED-/Touch-Treiberbasis.
- STT-/Übersetzungsanbieter und API.
- Streaming oder segmentbasierte Verarbeitung.
- VAD und Turn-Taking-Strategie.
- Sprach-/Autodetektion und Sprecherzuordnung.
- Displaylayout bei langen Texten und RTL-Sprachen.
- Maximale akzeptable Latenz und Offline-Verhalten.
- Datenschutz- und Kostenrahmen.
