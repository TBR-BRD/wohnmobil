# Entitäten

Das Dashboard und das Paket arbeiten mit festen Entitäts-IDs mit dem Präfix `wm_`. Die Integrationen vergeben eigene IDs. Damit alles ohne Anpassung läuft, benennst du die Entitäten nach dem Einrichten um:

*Einstellungen → Geräte & Dienste → Entitäten → Entität anklicken → ⚙️ → Entitäts-ID*

Ausnahme ist die Wasserwaage: Ihre IDs kommen aus dem Projekt [digitale-wasserwaage-wohnmobil-homeassistant](https://github.com/TBR-BRD/digitale-wasserwaage-wohnmobil-homeassistant) und bleiben so.

Mit ✱ markierte Entitäten erzeugt das Paket [`homeassistant/packages/wohnmobil.yaml`](../homeassistant/packages/wohnmobil.yaml) selbst.

## Energie

| Entitäts-ID | Quelle | Einheit |
|---|---|---|
| `sensor.wm_batterie_soc` | Berger-Batterie (BMS_BLE-HA) – Ladestand | % |
| `sensor.wm_batterie_spannung` | Berger-Batterie – Spannung | V |
| `sensor.wm_batterie_strom` | Berger-Batterie – Strom (+ laden / − entladen) | A |
| `sensor.wm_batterie_leistung` | Berger-Batterie – Leistung | W |
| `sensor.wm_batterie_temperatur` | Berger-Batterie – Temperatur | °C |
| `sensor.wm_battery_sense_spannung` | Victron Smart Battery Sense – Spannung | V |
| `sensor.wm_battery_sense_temperatur` | Victron Smart Battery Sense – Temperatur | °C |
| `sensor.wm_solar_leistung` | Victron MPPT – PV-Leistung | W |
| `sensor.wm_solar_ladestrom` | Victron MPPT – Ladestrom | A |
| `sensor.wm_solar_ertrag_heute` | Victron MPPT – Ertrag heute | kWh |
| `sensor.wm_solar_ladephase` | Victron MPPT – Ladezustand (Bulk/Absorption/Float …) | – |
| `sensor.wm_wechselrichter_zustand` | Victron Phoenix – Gerätezustand | – |
| `sensor.wm_wechselrichter_ac_leistung` | Victron Phoenix – AC-Scheinleistung | VA |
| `sensor.wm_wechselrichter_ac_spannung` | Victron Phoenix – AC-Spannung | V |
| `sensor.wm_wechselrichter_alarm` | Victron Phoenix – Alarm/Warnung | – |

## Gas

| Entitäts-ID | Quelle | Einheit |
|---|---|---|
| `sensor.wm_gas_hoehe` | Mopeka Pro – Füllhöhe | mm |
| `sensor.wm_gas_temperatur` | Mopeka Pro – Temperatur | °C |
| `sensor.wm_gas_messqualitaet` | Mopeka Pro – Messqualität | – |
| `sensor.wm_gas_sensor_batterie` | Mopeka Pro – Batterie | % oder V |
| `sensor.wm_gas_fuellstand` ✱ | berechnet aus Füllhöhe und Kalibrierung | % |

## Klima und Sicherheit

| Entitäts-ID | Quelle | Einheit |
|---|---|---|
| `sensor.wm_innen_temperatur` | Shelly H&T Gen3 – Temperatur | °C |
| `sensor.wm_innen_luftfeuchte` | Shelly H&T Gen3 – Luftfeuchte | % |
| `sensor.wm_ht_batterie` | Shelly H&T Gen3 – Batterie | % |
| `binary_sensor.wm_tuer` | Shelly BLU Door/Window – Fenster/Tür | auf/zu |
| `sensor.wm_tuer_batterie` | Shelly BLU Door/Window – Batterie | % |
| `binary_sensor.wm_bewegung` | Shelly BLU Motion – Bewegung | ja/nein |
| `sensor.wm_bewegung_batterie` | Shelly BLU Motion – Batterie | % |

## Wasserwaage

| Entitäts-ID | Quelle |
|---|---|
| `sensor.womo_level_neigung_laengs` | Xparkle RVS01 – Längsneigung (+ = Front höher) |
| `sensor.womo_level_neigung_quer` | Xparkle RVS01 – Querneigung (+ = rechts höher) |
| `switch.womo_level_nivellieren` | Schalter „Nivellieren“ (geplant in der Xparkle-Integration; ID ggf. anpassen) |

## Vom Paket erzeugt ✱

| Entitäts-ID | Zweck |
|---|---|
| `input_boolean.wm_alarm` | Alarm scharf: meldet Tür auf oder Bewegung |
| `input_number.wm_gas_hoehe_leer` | Kalibrierung: Füllhöhe bei leerer Flasche (mm) |
| `input_number.wm_gas_hoehe_voll` | Kalibrierung: Füllhöhe bei voller Flasche (mm) |
| `input_number.wm_gas_warnschwelle` | Warnung unter diesem Gas-Füllstand (%) |
| `input_number.wm_batterie_warnschwelle` | Warnung unter diesem Batterie-Ladestand (%) |
| `input_number.wm_frost_schwelle` | Frostwarnung unter dieser Innentemperatur (°C) |
| `sensor.wm_gas_fuellstand` | Gas-Füllstand in % |
| `sensor.wm_verbrauch` | Verbrauch im Bordnetz = Solar − Batterieleistung (W) |
| `binary_sensor.wm_gas_niedrig` | Gas unter Warnschwelle |
| `binary_sensor.wm_batterie_niedrig` | Batterie unter Warnschwelle |
| `binary_sensor.wm_frostgefahr` | Innen unter Frostschwelle |
| `binary_sensor.wm_sensor_batterie_niedrig` | Mindestens ein Sensor unter 20 % Batterie |
| `script.wm_melden` | Sendet eine Meldung (lokal + Handy) |
