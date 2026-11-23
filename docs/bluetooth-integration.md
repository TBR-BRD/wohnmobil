# Bluetooth-Integrationsplan

Ziel: Alle Geräte im Wohnmobil liefern ihre Daten an Home Assistant, möglichst über den Bluetooth-USB-Dongle am HA-Host.

> Status: Planung. Noch nicht an der realen Anlage getestet.

## Geräteübersicht

| Gerät | Funk / Protokoll | HA-Integration | Status |
|---|---|---|---|
| Shelly BLU Door/Window ZB | BLE (BTHome v2), alternativ Zigbee | `bthome` (Core) | geplant |
| Shelly BLU Motion ZB | BLE (BTHome v2), alternativ Zigbee | `bthome` (Core) | geplant |
| Mopeka Pro (Gasflasche) | BLE-Advertisement | `mopeka` (Core) | geplant |
| Victron SmartSolar MPPT 100/20 | BLE Instant Readout (verschlüsselt) | Victron BLE (Core bzw. HACS) | geplant |
| Victron Smart Battery Sense 12 V | BLE Instant Readout (verschlüsselt) | Victron BLE (Core bzw. HACS) | geplant |
| Victron Phoenix 12/500 + VE.Direct BT Smart Dongle | BLE Instant Readout über den Dongle (verschlüsselt) | Victron BLE (Unterstützung für Wechselrichter prüfen); Fallback VE.Direct per ESP32 | geplant |
| Berger LiFePO4 120 Ah (BT, Heizung) | BLE, aktive Verbindung zum BMS | HACS `BMS_BLE-HA` (falls BMS unterstützt) | BMS-Typ klären |
| Shelly H&T Gen3 | **WLAN** (kein Bluetooth) | `shelly` (Core) | geplant |
| Shelly BLU Gateway | BLE → WLAN | `shelly` (Core), optional als BT-Proxy | optional |

## Produktlinks

| Gerät | Link |
|---|---|
| Shelly BLU Gateway | https://www.shelly.com/de/products/shelly-blu-gateway |
| Berger LiFePO4 Lithium Batterie Pro 120 Ah 12 V mit Bluetooth & Heizung | https://www.fritz-berger.de/artikel/berger-lifepo4-lithium-batterie-120-ah-12-v-mit-bluetooth-mit-und-ohne-heizung-400265?varid=441139 |
| Victron SmartSolar MPPT 100/20 | https://www.victronenergy.de/solar-charge-controllers/smartsolar-mppt-75-10-75-15-100-15-100-20 |
| Victron Smart Battery Sense 12 V | https://www.victronenergy.de/meters-and-sensors/smart-battery-sense |
| Victron Phoenix Inverter VE.Direct 12/500 230 V | https://www.victronenergy.de/inverters/phoenix-inverter-vedirect-250va-800va |
| Victron VE.Direct Bluetooth Smart Dongle | https://www.victronenergy.de/communication-centres/ve-direct-bluetooth-smart-dongle |
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

## Einrichtung

### 1. Bluetooth-Dongle
- Per kurzer USB-Verlängerung anschließen: nicht direkt neben USB-3-Ports oder einer SSD (Funkstörungen im 2,4-GHz-Band).
- Einen von Home Assistant empfohlenen Adapter verwenden (z. B. mit Realtek- oder CSR-Chip).
- Prüfen: *Einstellungen → Geräte & Dienste → Bluetooth*.

### 2. Shelly BLU Sensoren und Mopeka
- Sollten automatisch als „Entdeckt“ erscheinen.
- Wenn in der Shelly-App die Verschlüsselung aktiv ist, verlangt BTHome den Bindkey (in der Shelly-App einsehbar).
- Mopeka: Flaschentyp bzw. -größe einstellen (z. B. 11 kg).

### 3. Victron MPPT und Smart Battery Sense
1. VictronConnect → Gerät → ⚙️ → *Produktinfo* → *Instant Readout* einschalten.
2. Verschlüsselungsschlüssel anzeigen und kopieren.
3. In HA die Victron-BLE-Integration hinzufügen und den Schlüssel pro Gerät eintragen.

Die VictronConnect-App funktioniert danach weiter.

### 4. Victron Phoenix Wechselrichter
Laut Victron unterstützt der VE.Direct Bluetooth Smart Dongle Instant Readout am Phoenix Inverter VE.Direct.
1. Dongle-Firmware in VictronConnect aktualisieren.
2. Instant Readout für den Dongle einschalten und den Schlüssel kopieren (wie bei Schritt 3).
3. In der Victron-BLE-Integration eintragen.

Noch zu prüfen: Zeigt Victron BLE den Wechselrichter vollständig an (AC-Leistung, Zustand, Warnungen)?

Fallback, falls nicht oder falls Schalten (Ein/Aus/Eco) gewünscht ist: **ESP32 mit ESPHome** am VE.Direct-Port. Dann ist der Port belegt und der Dongle fällt weg.

### 5. Berger LiFePO4 Batterie
1. Mit *nRF Connect* (Smartphone) den BLE-Namen der Batterie ermitteln (z. B. `xiaoxiang…`, `JK-…`, `DL-…`), daraus folgt der BMS-Typ.
2. Gegen die Liste der unterstützten BMS von `BMS_BLE-HA` abgleichen.
3. Hinweis: Solange HA verbunden ist, kann die Hersteller-App meist nicht gleichzeitig verbinden.

Die Batterie liefert den einzigen echten SoC-Wert. Der Smart Battery Sense misst nur Spannung und Temperatur.

### 6. Shelly H&T Gen3
Ins WLAN des Wohnmobils einbinden und über die Shelly-Integration hinzufügen.

## Reichweite
Die Wände eines Wohnmobils können Bluetooth abschirmen. Bei Aussetzern einen zusätzlichen Empfänger einplanen:
- Shelly BLU Gateway als Bluetooth-Scanner in der Shelly-Integration, oder
- ESP32 mit ESPHome als Bluetooth-Proxy.

## Offene Punkte
- [ ] HA-Hardware und Dongle-Modell dokumentieren
- [ ] BLE-Name bzw. BMS-Typ der Berger-Batterie ermitteln
- [ ] Wechselrichter: Instant Readout über den Dongle in HA testen
- [ ] Prüfen, ob die Verschlüsselung der Shelly-BLU-Geräte aktiv ist
- [ ] Dashboard (Lovelace) entwerfen
