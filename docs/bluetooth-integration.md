# Integrationsplan: Wohnmobil → Home Assistant

Ziel: Alle Geräte im Wohnmobil laufen auf einem **lokalen Home Assistant (Raspberry Pi 5)** im Wohnmobil. Dieselben Daten erscheinen zusätzlich im Home Assistant zu Hause auf der Karte „Wohnmobil“.

> Status: Planung. Noch nicht an der realen Anlage getestet.

## Architektur

```
Wohnmobil                                           Zuhause
┌──────────────────────────────────────┐           ┌────────────────────────┐
│ RPi 5 – Home Assistant „Wohnmobil“    │           │ Home Assistant          │
│  ├─ USB-Bluetooth-Stick              │           │  Remote Home-Assistant  │
│  │   → BTHome, Mopeka, Victron BLE,   │ Tailscale │  (HACS)                 │
│  │     Berger (aktive Verbindung)     │◄═════════►│  → Dashboard            │
│  ├─ Shelly H&T Gen3 (WLAN)           │           │    „Wohnmobil“          │
│  └─ LAN-Kabel → FRITZ!Box 5G          │           └────────────────────────┘
└──────────────────────────────────────┘
```

- **Lokal:** Der Pi empfängt alle Bluetooth-Geräte selbst. Verlauf, Automatisierungen und Bedienung funktionieren auch ohne Internet.
- **Nach Hause:** Über das Tailscale-Add-on auf dem Pi. Die Verbindung baut der Pi selbst auf, das klappt also auch hinter dem 5G-Netz. Ein VPN zwischen den FRITZ!Boxen ist nicht nötig.
- **Zu Hause:** Die HACS-Integration *Remote Home-Assistant* holt die Werte über Tailscale. Ohne Präfix behalten die Entitäten ihre IDs, und das Dashboard aus diesem Repo funktioniert auch zu Hause.
- **Shelly BLU Gateway:** Wird durch den Pi ersetzt. Zuerst ein paar Tage parallel laufen lassen, dann abziehen oder als Zusatzempfänger an eine Stelle mit schlechtem Empfang setzen.

## Geräteübersicht

