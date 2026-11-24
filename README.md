
# Kältebringer KBO12-R290 – ESPHome & Home Assistant

**Fully local control of the Kältebringer KBO12-R290 split air conditioner using ESP32, ESPHome and the Tuya MCU UART protocol – without Tuya Cloud.**

**Languages:** Deutsch | English below

---

# Deutsch

## Projektbeschreibung

Dieses Projekt ermöglicht die vollständig lokale Steuerung der Split-Klimaanlage **Kältebringer KBO12-R290** über Home Assistant und ESPHome.

Das originale Tuya-WLAN-Modul (WBR1) wird durch einen ESP32 ersetzt. Die Kommunikation erfolgt über das serielle Tuya-MCU-Protokoll mit 9600 Baud.

Die Datenpunkte wurden mit einem USB-Logikanalysator und PulseView aufgezeichnet, analysiert und anschließend mit ESPHome erfolgreich getestet.

Die Steuerung benötigt weder die Smart-Life-App noch eine Verbindung zur Tuya-Cloud.

## Funktionen

Folgende Funktionen wurden erfolgreich in Home Assistant getestet:

- Klimaanlage ein- und ausschalten
- Solltemperatur einstellen
- Aktuelle Raumtemperatur auslesen
- Betriebsarten: Heizen, Kühlen, Entfeuchten und Lüften
- Lüftergeschwindigkeit: Auto, Low, Medium und High
- ECO-Modus
- Sleep-Modus
- Vertikale Lamellenschwenkung
- Horizontale Lamellenschwenkung
- Displaybeleuchtung ein- und ausschalten

Die Betriebsarten und Lüftergeschwindigkeiten werden innerhalb der Klima-Entität in Home Assistant angezeigt.

Die Displaybeleuchtung wird als separater Schalter bereitgestellt.

## Benötigte Hardware

- Kältebringer KBO12-R290 mit originalem Tuya-WBR1-Modul
- ESP32-Entwicklungsboard (getestet mit ESP32 HW-394)
- Bidirektionaler 5-V-/3,3-V-Pegelwandler (MH-Modul)
- Geeignete Anschlussleitungen und Steckverbinder
- Home Assistant
- ESPHome

Für die ursprüngliche Protokollanalyse wurden zusätzlich ein USB-Logikanalysator und PulseView verwendet.

Diese werden für den späteren Betrieb nicht benötigt.

## Hardware und Verdrahtung

Die ursprüngliche Verbindung zum WBR1-Modul besteht aus vier Leitungen.

### Anschlussbelegung

| Originalkabelfarbe | WBR1-Anschluss | ESP32-Anschluss |
|---|---|---|
| Weiß | TXD | GPIO17 (TX) |
| Gelb | RXD | GPIO16 (RX) |
| Schwarz | GND | GND |
| Rot | VCC (+5 V) | Geeignete 5-V-Versorgung |

**Wichtig:** Die Bezeichnungen TXD und RXD beziehen sich auf das ursprüngliche WBR1-Modul.

Der ESP32 übernimmt die Funktion des entfernten WLAN-Moduls.

### Pegelwandler

Zwischen den UART-Datenleitungen der Klimaanlage und dem ESP32 wird ein geeigneter 5-V-/3,3-V-Pegelwandler verwendet.

Die tatsächliche Pegelwandlung erfolgt zwischen:

- HV-Seite: 5-V-Seite der Klimaanlagenschnittstelle
- LV-Seite: 3,3-V-Seite des ESP32

Der ESP32 darf an seinen GPIO-Pins nicht direkt mit 5-V-Logiksignalen betrieben werden.

Die gemeinsame Masseverbindung ist erforderlich.

Die Spannungsversorgung des ESP32 muss für das verwendete Entwicklungsboard geeignet und ausreichend dimensioniert sein.

### Sicherheitshinweise

**Achtung: Eingriffe in elektrische Geräte können gefährlich sein.**

Vor Arbeiten an der Klimaanlage muss das Gerät vollständig vom Stromnetz getrennt werden.

Die Installation sollte ausschließlich durch Personen erfolgen, die über die notwendigen elektrotechnischen Fachkenntnisse verfügen.

Die beschriebenen Anschlüsse wurden an einem konkret getesteten Gerät ermittelt. Andere Hardwareversionen können abweichen.

Diese Anleitung behandelt ausschließlich die elektronische Steuerschnittstelle. Eingriffe in den Kältemittelkreislauf sind nicht Bestandteil des Projekts.

## Tuya-MCU-Protokoll

Die Klimaanlage verwendet das serielle Tuya-MCU-Protokoll.

**Schnittstellenparameter:**

| Parameter | Wert |
|---|---|
| Baudrate | 9600 |
| Datenbits | 8 |
| Parität | Keine |
| Stoppbits | 1 |
| Tuya Product ID | `64z8cyptsfd23tas` |
| Gemeldete MCU-Version | `1.0.0` |

Die Kommunikation erfolgt bidirektional zwischen dem ESP32 und der Steuerplatine der Klimaanlage.

