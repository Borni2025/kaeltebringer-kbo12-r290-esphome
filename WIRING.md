# Kältebringer KBO12-R290 – Wiring Guide

## Deutsch – Anschlussanleitung

Diese Anleitung beschreibt die Verbindung eines ESP32 HW-394 mit der Steuerplatine der Kältebringer KBO12-R290 als Ersatz für das originale Tuya-WBR1-WLAN-Modul.

Die Pinbelegung wurde an einer KBO12-R290 ermittelt und mit ESPHome 2026.11.0 erfolgreich getestet.

### 1. Benötigte Hardware

- ESP32 HW-394 (ESP32 DevKit)
- Bidirektionaler 5-V-/3,3-V-Pegelwandler (MH-Modul)
- Geeignete Anschlussleitungen und isolierte Steckverbinder
- Kältebringer KBO12-R290 mit ursprünglichem WBR1-Anschluss

### 2. Originale Anschlussleitungen

| Kabelfarbe | Ursprünglicher WBR1-Anschluss | Funktion |
|---|---|---|
| Rot | VCC | +5 V |
| Schwarz | GND | Masse |
| Weiß | TXD | Sendedaten des WLAN-Moduls |
| Gelb | RXD | Empfangsdaten des WLAN-Moduls |

Die Bezeichnungen TXD und RXD beziehen sich auf das entfernte WBR1-Modul.

### 3. Spannungsversorgung

| Quelle | Ziel |
|---|---|
| Rot (+5 V) | ESP32 VIN |
| Rot (+5 V) | Pegelwandler HV |
| Schwarz (GND) | ESP32 GND |
| Schwarz (GND) | Pegelwandler GND |
| ESP32 3V3 | Pegelwandler LV |

**Hinweis:** Die Versorgung des ESP32 über die ursprüngliche 5-V-Leitung wurde im beschriebenen Testaufbau erfolgreich verwendet. Die Strombelastbarkeit dieser Versorgung wurde jedoch nicht gesondert nachgewiesen. Vor einem dauerhaften Einbau sind die Spannungsstabilität und ausreichende Stromreserve zu überprüfen.

VIN darf nur gemäß den Spezifikationen des tatsächlich verwendeten ESP32-Boards beschaltet werden. Die 5-V-Leitung darf niemals direkt an ESP32-GPIOs oder 3V3 angeschlossen werden.

### 4. UART-Datenleitungen

| Originalleitung | Pegelwandler (5-V-Seite) | Pegelwandler (3,3-V-Seite) | ESP32 |
|---|---|---|---|
| Weiß – TXD | HV1 | LV1 | GPIO17 (TX) |
| Gelb – RXD | HV2 | LV2 | GPIO16 (RX) |

Der Pegelwandler muss für eine UART-Verbindung mit 9600 Baud geeignet sein.

Die HV- und LV-Anschlüsse sind Referenzspannungsanschlüsse. HV1/HV2 und LV1/LV2 sind die jeweiligen Signalanschlüsse.

### 5. ESPHome-Konfiguration

Die getestete ESPHome-Konfiguration befindet sich in [klima1.yaml](klima1.yaml).

Verwendete UART-Einstellungen:

```yaml
uart:
  tx_pin: GPIO17
  rx_pin: GPIO16
  baud_rate: 9600
```

Die Kommunikation verwendet 8 Datenbits, keine Parität und 1 Stoppbit.

### 6. Sicherheits- und Prüfhinweise

**Achtung: Das Innengerät ist ein netzbetriebenes elektrisches Gerät.**

- Vor dem Öffnen und Anschließen das Gerät vollständig spannungsfrei schalten.
- Spannungsfreiheit und Eignung der Schnittstelle fachgerecht prüfen.
- Keine ungeschützten Leiter oder losen Steckbrücken im Gerät belassen.
- Den ESP32 und den Pegelwandler berührungsgeschützt und mechanisch sicher unterbringen.
- Die Belastbarkeit der 5-V-Versorgung sowie eine mögliche elektrische Verbindung zur Netzspannung vor dem Dauerbetrieb fachkundig prüfen.
- Andere Geräte- oder Platinenrevisionen können eine abweichende Belegung haben.

Die beschriebene Beschaltung ist eine Dokumentation des getesteten Aufbaus, keine allgemeine Freigabe für sämtliche Kältebringer-Modelle.

---

## English – Wiring Guide

This guide describes the replacement of the original Tuya WBR1 Wi-Fi module in the Kältebringer KBO12-R290 with an ESP32 HW-394 running ESPHome.

### 1. Required hardware

- ESP32 HW-394 development board
- Bidirectional 5 V / 3.3 V logic level converter (MH module)
- Suitable insulated wiring and connectors
- Kältebringer KBO12-R290 with original WBR1 interface

### 2. Original wires

| Wire color | Original WBR1 pin | Function |
|---|---|---|
| Red | VCC | +5 V |
| Black | GND | Ground |
| White | TXD | Wi-Fi module transmit |
| Yellow | RXD | Wi-Fi module receive |

TXD and RXD are named from the original WBR1 module's perspective.

### 3. Power connections

| Source | Destination |
|---|---|
| Red (+5 V) | ESP32 VIN |
| Red (+5 V) | Level shifter HV |
| Black (GND) | ESP32 GND |
| Black (GND) | Level shifter GND |
| ESP32 3V3 | Level shifter LV |

The original air conditioner 5 V supply powered the ESP32 successfully during testing. However, its available current capacity has not been independently verified. Check supply stability, available current, and the ESP32 board's VIN specifications before permanent installation.

Never connect 5 V directly to ESP32 GPIOs or its 3V3 pin.

### 4. UART connections

| Original wire | Level shifter HV side | Level shifter LV side | ESP32 |
|---|---|---|---|
| White – TXD | HV1 | LV1 | GPIO17 (TX) |
| Yellow – RXD | HV2 | LV2 | GPIO16 (RX) |

The level shifter must support UART operation at 9600 baud.

HV and LV are voltage reference pins; HV1/HV2 and LV1/LV2 are signal pins.

### 5. ESPHome configuration

See the tested [klima1.yaml](klima1.yaml) configuration.

UART settings:

```yaml
uart:
  tx_pin: GPIO17
  rx_pin: GPIO16
  baud_rate: 9600
```

Serial format: 8N1.

### 6. Safety

**Warning: The indoor air conditioner is a mains-powered appliance.**

Disconnect mains power before opening or modifying the device. Have electrical isolation, interface voltage levels, and power supply capacity checked by a qualified person.

Use properly insulated, mechanically secured connections and an appropriate enclosure for the electronics.

This wiring was tested on one KBO12-R290. Other hardware revisions may differ.
