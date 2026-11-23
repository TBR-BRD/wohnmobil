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
| Victron Phoenix 12/500 + VE.Direct BT Dongle | Dongle unterstützt kein Instant Readout | VE.Direct per USB-Kabel oder ESP32/ESPHome | Entscheidung offen |
| Berger LiFePO4 120 Ah (BT, Heizung) | BLE, aktive Verbindung zum BMS | HACS `BMS_BLE-HA` (falls BMS unterstützt) | BMS-Typ klären |
| Shelly H&T Gen3 | **WLAN** (kein Bluetooth) | `shelly` (Core) | geplant |
| Shelly BLU Gateway | BLE → WLAN | `shelly` (Core), optional als BT-Proxy | optional |

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
Der VE.Direct-Bluetooth-Smart-Dongle sendet kein Instant Readout. Alternativen:
- **VE.Direct-zu-USB-Kabel** am HA-Host, ausgelesen über eine VE.Direct-Integration (HACS).
- **ESP32 mit ESPHome** am VE.Direct-Port. Damit ist auch Schalten möglich (Ein/Aus/Eco).

In beiden Fällen ist der VE.Direct-Port belegt, der BT-Dongle fällt also weg.

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
- [ ] Wechselrichter: USB-Kabel oder ESP32 entscheiden
- [ ] Prüfen, ob die Verschlüsselung der Shelly-BLU-Geräte aktiv ist
- [ ] Dashboard (Lovelace) entwerfen
