# PowerDot Air – Loxone-Anbindung

Zwei Richtungen über das lokale Netzwerk:
- **Werte → Loxone:** der Dot sendet alle Luftwerte per UDP an den Miniserver.
- **Steuern → Dot:** Loxone steuert Display + Audio des Dots per HTTP (Helligkeit, Lautstärke, Timeout, Wechselzeit, Display an/aus).

Ablauf: Vorlagen kopieren → Loxone Config neu starten → je Vorlage einfügen → nachkonfigurieren.

## 1. Vorlagen einspielen

Die zwei `.xml`-Dateien aus diesem Ordner in die Vorlagenordner der Loxone Config kopieren:

| Vorlage | Zielordner |
|---|---|
| `VIU_PowerDot Air.xml` | `…\Dokumente\Loxone\Loxone Config\Templates\VirtualIn\` |
| `VO_PowerDot Air Steuerung.xml` | `…\Dokumente\Loxone\Loxone Config\Templates\VirtualOut\` |

Danach **Loxone Config schließen und neu öffnen** — die Vorlagen werden nur beim Programmstart eingelesen.

## 2. Werte empfangen (Eingang)

**Vorlage einfügen:**
1. Loxone Config mit dem Miniserver verbinden.
2. Reiter **Peripherie** → Bereich Virtuelle Eingänge → Knopf **„Vordefinierte UDP-Geräte"** (kleiner Pfeil ▾) → **„PowerDot Air"** wählen. Der Eingang wird mit allen 9 Befehlen eingefügt.

**Danach noch zu tun:**
- *(Optional)* In den Eigenschaften des Eingangs die **Senderadresse** = IP des Dots eintragen (dann nimmt Loxone nur die Pakete von diesem Dot). Der **UDP-Empfangsport 7000** ist bereits gesetzt.
- **Am Dot:** Web-UI → **Smart Home → Loxone** → Miniserver-IP + Port **7000** eintragen, **Aktiv**. Der Dot sendet dann alle 10 s ein Paket.
- **In den Miniserver speichern.** Die Befehle füllen sich mit den Live-Werten (im UDP-Monitor siehst du die eintreffenden Pakete).
- Jeden Wert per Drag & Drop ins Programm bzw. in die Visualisierung ziehen, um ihn zu verwenden.

Empfangene Werte:

| Befehl | Einheit |
|---|---|
| CO2 | ppm |
| Temperatur | °C |
| Luftfeuchte | % |
| PM1.0 / PM2.5 / PM4.0 / PM10 | µg/m³ |
| VOC Index / NOx Index | – |

## 3. Dot steuern (Ausgang)

**Vorlage einfügen:**
1. Reiter **Peripherie** → Bereich Virtuelle Ausgänge → Knopf **„Vordefinierte Geräte"** (▾) → **„PowerDot Air Steuerung"**. Der Ausgang wird mit 5 Befehlen eingefügt.

**Danach noch zu tun (wichtig):**
- **Adresse** des Ausgangs auf `http://<IP-des-Dots>` setzen. In der Vorlage steht eine **Beispiel-IP (192.168.68.150)** — die musst du auf deinen Dot anpassen.
- Jeden Befehl mit einer Quelle verbinden:
  - **Helligkeit / Lautstärke / Timeout / Wechselzeit** (analog) → mit einem Slider, einem Analogwert oder einer **Zeitschaltuhr** verbinden.
  - **Display an/aus** (digital) → mit einem Schalter oder einem Zeitschaltuhr-Ausgang verbinden.
- **In den Miniserver speichern.**

Befehle:

| Befehl | Typ | Wirkung |
|---|---|---|
| Helligkeit | Analog | Display-Helligkeit 10–100 % |
| Lautstärke | Analog | 0–100 % |
| Display-Timeout | Analog | 0–300 s (0 = nie aus) |
| Wechselzeit | Analog | 0–60 s (0 = aus) |
| Display an/aus | Digital | Display hart an/aus |

**Display an/aus:** Aus = Display bleibt dunkel, Touch weckt es nicht (Loxone besitzt den Zustand). Nicht dauerhaft gespeichert → nach einem Neustart des Dots ist es wieder an. Loxone sollte den Zustand per Zeitschaltuhr **regelmäßig** senden, nicht nur bei der Flanke.

Beispiel „abends runterfahren": eine Zeitschaltuhr → Helligkeit abends auf 15, morgens auf 80; oder Display an/aus zu festen Zeiten.

## Tipps
- **Feste IP für den Dot** (DHCP-Reservierung im Router) — die Steuerung spricht ihn per IP an; ändert sich die IP, funktioniert der Ausgang nicht mehr.
- Nach jeder Änderung an den Vorlagendateien: **Loxone Config neu starten**, sonst tauchen sie nicht auf.

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
