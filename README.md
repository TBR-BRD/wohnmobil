# Wohnmobil – Home Assistant

Alle Geräte im Wohnmobil auf einem lokalen Home Assistant (Raspberry Pi 5) mit Bluetooth-Stick. Über Tailscale erscheinen die Daten zusätzlich im Home Assistant zu Hause.

**Geräte:** Berger LiFePO4 120 Ah · Victron SmartSolar MPPT 100/20 · Victron Smart Battery Sense · Victron Phoenix 12/500 mit VE.Direct Bluetooth Smart Dongle · Mopeka Pro · Shelly H&T Gen3 · Shelly BLU Door/Window ZB · Shelly BLU Motion ZB · Digitale Wasserwaage (Xparkle RVS01)

## Inhalt

| Datei | Zweck |
|---|---|
| [docs/installation-rpi5.md](docs/installation-rpi5.md) | **Installation Schritt für Schritt auf einem neuen Raspberry Pi 5** |
| [docs/bluetooth-integration.md](docs/bluetooth-integration.md) | Architektur, Geräte, Integrationen, Einrichtung, offene Punkte |
| [docs/entitaeten.md](docs/entitaeten.md) | Feste Entitäts-IDs, auf die Dashboard und Paket aufbauen |
| [docs/dashboard.md](docs/dashboard.md) | Bedienoberfläche: Ansichten und Installation |
| [homeassistant/packages/wohnmobil.yaml](homeassistant/packages/wohnmobil.yaml) | Helfer, berechnete Sensoren, Warnungen, Alarm |
| [homeassistant/dashboards/wohnmobil.yaml](homeassistant/dashboards/wohnmobil.yaml) | Dashboard „Wohnmobil“ |

## Verwandte Projekte

- [digitale-wasserwaage-wohnmobil-homeassistant](https://github.com/TBR-BRD/digitale-wasserwaage-wohnmobil-homeassistant): Neigungsanzeige mit Keilhöhen pro Rad
- [wohnmobil-touren-homeassistant](https://github.com/TBR-BRD/wohnmobil-touren-homeassistant): Touren aus GPS-Daten auf der Karte
