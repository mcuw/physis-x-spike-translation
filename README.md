# Physis-X Spike: Translation Assistant

## Zweck und Use Case

Dieses Repository untersucht den Übersetzungsassistenten auf dem ESP32-C6 Touch-AMOLED-Board. Der Nutzer wählt die Sprachen direkt am AMOLED aus. Das Gerät nutzt den Smartphone-Hotspot als Internetzugang für Speech-to-Text und Übersetzung.

Gesprächsablauf:
1. Nutzer spricht auf Deutsch (oder einer ausgewählten Quellsprache).
2. Die Audio Direction-Komponente ermittelt die Sprecherseite.
3. Sprache wird erkannt/transkribiert und übersetzt.
4. Der übersetzte Text erscheint auf dem AMOLED zum Gegenüber um 180° gedreht.
5. Die andere Person antwortet; Richtung und Sprachpaar werden entsprechend umgeschaltet.
6. Die Antwort wird normal ausgerichtet für den Nutzer angezeigt.

Dieser Spike validiert Dienstpipeline und UI-/Dialogablauf. Er ist noch keine vollständige Produkt-FSD.

## Spike-Plan und Issues

| Reihenfolge | Issue | Arbeitspaket | Abnahmekriterien |
|---|---|---|---|
| 1 | [TR-1 – Übersetzungsdienst-Pipeline](https://github.com/mcuw/physis-x-spike-translation/issues/1) | Provider-/API-Optionen, Audio-Upload/Streaming, STT, Übersetzung, Fehler/Retry, Smartphone-Hotspot, Latenz, Kosten und Datenschutz untersuchen. Zunächst mit Testaudio arbeiten. | Testaudio läuft bis zur übersetzten Textantwort durch; Latenz ist über mehrere Beispiele gemessen; Hotspot-Abbruch/Reconnect ist getestet; Secrets sind nicht im Repository; Provider-/Datenschutzrisiken dokumentiert. |
| 2 | [TR-2 – AMOLED-Dialog und Ausrichtung](https://github.com/mcuw/physis-x-spike-translation/issues/2) | Sprachwahl am Display, Sprecher A/B, erkannte Richtung, Übersetzungsstatus, normale/180°-Darstellung, Fehler-/Unbestimmt-Zustände und Touch-Bedienung prototypisieren. | Sprachen sind am AMOLED wählbar; beide Gesprächsrichtungen werden korrekt zugeordnet; Textausrichtung folgt dem Richtungsereignis; Fehler/unklare Richtung ergeben einen verständlichen UI-Zustand; vollständiger Dialog ist demonstrierbar. |

## Arbeitshypothesen

- Smartphone-Hotspot ist der Internet-Uplink; Zugangsdaten werden nicht in Quellcode oder Firmware-Repository eingecheckt.
- Die Übersetzung erfolgt zunächst über einen externen Dienst. Anbieter, API und Audioformat sind offene Entscheidungen.
- Live-Audio und Streaming werden gegen eine zunächst einfachere Datei-/Segment-Pipeline abgewogen.
- Das Display zeigt jeweils die für die Gesprächsrichtung passende Übersetzung; die exakte Turn-Taking-Logik ist zu spezifizieren.

## Abhängigkeiten

- TR-1 kann mit Testaudio unabhängig von der Richtungsbestimmung beginnen.
- TR-2 kann früh mit simulierten Richtungsereignissen prototypisiert werden.
- Die Live-Integration von Audio Direction benötigt [physis-x-spike-audio-direction](https://github.com/mcuw/physis-x-spike-audio-direction), insbesondere Audio-1/Audio-2.
- Der Thread-TBR ist für die lokale Audio-/Cloud-Pipeline nicht grundsätzlich erforderlich; der Smartphone-Hotspot stellt den Internetzugang bereit.

## Risiken und offene Entscheidungen

- End-to-End-Latenz und Gesprächsgefühl bei wechselnden Sprechern.
- Provider-API, Kosten, Quotas, Verfügbarkeit und Datenschutz/Datenresidenz.
- Streaming vs. Segmentverarbeitung und Umgang mit Teiltranskripten.
- Hotspot-Reconnect und Verhalten bei fehlendem Internet.
- LVGL-/Display-Aktualisierung, Lesbarkeit, Textlängen und Rotation.
- Verhalten bei gleichzeitiger Sprache, unklarer Richtung und Übersetzungsfehlern.
- Ob Sprachausgabe (TTS) Teil des Produktumfangs ist oder zunächst nur Text angezeigt wird.

## Ergebnis für die FSD

Prototyp der Dienstpipeline, Latenz-/Fehler-Messwerte, Anbieterentscheidungen und UI-Zustandsmodell. Die FSD soll daraus konkrete Anforderungen an Audioformate, API-Verträge, Turn-Taking, UI-Zustände und Datenschutz ableiten.
