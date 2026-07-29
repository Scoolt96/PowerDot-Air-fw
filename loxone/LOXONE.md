# PowerDot Air – Loxone-Anbindung

Zwei Richtungen, beide über das lokale Netzwerk:
- **Werte → Loxone:** der Dot sendet alle Luftwerte per UDP an den Miniserver.
- **Steuern → Dot:** Loxone steuert Display + Audio des Dots per HTTP (Helligkeit, Lautstärke, Timeout, Wechselzeit, Display an/aus).

Die zwei fertigen Vorlagen liegen in diesem Ordner:

| Datei | Loxone-Ordner | Zweck |
|---|---|---|
| `VIU_PowerDot Air.xml` | `…\Loxone Config\Templates\VirtualIn\` | Werte empfangen (9 Befehle) |
| `VO_PowerDot Air Steuerung.xml` | `…\Loxone Config\Templates\VirtualOut\` | Dot steuern (5 Befehle) |

Nach dem Kopieren **Loxone Config neu starten** (Vorlagen werden nur beim Start gelesen).

## Voraussetzung
- Dot im selben WLAN wie der Miniserver.
- **Feste IP für den Dot** (DHCP-Reservierung) empfohlen — die Steuerung spricht ihn per IP an.

## Teil 1 – Werte in Loxone (Eingang)
1. Am Dot: Web-UI → **Smart Home → Loxone** → Miniserver-IP + Port **7000**, **Aktiv**. Der Dot sendet dann alle 10 s ein UDP-Paket.
2. Loxone Config: bei den Virtuellen Eingängen → **Vordefinierte UDP-Geräte** → **PowerDot Air** einfügen.
3. Port 7000; optional Absenderadresse = IP des Dots. In den Miniserver speichern.

Übertragene Werte:

| Befehl | Einheit |
|---|---|
| CO2 | ppm |
| Temperatur | °C |
| Luftfeuchte | % |
| PM1.0 / PM2.5 / PM4.0 / PM10 | µg/m³ |
| VOC Index / NOx Index | – |

## Teil 2 – Dot steuern (Ausgang)
1. Loxone Config: bei den Virtuellen Ausgängen → **Vordefinierte Geräte** → **PowerDot Air Steuerung** einfügen.
2. **Adresse** auf `http://<IP-des-Dots>` setzen (in der Vorlage voreingestellt — pro Installation anpassen).
3. Die Befehle mit Slidern / Zeitschaltuhren verbinden.

| Befehl | Typ | Wirkung |
|---|---|---|
| Helligkeit | Analog | Display-Helligkeit 10–100 % |
| Lautstärke | Analog | 0–100 % |
| Display-Timeout | Analog | 0–300 s (0 = nie aus) |
| Wechselzeit | Analog | 0–60 s (0 = aus) |
| Display an/aus | Digital | Display hart an/aus |

**Display an/aus:** Aus = Display bleibt dunkel, Touch weckt es nicht (Loxone besitzt den Zustand). Nicht dauerhaft gespeichert → nach einem Neustart des Dots ist es wieder an. Loxone sollte den Zustand per Zeitschaltuhr regelmäßig senden, nicht nur bei der Flanke.

Beispiel „abends runterfahren": Zeitschaltuhr → Helligkeit abends 15, morgens 80; oder Display an/aus zu festen Zeiten.

## Endpoints (für eigene Integrationen)
Alle als HTTP-GET am Dot, Antwort = aktueller Wert:

| Endpoint | Bereich |
|---|---|
| `/setbright?v=` | 10–100 |
| `/setvol?v=` | 0–100 |
| `/settimeout?v=` | 0–300 |
| `/setscroll?v=` | 0–60 |
| `/display?on=` | 0 / 1 |

Die vier Slider-Werte spiegeln automatisch das Geräte-Menü und die Web-Oberfläche des Dots.
