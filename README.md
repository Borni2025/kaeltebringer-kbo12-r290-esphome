# Kältebringer KBO12-R290 – ESPHome & Home Assistant

**Local control of the Kältebringer KBO12-R290 split air conditioner using ESP32, ESPHome and the Tuya MCU UART protocol – without Tuya Cloud.**

🇩🇪 Deutsch | 🇬🇧 English below

---

## 🇩🇪 Deutsch

### Projektbeschreibung

Dieses Projekt ermöglicht die lokale Steuerung der Split-Klimaanlage **Kältebringer KBO12-R290** über Home Assistant und ESPHome.

Das originale Tuya-WLAN-Modul (WBR1) wird durch einen ESP32 ersetzt. Die Kommunikation mit der Steuerplatine erfolgt über das serielle Tuya-MCU-Protokoll mit 9600 Baud.

Die Datenpunkte wurden mithilfe eines Logikanalysators und PulseView ermittelt und anschließend erfolgreich mit ESPHome getestet.

**Erfolgreich getestete Funktionen:**

- Klimaanlage ein- und ausschalten
- Solltemperatur einstellen und Isttemperatur auslesen
- Betriebsarten: Heizen, Kühlen, Entfeuchten, Lüften
- Lüftergeschwindigkeit: Auto, Low, Medium, High
- ECO- und Sleep-Modus
- Vertikale und horizontale Lamellenschwenkung
- Displaybeleuchtung ein- und ausschalten

Die Kommunikation erfolgt vollständig lokal. Eine Tuya-Cloud-Verbindung ist nicht erforderlich.

### Benötigte Hardware

- Kältebringer KBO12-R290 mit originalem Tuya-WBR1-Modul
- ESP32-Entwicklungsboard (getestet mit ESP32 HW-394)
- Bidirektionaler 5-V-/3,3-V-Pegelwandler (MH-Modul)
- Geeignete Anschlussleitungen und Steckverbinder
- Home Assistant mit ESPHome

### Verdrahtung

Die ursprüngliche WLAN-Modulverbindung besitzt vier Leitungen.

| Originalkabelfarbe | Anschluss am WBR1 | ESP32 |
|---|---|---|
| Weiß | TXD | GPIO17 (TX) |
| Gelb | RXD | GPIO16 (RX) |
| Schwarz | GND | GND |
| Rot | VCC (5 V) | Versorgung über geeignete 5-V-Anbindung |

**Wichtig:** Die Datenleitungen werden über einen geeigneten Pegelwandler mit dem ESP32 verbunden. Der ESP32 arbeitet mit 3,3-V-Logik, während an der ursprünglichen Modulschnittstelle 5 V gemessen wurden. TX und RX sind aus Sicht des ursprünglichen WBR1-Moduls bezeichnet.

Die genaue Versorgung des verwendeten ESP32-Boards muss anhand seiner Hardwareausführung geprüft werden.

**Sicherheit:** Vor Arbeiten an der Klimaanlage ist diese vollständig vom Stromnetz zu trennen. Eingriffe in das Gerät sollten nur von entsprechend fachkundigen Personen vorgenommen werden.

### Identifiz
