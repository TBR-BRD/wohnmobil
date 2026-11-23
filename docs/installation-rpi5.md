# Installation auf einem neuen Raspberry Pi 5

Schritt für Schritt vom ausgepackten Pi 5 bis zum fertigen Dashboard im Wohnmobil und zu Hause.

> Stand: Planung, noch nicht an der realen Anlage durchgespielt. Abweichungen bitte hier nachtragen.

**Übersicht**
1. [Einkaufsliste](#1-einkaufsliste)
2. [Bootloader aktualisieren](#2-bootloader-aktualisieren-nvme-boot)
3. [Home Assistant OS auf die SSD schreiben](#3-home-assistant-os-auf-die-ssd-schreiben)
4. [Erster Start](#4-erster-start)
5. [Grundeinstellungen und Add-ons](#5-grundeinstellungen-und-add-ons)
6. [Bluetooth-Stick einrichten](#6-bluetooth-stick-einrichten)
7. [HACS installieren](#7-hacs-installieren)
8. [Geräte einbinden](#8-geräte-einbinden)
9. [Entitäten umbenennen](#9-entitäten-umbenennen)
10. [Paket, Dashboard und Wasserwaage-Karte](#10-paket-dashboard-und-wasserwaage-karte)
11. [Tailscale](#11-tailscale)
12. [Home Assistant zu Hause anbinden](#12-home-assistant-zu-hause-anbinden)
13. [Einbau und Stromversorgung im Wohnmobil](#13-einbau-und-stromversorgung-im-wohnmobil)
14. [Backups](#14-backups)
15. [Abschluss-Check](#15-abschluss-check)

---

## 1. Einkaufsliste

| Teil | Hinweis |
|---|---|
| Raspberry Pi 5 (4 oder 8 GB) | 4 GB reichen |
| Aktiver Kühler | Offizieller Active Cooler; im Sommer wird es im Wohnmobil heiß |
| M.2-Aufsatz + NVMe-SSD | z. B. offizieller Raspberry Pi M.2 HAT+ mit 128–256 GB SSD |
| Gehäuse | passend für Pi 5 mit M.2-Aufsatz und Kühler |
| microSD-Karte (beliebig) | nur einmalig für das Bootloader-Update |
| USB-NVMe-Adapter **oder** zweite Möglichkeit, die SSD am PC zu beschreiben | für das Aufspielen von Home Assistant OS |
| USB-Bluetooth-Stick + kurze USB-Verlängerung (20–50 cm) | Stick mit von Home Assistant empfohlenem Chip |
| LAN-Kabel | Pi → FRITZ!Box 5G |
| 12-V-auf-5-V-Wandler mit **USB-C PD, 5 V / 5 A** | für den Betrieb im Wohnmobil |
| Sicherung + Sicherungshalter (z. B. 3 A) | in der 12-V-Zuleitung |
| Für die Einrichtung zu Hause: offizielles Pi-5-Netzteil (27 W) | bequemer als gleich im Wohnmobil |

## 2. Bootloader aktualisieren (NVMe-Boot)

Damit der Pi 5 von der SSD startet, braucht er einen aktuellen Bootloader mit NVMe in der Startreihenfolge.

1. Auf dem PC den **Raspberry Pi Imager** installieren: https://www.raspberrypi.com/software/
2. Imager starten → *Modell:* Raspberry Pi 5 → *Betriebssystem:* **Misc utility images → Bootloader (Pi 5 family) → NVMe/USB Boot** → *SD-Karte* wählen → Schreiben.
3. SD-Karte in den Pi stecken (SSD-Aufsatz schon montiert), Strom an.
4. Warten, bis die grüne LED gleichmäßig blinkt (bzw. bei angeschlossenem Monitor der Bildschirm grün ist). Das dauert unter einer Minute.
5. Strom aus, **SD-Karte entfernen**.

## 3. Home Assistant OS auf die SSD schreiben

**Weg A – SSD am PC (empfohlen):**
1. SSD in den USB-NVMe-Adapter, an den PC anschließen.
2. Raspberry Pi Imager → *Modell:* Raspberry Pi 5 → *Betriebssystem:* **Other specific-purpose OS → Home assistants and home automation → Home Assistant → Home Assistant OS (RPi 5)** → *Speicher:* die SSD → Schreiben.
3. Die Frage nach „Einstellungen anpassen“ mit **Nein** beantworten (wird von Home Assistant OS nicht genutzt).
4. SSD in den M.2-Aufsatz des Pi einbauen.

**Weg B – ohne USB-NVMe-Adapter:**
1. Mit dem Imager **Raspberry Pi OS** auf eine SD-Karte schreiben, Pi damit starten.
2. Auf dem Pi den Imager öffnen (*Zubehör → Imager*) und wie in Weg A Home Assistant OS auf die eingebaute SSD schreiben.
3. Pi aus, SD-Karte entfernen.

## 4. Erster Start

1. Pi per **LAN-Kabel** an einen Router hängen (für die Einrichtung zu Hause geht der Heim-Router; endgültig die FRITZ!Box 5G im Wohnmobil). Bluetooth-Stick noch nicht nötig.
2. Strom an. Der erste Start lädt Home Assistant herunter und dauert **10–20 Minuten**.
3. Im Browser öffnen: `http://homeassistant.local:8123`
   Klappt das nicht: IP-Adresse des Pi in der FRITZ!Box nachsehen (*Heimnetz → Netzwerk*) und `http://<IP>:8123` öffnen.
4. Einrichtungsassistent:
   - **„Neues Smart Home erstellen“** (nicht aus Backup wiederherstellen)
   - Benutzer anlegen (sicheres Passwort, kommt später über Tailscale nach außen)
   - Standort: Heimatort oder Stellplatz; Zeitzone **Europe/Berlin**, Sprache **Deutsch**, Einheiten **metrisch**, Währung **EUR**
   - Name des Smart Homes: **Wohnmobil** (hilft später, die beiden Instanzen auseinanderzuhalten)
5. Gefundene Geräte kann der Assistent anbieten: erst einmal überspringen, das kommt in Schritt 8.

**Wenn der Pi ins Wohnmobil umzieht:** Er bekommt dort von der FRITZ!Box 5G eine neue IP-Adresse. Alles Weitere läuft über `homeassistant.local` bzw. Tailscale und funktioniert weiter.

**Feste IP in der FRITZ!Box 5G:** *Heimnetz → Netzwerk → Pi bearbeiten (Stift) → „Diesem Netzwerkgerät immer die gleiche IPv4-Adresse zuweisen“*.

## 5. Grundeinstellungen und Add-ons

1. **Erweiterter Modus:** Unten links auf den Benutzernamen → *Erweiterter Modus* einschalten.
2. **Updates:** *Einstellungen → System → Updates*: alles aktualisieren (Core, Supervisor, Betriebssystem).
3. **Add-ons** installieren unter *Einstellungen → Add-ons → Add-on Store*:

| Add-on | Wozu | Einstellungen |
|---|---|---|
| **File editor** | `configuration.yaml` bearbeiten | *In Seitenleiste anzeigen* an |
| **Terminal & SSH** | Dateien herunterladen, HACS installieren | *In Seitenleiste anzeigen* an |
| **Tailscale** | Zugriff von zu Hause und unterwegs | kommt in Schritt 11 |

Jedes Add-on: *Installieren → Starten*, bei *Beim Booten starten* den Haken setzen.

## 6. Bluetooth-Stick einrichten

1. Pi herunterfahren (*Einstellungen → System → ⏻ → Herunterfahren*), Strom aus.
2. Bluetooth-Stick über die **USB-Verlängerung** an einen **USB-2-Anschluss** (schwarz) stecken. Den Stick weg vom Gehäuse und von der SSD führen.
3. Pi starten.
4. *Einstellungen → Geräte & Dienste*: Unter **Bluetooth** erscheinen jetzt zwei Adapter, das eingebaute Bluetooth des Pi und der USB-Stick.
5. Den Eintrag des **eingebauten** Adapters öffnen → ⋮ → **Deaktivieren**. So läuft alles über den zuverlässigeren Stick. Welcher welcher ist, steht im Eintrag (Hersteller bzw. „Raspberry Pi“ / Cypress).
6. Kontrolle: Beim USB-Stick *Konfigurieren* → **Werbungsmonitor** (Advertisement monitor). Nach kurzer Zeit tauchen dort Geräte in der Nähe auf.

## 7. HACS installieren

HACS wird für die Berger-Batterie (BMS_BLE-HA) und später für Remote Home-Assistant gebraucht.

1. **Terminal** in der Seitenleiste öffnen und eingeben:
   ```bash
   wget -O - https://get.hacs.xyz | bash -
   ```
2. Home Assistant neu starten (*Einstellungen → System → ⏻ → Home Assistant neu starten*).
3. *Einstellungen → Geräte & Dienste → Integration hinzufügen → HACS*, die Hinweise bestätigen.
4. Den angezeigten Code auf https://github.com/login/device eingeben und HACS mit dem GitHub-Konto verbinden.
5. HACS erscheint in der Seitenleiste.

Anleitung von HACS: https://hacs.xyz/docs/use/

## 8. Geräte einbinden

Für die Bluetooth-Geräte gilt: Sie müssen in Reichweite des Pi sein. Für die Einrichtung zu Hause also am besten im Wohnmobil bzw. gleich die Geräte mitnehmen.

### 8.1 Shelly BLU Door/Window ZB und BLU Motion ZB
1. Beim Sensor einmal den Knopf drücken, damit er sofort sendet.
2. *Einstellungen → Geräte & Dienste*: Unter **Entdeckt** erscheint **BTHome** → *Konfigurieren*.
3. Fragt Home Assistant nach einem **Bindkey**, ist die Verschlüsselung aktiv: Den Schlüssel in der Shelly-App beim Gerät nachsehen (*Einstellungen → Verschlüsselung*) und eintragen.
4. Gerät einem Bereich zuordnen, z. B. **Wohnmobil**.

### 8.2 Mopeka Pro
1. Sensor ist montiert (mittig, sauber unter der Flasche). Kurz am Sensor den Knopf drücken.
2. Unter **Entdeckt** erscheint **Mopeka** → *Konfigurieren*.
3. Falls nach dem Flaschen- bzw. Medientyp gefragt wird: **Propan** bzw. die passende Flasche wählen.
4. Die Umrechnung in % macht das Paket (Schritt 10); die Kalibrierung steht in [dashboard.md](dashboard.md#gas-kalibrieren).

### 8.3 Victron: SmartSolar MPPT, Smart Battery Sense, Phoenix mit Dongle
Für jedes der drei Geräte in der **VictronConnect**-App am Handy:
1. Mit dem Gerät verbinden. Firmware-Updates einspielen, die die App anbietet (beim Phoenix auch die des **Dongles**).
2. ⚙️ → ⋮ → **Produktinfo** → **Instant Readout** einschalten.
3. **Verschlüsselungsdaten anzeigen** → den **Schlüssel** kopieren (z. B. per Mail an dich selbst oder in einen Passwort-Manager).
4. Verbindung trennen.

In Home Assistant:
5. *Integration hinzufügen → **Victron BLE*** (bzw. unter **Entdeckt**). Gerät auswählen, den Schlüssel eintragen.
6. Für alle drei Geräte wiederholen.

Fehlt die Integration *Victron BLE* in deiner Version, gibt es sie über HACS. Den Wechselrichter danach prüfen: Sind Zustand, AC-Leistung und Warnungen vorhanden? Ergebnis im [Integrationsplan](bluetooth-integration.md#offene-punkte) eintragen.

### 8.4 Berger LiFePO4 Batterie
1. Am Handy die App **nRF Connect** öffnen → *Scan* → den Eintrag der Batterie finden (Signalstärke steigt, wenn man näher rangeht). **Namen notieren**, z. B. `xiaoxiang…`, `JK-…`, `DL-…`.
2. Die Berger-App am Handy **schließen** (die Batterie erlaubt meist nur eine Verbindung).
3. HACS → **BMS_BLE-HA** suchen (https://github.com/patman15/BMS_BLE-HA) → *Herunterladen* → Home Assistant neu starten.
4. Die Batterie erscheint unter **Entdeckt** als BMS-Gerät → *Konfigurieren*. Wird sie nicht erkannt: Mit dem notierten Namen in der Liste der unterstützten BMS nachsehen und hier eintragen.

### 8.5 Shelly H&T Gen3 (WLAN)
1. In der **Shelly-App** den H&T ins **WLAN der FRITZ!Box 5G** einbinden (bei der Einrichtung zu Hause: ins Heim-WLAN, später in der Shelly-App auf das Wohnmobil-WLAN umstellen).
2. Den Knopf am H&T kurz drücken, damit er aufwacht.
3. In Home Assistant erscheint unter **Entdeckt** **Shelly** → *Konfigurieren*. Wenn nicht: *Integration hinzufügen → Shelly* → IP-Adresse des H&T aus der FRITZ!Box eintragen und dabei den Knopf drücken.
4. Batteriebetriebene Shellys melden sich über eine ausgehende Verbindung bei Home Assistant. Home Assistant richtet das beim Hinzufügen selbst ein. Wenn trotzdem keine Werte kommen: In der Weboberfläche des H&T unter *Einstellungen → Ausgehender WebSocket* die Adresse `ws://<IP-des-Pi>:8123/api/shelly/ws` prüfen.

### 8.6 Digitale Wasserwaage (Xparkle RVS01)
Noch nicht fertig, das Protokoll wird im eigenen Projekt ausgelesen: https://github.com/TBR-BRD/digitale-wasserwaage-wohnmobil-homeassistant/blob/main/docs/xparkle-integration.md
Die Schritte dort gelten für den Pi 5 genauso (dort steht noch Pi 4).

### 8.7 Shelly BLU Gateway
Nicht in Home Assistant einbinden. Es bleibt erst einmal an der FRITZ!Box und schickt weiter an die Shelly-Cloud. Nach ein paar Tagen ohne Lücken in Home Assistant kann es weg.

## 9. Entitäten umbenennen

Damit Dashboard und Paket die Werte finden, bekommen die Entitäten feste IDs. Die Tabelle steht in [entitaeten.md](entitaeten.md).

Für jede Zeile:
1. *Einstellungen → Geräte & Dienste → Entitäten* → Entität suchen und anklicken → ⚙️.
2. **Entitäts-ID** auf den Wert aus der Tabelle ändern, z. B. `sensor.wm_batterie_soc` → *Aktualisieren*.
3. Die Frage, ob auch verknüpfte IDs angepasst werden sollen, kann man bestätigen.

Liefert eine Integration einen Wert nicht, die Zeile einfach auslassen. Die Karte zeigt dann „Entität nicht verfügbar“ und kann im Dashboard entfernt werden.

## 10. Paket, Dashboard und Wasserwaage-Karte

1. **Terminal** öffnen und die Dateien direkt aus GitHub holen:
   ```bash
   mkdir -p /config/packages /config/dashboards /config/www
   curl -fsSL -o /config/packages/wohnmobil.yaml \
     https://raw.githubusercontent.com/TBR-BRD/wohnmobil/main/homeassistant/packages/wohnmobil.yaml
   curl -fsSL -o /config/dashboards/wohnmobil.yaml \
     https://raw.githubusercontent.com/TBR-BRD/wohnmobil/main/homeassistant/dashboards/wohnmobil.yaml
   curl -fsSL -o /config/www/womo-level-card.js \
     https://raw.githubusercontent.com/TBR-BRD/digitale-wasserwaage-wohnmobil-homeassistant/main/womo-level-card.js
   ls -l /config/packages /config/dashboards /config/www
   ```
   Für spätere Updates dieselben Befehle noch einmal ausführen.

2. **File editor** öffnen → `configuration.yaml` → am Ende ergänzen (falls `homeassistant:` oder `lovelace:` schon vorhanden sind, die Zeilen dort einfügen statt doppelt anlegen):
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
   Speichern.

3. **Konfiguration prüfen:** *Entwicklerwerkzeuge → YAML → Konfiguration prüfen*. Erst bei „Konfiguration in Ordnung“ weiter.

4. **Neu starten:** *Einstellungen → System → ⏻ → Home Assistant neu starten*.

5. **Wasserwaage-Karte als Ressource:** *Einstellungen → Dashboards → ⋮ (oben rechts) → Ressourcen → Ressource hinzufügen*
   - URL: `/local/womo-level-card.js`
   - Typ: **JavaScript-Modul**
   Danach die Browserseite neu laden (bei der Handy-App: App neu starten).

6. **Meldungen ans Handy:** Die Home-Assistant-App am Handy installieren und mit dem Pi verbinden. Dann im File editor in `packages/wohnmobil.yaml` beim Skript `wm_melden` `notify.notify` durch `notify.mobile_app_<name-deines-handys>` ersetzen (den genauen Namen zeigt *Entwicklerwerkzeuge → Aktionen*, Suche nach „mobile_app“). *Entwicklerwerkzeuge → YAML → Skripte neu laden*.

7. **Einstellungen im Dashboard:** In der Ansicht **Geräte** die Gas-Kalibrierung und die Warnschwellen setzen.

## 11. Tailscale

1. *Einstellungen → Add-ons → Tailscale* → *Installieren → Starten*, *Beim Booten starten* an.
2. *Weboberfläche öffnen* → mit deinem Tailscale-Konto anmelden → Gerät hinzufügen.
3. In der Tailscale-Verwaltung (https://login.tailscale.com/admin/machines):
   - Das Gerät sinnvoll benennen, z. B. **wohnmobil-ha**.
   - Beim Gerät ⋯ → **Disable key expiry**. Sonst fällt die Verbindung nach einigen Monaten weg, während das Wohnmobil unterwegs ist.
4. Test vom Handy oder Laptop mit aktivem Tailscale: `http://wohnmobil-ha:8123` (MagicDNS) bzw. `http://<Tailscale-IP>:8123`.
5. In der Home-Assistant-App am Handy: Bei *Einstellungen → Begleit-App → Server* als **externe URL** die Tailscale-Adresse eintragen. Dann klappt die App auch, wenn du nicht im Wohnmobil-WLAN bist.

## 12. Home Assistant zu Hause anbinden

**Am Pi im Wohnmobil:**
1. HACS → **Remote Home-Assistant** (https://github.com/custom-components/remote_homeassistant) herunterladen und neu starten. Die Integration muss laut Projekt auf **beiden** Seiten installiert sein; die README dort genau befolgen, was die Gegenstelle braucht.
2. Profil (unten links) → *Sicherheit* → **Langlebige Zugriffstoken → Token erstellen**, Name z. B. „Zuhause“. Den Token sofort kopieren, er wird nur einmal angezeigt.

**Am Home Assistant zu Hause:**
3. Tailscale muss dort laufen (Add-on) und im selben Tailnet sein.
4. HACS → **Remote Home-Assistant** herunterladen → neu starten.
5. *Integration hinzufügen → Remote Home-Assistant*:
   - Host: `wohnmobil-ha` (bzw. Tailscale-IP), Port `8123`, kein SSL
   - Zugriffstoken: der Token aus Schritt 2
   - **Kein Präfix** setzen
   - Optional bei den Filtern nur Entitäten mit `wm_` und `womo_level` übernehmen
6. Dashboard und Wasserwaage-Karte zu Hause wie in [Schritt 10](#10-paket-dashboard-und-wasserwaage-karte) einrichten, aber **nur** `dashboards/wohnmobil.yaml` und `www/womo-level-card.js` holen, **nicht** das Paket. Die berechneten Sensoren und Automatisierungen kommen fertig vom Pi.

Ist das Wohnmobil offline, zeigt das Dashboard zu Hause die Werte als „nicht verfügbar“. Sobald die Verbindung wieder steht, verbindet sich Remote Home-Assistant von selbst neu.

## 13. Einbau und Stromversorgung im Wohnmobil

1. **Wandler anschließen:** 12-V-auf-5-V-Wandler (USB-C PD, 5 V / 5 A) an die Bordbatterie bzw. den 12-V-Verteiler, mit **Sicherung** in der Plus-Leitung möglichst nah an der Abzweigung.
   - Nicht über den Zigarettenanzünder/USB-Buchse am Armaturenbrett, die ist oft nur bei Zündung an.
   - Ein Wandler ohne PD liefert dem Pi weniger Strom. Er warnt dann und begrenzt die USB-Anschlüsse auf 600 mA. Für den Bluetooth-Stick reicht das, ein PD-Wandler ist trotzdem besser.
2. **Ort:** möglichst mittig im Fahrzeug, nicht im Metallkasten und nicht direkt neben dem Wechselrichter. Gute Belüftung, der Pi wird im Sommer warm.
3. **Festmachen:** Gehäuse verschrauben oder mit Klett fixieren, Kabel mit Zugentlastung. Erschütterungen beim Fahren.
4. **LAN** zur FRITZ!Box 5G, Bluetooth-Stick an der Verlängerung frei hängend bzw. mit Klett an der Wand.
5. **Verbrauch beobachten:** Ein Pi 5 braucht ca. 3–5 W, grob **6–10 Ah pro Tag**. Im Dashboard unter **Energie** den Ladestand im Winter im Blick behalten.
6. **Reichweite prüfen:** Ein paar Tage laufen lassen. Im Verlauf jeder Entität sieht man Lücken. Hat ein Gerät Aussetzer (typisch: Mopeka im Gaskasten), das Shelly BLU Gateway in seiner Nähe einstecken und in der Shelly-Integration als Bluetooth-Scanner (passiv) einbinden.

## 14. Backups

1. *Einstellungen → System → Backups → Automatische Backups einrichten*: täglich, die letzten 7 behalten.
2. Den **Verschlüsselungsschlüssel** der Backups herunterladen und sicher aufbewahren (ohne ihn ist ein Backup wertlos).
3. Speicherort zusätzlich zur SSD: z. B. eine Netzwerkfreigabe zu Hause über Tailscale (*Einstellungen → System → Speicher → Netzwerkspeicher hinzufügen*) oder Home Assistant Cloud.
4. Nach größeren Änderungen ein Backup von Hand erstellen.

## 15. Abschluss-Check

- [ ] Pi startet von der SSD, keine SD-Karte steckt
- [ ] Alle Updates eingespielt
- [ ] Nur der USB-Bluetooth-Stick ist aktiv
- [ ] BTHome: Tür/Fenster und Bewegung reagieren
- [ ] Mopeka: Füllhöhe plausibel, Gas-Kalibrierung gesetzt
- [ ] Victron BLE: MPPT, Battery Sense und Phoenix liefern Werte
- [ ] Berger-Batterie: Ladestand, Spannung, Strom vorhanden
- [ ] Shelly H&T Gen3: Temperatur und Luftfeuchte kommen an
- [ ] Alle Entitäten nach [entitaeten.md](entitaeten.md) umbenannt
- [ ] Dashboard „Wohnmobil“ in der Seitenleiste, keine „nicht verfügbar“-Karten (außer Wasserwaage, solange die Integration fehlt)
- [ ] Testmeldung kommt am Handy an (*Entwicklerwerkzeuge → Aktionen → `script.wm_melden`* mit `text: Test`)
- [ ] Tailscale: Zugriff von unterwegs klappt, Key expiry deaktiviert
- [ ] Zu Hause: Remote Home-Assistant verbunden, Dashboard zeigt Werte
- [ ] Automatische Backups laufen, Schlüssel gesichert
- [ ] Im Wohnmobil eingebaut, mit Sicherung, Reichweite ein paar Tage geprüft