Der Tuya-Handshake und die Übertragung der Datenpunkte funktionieren mit der offiziellen ESPHome-Tuya-Komponente.

## Identifizierte Datenpunkte

Die folgenden Datenpunkte wurden durch UART-Aufzeichnungen identifiziert und für die ESPHome-Steuerung verwendet.

| DP | Funktion | Werte |
|---|---|---|
| 1 | Power | 0 = Aus, 1 = Ein |
| 2 | Solltemperatur | Temperatur in °C |
| 3 | Isttemperatur | Temperatur in °C |
| 4 | Betriebsart | 0 = Kühlen, 1 = Heizen, 2 = Trocknen, 3 = Lüften, 4 = Auto |
| 5 | Lüftergeschwindigkeit | 0 = Auto, 2 = Low, 3 = Medium, 4 = High |
| 8 | ECO | 0 = Aus, 1 = Ein |
| 101 | Sleep | 0 = Aus, 1 = Ein |
| 104 | Displaybeleuchtung | 0 = Aus, 1 = Ein |
| 106 | Vertikale Lamellenschwenkung | 0 = Aus, 1 = Ein |
| 107 | Horizontale Lamellenschwenkung | 0 = Aus, 1 = Ein |

### Hinweise zu weiteren Datenpunkten

Weitere Datenpunkte wurden während der Analyse beobachtet:

- DP 12
- DP 102
- DP 103
- DP 108
- DP 109
- DP 110

Deren vollständige Bedeutung ist noch nicht geklärt.

Beim Lüfter ist der Enum-Wert `1` bislang ebenfalls nicht eindeutig zugeordnet.

## ESPHome-Konfiguration

Die getestete ESPHome-Konfiguration befindet sich in:

**[klima1.yaml](klima1.yaml)**

Sie verwendet die offiziellen ESPHome-Komponenten:

- Tuya MCU
- Tuya Climate
- Tuya Switch

Die Konfiguration wurde erfolgreich mit **ESPHome 2026.11.0** und dem ESP-IDF-Framework getestet.

### Installation

1. ESP32 entsprechend der Anschlussbelegung anschließen.
2. ESPHome in Home Assistant öffnen.
3. Ein neues ESP32-Gerät anlegen.
4. Die Konfiguration aus `klima1.yaml` übernehmen.
5. Eigene WLAN-Zugangsdaten in ESPHome-Secrets hinterlegen.
6. Konfiguration kompilieren und auf den ESP32 übertragen.
7. ESP32 mit der Klimaanlagensteuerung verbinden.
8. Gerät in Home Assistant einbinden.
9. Funktionen testen.

Vor elektrischen Anschlussarbeiten muss die Klimaanlage spannungsfrei sein.

### WLAN-Zugangsdaten

Die YAML verwendet:

```yaml
wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
```

Die persönlichen WLAN-Zugangsdaten werden nicht im Repository gespeichert.

## Fehlersuche

### Tuya initialization failed at init_state 0

Während der Entwicklung trat zunächst folgende Meldung auf:

```text
[E][tuya:540]: Initialization failed at init_state 0
```

Ursache kann eine fehlende oder fehlerhafte UART-Verbindung sein.

Im getesteten Aufbau wurde unter anderem ein Kontaktproblem auf der Masseleitung festgestellt.

Nach Überprüfung der Verbindung konnte die Tuya-Kommunikation erfolgreich initialisiert werden.

### Erfolgreiche Initialisierung

Bei erfolgreicher Verbindung meldet ESPHome die erkannten Datenpunkte und die Produktkennung:

```text
Product: '{"p":"64z8cyptsfd23tas","v":"1.0.0","m":1}'
```

Die angezeigten Datenpunkte zeigen, dass die Kommunikation mit der Klimaanlagensteuerung funktioniert.

## Teststatus

**Erfolgreich getestet:**

- Modell: Kältebringer KBO12-R290
- ESP32: HW-394
- Framework: ESP-IDF
- ESPHome: 2026.11.0
- Tuya UART: 9600 Baud, 8N1
- Bidirektionale Kommunikation
- Temperatur- und Betriebsartensteuerung
- Lüftersteuerung
- ECO und Sleep
- Beide Swing-Richtungen
- Displaybeleuchtung
- Integration in Home Assistant
- Betrieb ohne originale Tuya-WLAN-Platine
- Keine Tuya-Cloud erforderlich

Die Lösung wurde an einer Kältebringer KBO12-R290 erfolgreich getestet.

Die Kompatibilität mit anderen Modellen, Hardwareversionen oder Firmwareständen wurde noch nicht bestätigt.

---

# English

## Overview

This project enables fully local control of the **Kältebringer KBO12-R290 split air conditioner** using ESP32, ESPHome and Home Assistant.

The original Tuya WBR1 Wi-Fi module is replaced with an ESP32.

Communication with the air conditioner's control board uses the Tuya MCU UART protocol at 9600 baud.

The datapoints were identified using a USB logic analyzer and PulseView, then successfully tested with ESPHome.

