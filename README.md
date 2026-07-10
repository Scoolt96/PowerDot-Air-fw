# PowerDot Air

**Luftqualitäts-Monitor mit rundem Touch-Display.**
PowerDot Air misst kontinuierlich CO₂, Feinstaub, Gase (VOC/NOx), Temperatur und
Luftfeuchte mit einem **Sensirion SEN66** und zeigt alles auf einem 1,46″-Rund­display,
in einer eingebauten **Web-Oberfläche** und direkt in **Home Assistant** an.

> ℹ️ Dieses Repository enthält **ausschließlich die öffentlichen Firmware-Releases**
> (kompilierte `.bin`-Dateien) für das Over-the-Air-Update. Der Quellcode der Firmware
> ist **proprietär** und nicht Teil dieses Repos.

🛒 **Gerät & Infos:** [servicepoint.st](https://servicepoint.st)

---

## Projektbeschreibung

PowerDot Air ist ein kompaktes, eigenständiges Wandgerät für die **Raumluft­qualität**.
Im Kern sitzt ein ESP32-S3 mit einem runden 412 × 412-Touch­display und ein
Multi­gas-/Partikel­sensor **Sensirion SEN66**, der neun Messgrößen gleichzeitig erfasst.

Die Werte werden auf drei Wegen nutzbar gemacht:

1. **Am Gerät** – ein modernes, kreisrundes UI mit Übersichts­seite und je einer Detail­seite
   pro Messgröße, alles farbcodiert nach Luft­qualität und mit Live-Verlaufs­graphen.
2. **Im Browser** – jedes Gerät spannt bei Bedarf einen WLAN-Hotspot auf und liefert eine
   Web-Oberfläche mit Live-Werten, Langzeit-Diagrammen und allen Einstellungen.
3. **In der Smart-Home-Zentrale** – per **MQTT-Discovery** taucht PowerDot Air automatisch
   als Gerät in **Home Assistant** auf: alle Sensoren *und* die wichtigsten Einstellungen
   lassen sich dort ablesen bzw. fernsteuern.

Firmware-Updates laufen bequem **über WLAN (OTA)** direkt aus diesem Repository.

---

## Was gemessen wird

Alle Werte stammen aus **einem** Sensor (Sensirion SEN66) im Dauerbetrieb, 1 × pro Sekunde gepollt.

| Messgröße | Einheit | Hinweis |
|---|---|---|
| CO₂ | ppm | Frischluft ≈ 400, ab ~1000 lüften |
| Feinstaub PM1.0 / PM2.5 / PM4 / PM10 | µg/m³ | vier Partikelgrößen |
| VOC-Index | 1–500 | flüchtige org. Verbindungen, Basislinie ≈ 100 |
| NOx-Index | 1–500 | Stickoxide, Basislinie ≈ 1 |
| Temperatur | °C | Innenraum |
| rel. Luftfeuchte | % | Innenraum |

> VOC/NOx sind **Index-Werte** (kein absolutes ppb): der Sensor lernt im Dauerbetrieb seine
> Basislinie ein und schlägt bei Abweichungen aus. Direkt nach dem Einschalten läuft eine
> kurze Aufwärmphase, in der einzelne Werte noch als „–“ angezeigt werden.

---

## Funktionen

- 🟢 **Farbcodierte Live-Anzeige** – jeder Wert wird nach Qualität eingefärbt
  (grün → gelb → orange → rot, grau während der Aufwärmphase).
- 📈 **Verlaufs­graphen** direkt am Gerät und als Langzeit-Logger in der Web-UI
  (Werte werden lokal gespeichert und über Stunden/Tage dargestellt).
- 🕒 **Übersichts­seite** mit vollflächigem Gesamt-Luftqualitäts­graph hinter einer NTP-Uhr.
- 🌐 **Web-Oberfläche** über eigenen Hotspot oder im Heim-WLAN.
- 🏠 **Home-Assistant-Anbindung** per MQTT-Discovery – Sensoren *und* steuerbare Einstellungen.
- 🔄 **Online-Update (OTA)** über WLAN, direkt aus diesem Repo.
- 🔊 **Akustische Rückmeldung** (abschaltbar / Lautstärke einstellbar).
- ⚙️ **Anpassbar** – Helligkeit, Display-Timeout, Seiten-Wechselzeit, aktive Seiten u. v. m.

---

## Am Gerät – Seiten & Bedienung

Gewischt wird **links/rechts** durch die Hauptseiten (kreisförmig) und **nach oben** in die
Einstellungen.

| Seite | Inhalt |
|---|---|
| **Übersicht** | Gesamt-Luftqualität als vollflächiger Verlaufsgraph hinter der Uhrzeit, plus Gesamtstatus & Datum |
| **CO₂** | großer ppm-Wert, Status (Frisch / Gut / Lüften / Schlecht), Trend-Graph |
| **Feinstaub** | PM2.5 groß, PM1.0 / PM4 / PM10 im Detail, Status, Trend-Graph |
| **Gase** | VOC- & NOx-Index, Status, Trend-Graph |
| **Klima** | Temperatur & Luftfeuchte, Status, Trend-Graph |

**Einstellungen** (Wisch nach oben): Display (Wechselzeit / Timeout / Helligkeit),
Audio (Lautstärke), Netzwerk (Web-Hotspot / WLAN), Gerät (Firmware-Version / Werksreset)
sowie „Aktive Seiten“ zum Ein-/Ausblenden einzelner Hauptseiten.

---

## Web-Oberfläche

Erreichbar über den Geräte-Hotspot oder – nach WLAN-Einrichtung – über die IP im Heimnetz.

| Bereich | Funktion |
|---|---|
| **Dashboard** | Gesamtstatus + alle Luftwerte live, farbcodiert, inkl. System-Infos |
| **Verlauf** | Langzeit-Diagramme mit Min/Max und Hover-Wertanzeige |
| **Einstellungen** | Display, Audio, aktive Seiten |
| **Setup** | WLAN-Einrichtung (Assistent) |
| **Home Assistant** | MQTT-Broker konfigurieren |
| **Firmware** | Online-Update (OTA) oder `.bin` per Drag & Drop |

Die **Uhrzeit** wird per NTP (Zeitzone Europe/Vienna) synchronisiert, sobald WLAN verbunden ist.

---

## Home Assistant

PowerDot Air spricht **MQTT-Discovery**: mit konfiguriertem MQTT-Broker (z. B. Mosquitto)
erscheint das Gerät automatisch unter *Einstellungen → Geräte & Dienste → MQTT* – ohne
manuelles YAML.

- **Sensoren:** CO₂, PM1.0 / PM2.5 / PM4 / PM10, Temperatur, Luftfeuchte, VOC-Index,
  NOx-Index, WLAN-Signal (RSSI).
- **Steuerbar aus Home Assistant:** Display-Helligkeit, Lautstärke, Seiten-Wechselzeit,
  Display-Timeout, Diagramm-Zeitfenster sowie Ein/Aus je Hauptseite.

So lassen sich Automationen bauen (z. B. „Lüften“-Benachrichtigung bei hohem CO₂) und das
Gerät zentral konfigurieren.

---

## Firmware-Update (OTA)

Das Gerät aktualisiert sich **über WLAN** direkt aus diesem Repository:

1. Web-Oberfläche öffnen → **Firmware**.
2. **„Auf Updates prüfen“** – das Gerät vergleicht seine Version mit dem neuesten Release hier.
3. Ist eine **neuere** Version verfügbar, wird das Asset `PowerDot_Air.ino.bin` geladen und installiert.

Der Release-**Tag** entspricht der Firmware-Version (z. B. `v1.0.0-beta`). Es wird nur
aktualisiert, wenn der Tag **höher** ist als die installierte Version – gleiche Version
zeigt „Aktuell“.

Alternativ lässt sich in der Web-UI eine `.bin` auch **manuell per Drag & Drop** hochladen.

---

## Hardware

| Komponente | Detail |
|---|---|
| Board | Waveshare ESP32-S3-Touch-LCD-1.46B (ESP32-S3R8, 16 MB Flash, 8 MB PSRAM) |
| Display | SPD2010, 412 × 412 px **rund**, QSPI, kapazitiver Touch |
| Sensor | **Sensirion SEN66** (CO₂, PM, VOC, NOx, Temp, Feuchte) |
| Audio | PCM5101A, I²S |
| Konnektivität | WLAN 2,4 GHz (Access-Point + Client), MQTT |

---

## Versionen

| Version | Stand | Highlights |
|---|---|---|
| **v1.0.1-beta** | 10.07.2026 | Temperatur-Offset (Kalibrierung gegen Eigenerwärmung, Gerät + Web-UI); überarbeitete Netzwerk-/Hotspot-Seite (Netzwerk-IP dauerhaft sichtbar, Countdown-Zeitleiste, Hotspot „PowerDot Air“); mehrfarbiger & flüssiger Splash; weicher, kontinuierlicher Übersichts-Graph |
| **v1.0.0-beta** | Erst-Release | Luftqualität (SEN66), rundes UI mit Verlaufsgraphen, Web-UI + Langzeit-Logger, Home-Assistant-Anbindung (MQTT-Discovery), OTA-Update |

> Status: **Beta** – aktive Weiterentwicklung.

---

## Hinweise

- Die **Firmware ist proprietär**; dieses Repo veröffentlicht nur die kompilierten
  OTA-`.bin`-Assets. Kein Nachbau, keine Weitergabe des Firmware-Images.
- Gerät, Preis und Support: **[servicepoint.st](https://servicepoint.st)**.