| Gerät | Funk / Protokoll | HA-Integration (lokal am Pi) |
|---|---|---|
| Shelly BLU Door/Window ZB | BLE (BTHome v2), alternativ Zigbee | `bthome` (Core) |
| Shelly BLU Motion ZB | BLE (BTHome v2), alternativ Zigbee | `bthome` (Core) |
| Mopeka Pro (Gasflasche) | BLE-Advertisement | `mopeka` (Core) |
| Victron SmartSolar MPPT 100/20 | BLE Instant Readout (verschlüsselt) | Victron BLE |
| Victron Smart Battery Sense 12 V | BLE Instant Readout (verschlüsselt) | Victron BLE |
| Victron Phoenix 12/500 + VE.Direct BT Smart Dongle | BLE Instant Readout über den Dongle | Victron BLE (Wechselrichter-Unterstützung prüfen) |
| Berger LiFePO4 120 Ah (BT, Heizung) | BLE, aktive Verbindung zum BMS | HACS `BMS_BLE-HA` (BMS-Typ prüfen) |
| Shelly H&T Gen3 | **WLAN** | `shelly` (Core) |
| Shelly BLU Gateway | BLE → WLAN | entfällt, optional Zusatzempfänger |
| Digitale Wasserwaage (Xparkle RVS01) | BLE, Protokoll noch unbekannt | eigene Integration `xparkle_level`, Karte `womo-level-card` – siehe [digitale-wasserwaage-wohnmobil-homeassistant](https://github.com/TBR-BRD/digitale-wasserwaage-wohnmobil-homeassistant) |

## Produktlinks

| Gerät | Link |
|---|---|
| Shelly BLU Gateway | https://www.shelly.com/de/products/shelly-blu-gateway |
| Berger LiFePO4 Lithium Batterie Pro 120 Ah 12 V mit Bluetooth & Heizung | https://www.fritz-berger.de/artikel/berger-lifepo4-lithium-batterie-120-ah-12-v-mit-bluetooth-mit-und-ohne-heizung-400265?varid=441139 |
| Victron SmartSolar MPPT 100/20 | https://www.victronenergy.de/solar-charge-controllers/smartsolar-mppt-75-10-75-15-100-15-100-20 |
| Victron Smart Battery Sense 12 V | https://www.victronenergy.de/meters-and-sensors/smart-battery-sense |
| Victron Phoenix Inverter VE.Direct 12/500 230 V | https://www.victronenergy.de/inverters/phoenix-inverter-vedirect-250va-800va |
| Victron VE.Direct Bluetooth Smart Dongle | https://www.victronenergy.de/communication-centres/ve-direct-bluetooth-smart-dongle |
| Xparkle RVS01 (Wasserwaage) | https://www.hobbydirekt.de/Neuheiten/Neuheiten-2025/Minicars/Xparkle-RVS01-Smart-RV-Nivellierungsnavigator-XPARKLE-XP-990008-01-XP990008-01::410112.html |
| Mopeka Pro Füllstandsmessgerät für Gasflaschen | https://www.fritz-berger.de/artikel/mopeka-pro-fuellstand-messgeraet-fuer-gasflaschen-mit-bluetooth-451114 |
| Shelly H&T Gen3 | https://www.shelly.com/de/products/shelly-h-t-gen3-matte-white |
| Shelly BLU Door/Window ZB | https://www.shelly.com/de/products/shelly-blu-door-window-zb-white |
| Shelly BLU Motion ZB | https://www.shelly.com/de/products/shelly-blu-motion-zb |

### Integrations-Dokumentation

- Bluetooth: https://www.home-assistant.io/integrations/bluetooth/
- BTHome: https://www.home-assistant.io/integrations/bthome/
- Mopeka: https://www.home-assistant.io/integrations/mopeka/
- Shelly: https://www.home-assistant.io/integrations/shelly/
- BMS_BLE-HA (HACS): https://github.com/patman15/BMS_BLE-HA
- Remote Home-Assistant (HACS): https://github.com/custom-components/remote_homeassistant

## Hardware im Wohnmobil

| Punkt | Empfehlung |
|---|---|
| Rechner | Raspberry Pi 5 mit Home Assistant OS |
| Speicher | SSD (z. B. per NVMe-Aufsatz) statt SD-Karte, wegen Erschütterungen und Stromausfällen |
| Stromversorgung | 12-V-auf-5-V-Wandler mit USB-C und **5 V / 5 A**, direkt an der Bordbatterie mit Sicherung |
| Verbrauch | ca. 3–5 W, also grob **6–10 Ah pro Tag** aus der 120-Ah-Batterie |
| Bluetooth | USB-Stick an einer kurzen Verlängerung, weg von USB 3 und der SSD |
| Netzwerk | LAN-Kabel zur FRITZ!Box 5G (das Pi-WLAN teilt sich den Chip mit Bluetooth) |
| Einbauort | möglichst mittig im Fahrzeug, damit alle Sensoren gut erreichbar sind |

## Einrichtung

### 1. Home Assistant auf dem Pi
1. Home Assistant OS auf die SSD schreiben, den Pi per LAN an die FRITZ!Box hängen.
2. Bluetooth-Stick anschließen. Prüfen: *Einstellungen → Geräte & Dienste → Bluetooth*.
3. HACS installieren (für BMS_BLE-HA).

### 2. Shelly BLU Sensoren und Mopeka
- Sollten automatisch als „Entdeckt“ erscheinen.
- Wenn in der Shelly-App die Verschlüsselung aktiv ist, verlangt BTHome den Bindkey (in der Shelly-App einsehbar).
- Mopeka: Füllhöhe kalibrieren (siehe [Dashboard-Doku](dashboard.md)).

### 3. Victron: MPPT, Smart Battery Sense, Phoenix
1. VictronConnect → Gerät → ⚙️ → *Produktinfo* → *Instant Readout* einschalten. Beim Phoenix vorher die Dongle-Firmware aktualisieren.
2. Verschlüsselungsschlüssel anzeigen und kopieren.
3. In HA die Victron-BLE-Integration hinzufügen und den Schlüssel pro Gerät eintragen.

Die VictronConnect-App funktioniert danach weiter. Falls Victron BLE den Wechselrichter nicht vollständig anzeigt oder Schalten (Ein/Aus/Eco) gewünscht ist: ESP32 mit ESPHome am VE.Direct-Port. Dann fällt der Dongle weg.

### 4. Berger LiFePO4 Batterie
1. Mit *nRF Connect* (Smartphone) den BLE-Namen der Batterie ermitteln (z. B. `xiaoxiang…`, `JK-…`, `DL-…`), daraus folgt der BMS-Typ.
2. Gegen die Liste der unterstützten BMS von `BMS_BLE-HA` abgleichen und die Integration einrichten.
3. Hinweis: Solange HA verbunden ist, kann die Hersteller-App meist nicht gleichzeitig verbinden.

Die Batterie liefert den einzigen echten SoC-Wert. Der Smart Battery Sense misst nur Spannung und Temperatur.

### 5. Shelly H&T Gen3
Ins WLAN der FRITZ!Box 5G einbinden und über die Shelly-Integration hinzufügen.

### 6. Tailscale und Zuhause
1. Auf dem Pi das Tailscale-Add-on installieren und ins bestehende Tailnet aufnehmen.
2. Auf dem Pi einen langlebigen Zugangstoken erstellen (*Profil → Sicherheit*).
3. Zu Hause *Remote Home-Assistant* (HACS) einrichten: Tailscale-Adresse des Pi plus Token, **ohne Präfix**. Optional nur `wm_*`-Entitäten übernehmen.
4. Das Dashboard aus diesem Repo zu Hause genauso einbinden.

### 7. Digitale Wasserwaage
Eigenes Projekt: https://github.com/TBR-BRD/digitale-wasserwaage-wohnmobil-homeassistant
- Sensor Xparkle RVS01 über den Bluetooth-Stick des Pi 5 (der dortige Plan nennt noch den Pi 4, das Vorgehen ist gleich).
- Liefert `sensor.womo_level_neigung_laengs` und `sensor.womo_level_neigung_quer` für die Karte `custom:womo-level-card`.
- Die Karte ist im Dashboard als eigene Ansicht „Nivellieren“ eingebunden.

### 8. Bedienoberfläche
Siehe [dashboard.md](dashboard.md).

## Alternative ohne Pi
Das Shelly BLU Gateway bleibt an der FRITZ!Box und arbeitet als passiver Bluetooth-Scanner in der Shelly-Integration des HA zu Hause. Dazu kommen ein WireGuard-VPN zwischen den beiden FRITZ!Boxen (unterschiedliche Netze, z. B. 192.168.179.x im Wohnmobil) und ein ESP32 mit ESPHome als Bluetooth-Proxy für die Berger-Batterie. Nachteil: ohne Internet keine Daten und keine Automatisierungen.

## Offene Punkte
- [ ] Modell des Bluetooth-Sticks dokumentieren
- [ ] BLE-Name bzw. BMS-Typ der Berger-Batterie ermitteln
- [ ] Wechselrichter: Instant Readout über den Dongle in HA testen
- [ ] Prüfen, ob die Verschlüsselung der Shelly-BLU-Geräte aktiv ist
- [ ] Entitäten nach [entitaeten.md](entitaeten.md) umbenennen
- [ ] Wasserwaage: Xparkle-Protokoll auslesen (im eigenen Repo)
- [ ] Shelly BLU Gateway ein paar Tage parallel betreiben, dann entscheiden