**No Tuya Cloud or Smart Life connection is required.**

## Features

Successfully tested functions include:

- Power on/off
- Target temperature control
- Current room temperature
- Heating mode
- Cooling mode
- Dry mode
- Fan-only mode
- Auto, low, medium and high fan speeds
- ECO preset
- Sleep preset
- Vertical swing
- Horizontal swing
- Display light on/off

Most functions are provided through the Home Assistant climate entity.

Display lighting is exposed as an additional switch entity.

## Required hardware

- Kältebringer KBO12-R290 with original WBR1 module
- ESP32 development board (tested with HW-394)
- Bidirectional 5 V / 3.3 V logic level converter
- Suitable wiring and connectors
- Home Assistant
- ESPHome

A USB logic analyzer and PulseView were used during development but are not required for normal operation.

## Wiring

The original WBR1 interface uses four wires.

| Original wire color | WBR1 pin | ESP32 connection |
|---|---|---|
| White | TXD | GPIO17 (TX) |
| Yellow | RXD | GPIO16 (RX) |
| Black | GND | GND |
| Red | VCC (+5 V) | Suitable 5 V supply |

TXD and RXD refer to the original WBR1 module.

The ESP32 replaces the original Wi-Fi module.

### Logic level conversion

An appropriate voltage level converter is required between the air conditioner UART interface and the ESP32.

- HV: 5 V interface side
- LV: 3.3 V ESP32 side

Do not connect 5 V UART signals directly to ESP32 GPIO pins.

A common ground is required.

Ensure the ESP32 receives a suitable and sufficiently rated power supply.

### Safety

**Warning: Modifying electrical appliances can be dangerous.**

Disconnect the appliance from mains power before working on its internal electronics.

Installation should only be performed by appropriately qualified persons.

The pinout was determined on one tested unit. Other hardware revisions may differ.

This project covers only the electronic control interface and does not involve modifications to the refrigerant system.

## Tuya MCU protocol

| Parameter | Value |
|---|---|
| Baud rate | 9600 |
| Data bits | 8 |
| Parity | None |
| Stop bits | 1 |
| Product ID | `64z8cyptsfd23tas` |
| Reported MCU version | `1.0.0` |

The official ESPHome Tuya component successfully handles communication and initialization.

## Identified datapoints

| DP | Function | Values |
|---|---|---|
| 1 | Power | 0 = Off, 1 = On |
| 2 | Target temperature | °C |
| 3 | Current temperature | °C |
| 4 | Operating mode | 0 = Cool, 1 = Heat, 2 = Dry, 3 = Fan, 4 = Auto |
| 5 | Fan speed | 0 = Auto, 2 = Low, 3 = Medium, 4 = High |
| 8 | ECO | 0 = Off, 1 = On |
| 101 | Sleep | 0 = Off, 1 = On |
| 104 | Display light | 0 = Off, 1 = On |
| 106 | Vertical swing | 0 = Off, 1 = On |
| 107 | Horizontal swing | 0 = Off, 1 = On |

Additional datapoints were observed but have not yet been fully identified.

Fan speed enum value `1` also remains undocumented.

## ESPHome configuration

The tested ESPHome configuration is available here:

**[klima1.yaml](klima1.yaml)**

It uses the official ESPHome Tuya MCU, Tuya Climate and Tuya Switch components.

### Installation

1. Connect the ESP32 according to the documented wiring.
2. Open ESPHome in Home Assistant.
3. Create a new ESP32 device.
4. Copy the configuration from `klima1.yaml`.
5. Set up your own Wi-Fi credentials using ESPHome secrets.
6. Compile and flash the firmware.
7. Connect the ESP32 to the air conditioner control board.
8. Add the device to Home Assistant.
9. Test the controls.

Disconnect mains power before performing wiring work.

## Troubleshooting

If ESPHome reports:

```text
Initialization failed at init_state 0
```

Check the UART connections, voltage levels, power supply and common ground.

During development, an intermittent ground connection was one of the issues encountered.

Successful initialization reports the product information:

```text
Product: '{"p":"64z8cyptsfd23tas","v":"1.0.0","m":1}'
```

## Tested configuration

- Kältebringer KBO12-R290
- ESP32 HW-394
- ESPHome 2026.11.0
- ESP-IDF framework
- Tuya MCU UART at 9600 baud
- Successful bidirectional UART communication
- Home Assistant climate control
- Fan and swing controls
- ECO and Sleep
- Display light switch
- Fully local operation without Tuya Cloud

Compatibility with other models and firmware versions has not yet been verified.

---

## References

- [ESPHome – Tuya MCU](https://esphome.io/components/tuya/)
- [ESPHome – Tuya Climate](https://esphome.io/components/climate/tuya/)
- [ESPHome – Tuya Switch](https://esphome.io/components/switch/tuya/)
- [Home Assistant](https://www.home-assistant.io/)

## License

This project is licensed under the MIT License.

See the [LICENSE](LICENSE) file for details.
