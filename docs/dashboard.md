# Bedienoberfläche „Wohnmobil“

Dashboard für die lokale Bedienung am Raspberry Pi 5 im Wohnmobil, auf dem Handy oder Tablet über das WLAN der FRITZ!Box. Über Remote Home-Assistant läuft dasselbe Dashboard auch zu Hause.

## Ansichten

| Ansicht | Inhalt |
|---|---|
| **Übersicht** | Warnungen (nur wenn aktiv), Batterie- und Gas-Anzeige, Solar, Verbrauch, 230 V, Klima, Tür, Bewegung, Alarm-Schalter, Sprung zur Wasserwaage |
| **Energie** | Berger-Batterie, Smart Battery Sense, Solar mit Verlauf und Tagesertrag, Wechselrichter |
| **Klima** | Temperatur und Luftfeuchte mit 48-h-Verlauf, Frostschwelle, Alarm, Ereignis-Logbuch |
| **Nivellieren** | Schalter „Nivellieren“ und die Karte `womo-level-card` aus dem Wasserwaage-Projekt |
| **Geräte** | Batteriestände der Sensoren, Gas-Kalibrierung, Warnschwellen |

Was du bedienen kannst: Alarm scharf/unscharf, Nivellieren an/aus, Warnschwellen und Gas-Kalibrierung. Die übrigen Geräte sind Sensoren und zeigen nur Werte an. Den Wechselrichter schalten geht erst mit dem ESP32-Fallback am VE.Direct-Port (siehe [Integrationsplan](bluetooth-integration.md)).

## Voraussetzungen

- Home Assistant 2025.10 oder neuer (Sections-Ansichten, Tile-Funktionen, `default_entity_id` bei Templates)
- Entitäten umbenannt nach [entitaeten.md](entitaeten.md)
- Für die Ansicht „Nivellieren“: `womo-level-card.js` aus [digitale-wasserwaage-wohnmobil-homeassistant](https://github.com/TBR-BRD/digitale-wasserwaage-wohnmobil-homeassistant)

## Installation

1. Den Ordner `homeassistant/` dieses Repos nach `/config/` auf dem Pi kopieren, z. B. mit dem Samba- oder File-Editor-Add-on:
   ```
   /config/packages/wohnmobil.yaml
   /config/dashboards/wohnmobil.yaml
   ```
2. In `configuration.yaml` ergänzen:
   ```yaml
   homeassistant:
     packages: !include_dir_named packages

   lovelace:
     dashboards:
       wohnmobil:
         mode: yaml
         title: Wohnmobil
         icon: mdi:rv-truck
         show_in_sidebar: true
         filename: dashboards/wohnmobil.yaml
   ```
3. Wasserwaage-Karte: `womo-level-card.js` nach `/config/www/` kopieren. Dann unter *Einstellungen → Dashboards → ⋮ → Ressourcen* die Ressource `/local/womo-level-card.js` vom Typ **JavaScript-Modul** hinzufügen.
4. *Entwicklerwerkzeuge → YAML → Konfiguration prüfen*, danach Home Assistant neu starten.
5. Die Meldungen gehen an `notify.notify`. Im Skript `wm_melden` ggf. auf das eigene Handy ändern, z. B. `notify.mobile_app_<handy>`.

## Gas kalibrieren

Die Mopeka misst die Füllhöhe in mm. Daraus macht das Paket einen Füllstand in %:

1. Volle Flasche anschließen und in der Ansicht **Geräte** die angezeigte Füllhöhe bei „Füllhöhe voll“ eintragen.
2. Bei leerer Flasche den Wert bei „Füllhöhe leer“ eintragen (meist nahe 0).

Auf eine gute Messqualität achten: Der Sensor muss mittig und sauber unter der Flasche sitzen.

## Zu Hause verwenden

Remote Home-Assistant ohne Präfix einrichten: Dann haben die Entitäten zu Hause dieselben IDs, und Schritt 1–3 laufen dort genauso. Das Paket (`packages/wohnmobil.yaml`) zu Hause **nicht** zusätzlich einbinden. Die berechneten Sensoren und Automatisierungen kommen schon fertig vom Pi.
