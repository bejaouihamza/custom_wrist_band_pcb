<div align="center">

# ⌚ Smart Health Wristband — ESP32-S3

**A compact, multi-sensor health-monitoring wristband PCB built around the ESP32-S3-WROOM-1,
featuring heart-rate / SpO₂ sensing, motion tracking, skin-conductance measurement,
haptic feedback, MEMS audio, and a full battery-management system.**

![License](https://img.shields.io/badge/License-CERN--OHL-blue)
![MCU](https://img.shields.io/badge/MCU-ESP32--S3-red)
![PCB](https://img.shields.io/badge/PCB-4--Layer-green)
![Sensors](https://img.shields.io/badge/Sensors-5-orange)
![Firmware](https://img.shields.io/badge/Firmware-MicroPython%20%7C%20C%2B%2B-lightgrey)
![Designer](https://img.shields.io/badge/Designer-Bejaoui%20Hamza-purple)
![Institution](https://img.shields.io/badge/Institution-INSAT-yellow)

</div>

---

## 📷 PCB Renders

| 3D Model (Top) | 3D Model (Bottom) |
|:-:|:-:|
| ![3D Top](https://raw.githubusercontent.com/bejaouihamza/custom_wrist_band_pcb/refs/heads/main/wrist_band_top.jpg) | ![3D Bottom](https://github.com/bejaouihamza/custom_wrist_band_pcb/blob/main/20260327_232929.jpg?raw=true) |

| Full Schematic | PCB Layout (Bottom) |
|:-:|:-:|
|![Schematic](https://github.com/bejaouihamza/custom_wrist_band_pcb/blob/main/Capture%20d'%C3%A9cran%202026-06-07%20174537.png?raw=true) | ![Layout Bottom](https://github.com/bejaouihamza/custom_wrist_band_pcb/blob/main/Capture%20d'%C3%A9cran%202026-06-07%20174246.png?raw=true) |

> 📁 **To add your images:** create an `images/` folder in the repo root and export your renders from KiCad (File → Export → PNG / 3D Viewer → Export PNG). Replace the filenames above to match.

---

## 🌟 Key Features

- **❤️ Heart Rate & SpO₂** — MAX30102 optical sensor with active-low interrupt
- **🏃 Motion & Gesture** — LSM6DSM 6-axis IMU (accelerometer + gyroscope) with wake-up interrupt
- **🧠 Galvanic Skin Response** — INA333 precision instrumentation amplifier, gain configurable via Rg (G = 1 + 100k/Rg), sampled at 7.23 Hz
- **🌡️ Skin Temperature** — TMP117 high-accuracy digital temperature sensor (±0.1 °C)
- **📳 Haptic Feedback** — DRV2605L LRA driver with I²S-triggered vibration patterns
- **🎙️ MEMS Microphone** — INMP441 24-bit I²S digital microphone (left channel)
- **🔋 Battery Management** — BQ24090 USB charger (500 mA) + IRLML6402 P-FET reverse-polarity protection, 1N5819WS Schottky
- **⚡ Power Rail** — TPS63020 buck/boost converter → 3.3 V system rail; XC6206 LDO → 1.8 V for MAX30102 analog supply
- **📡 Wireless** — ESP32-S3-WROOM-1-N8 (Wi-Fi + BLE 5.0, 8 MB flash)
- **🔌 USB-C** — Power-only USB-C receptacle with CC resistors (5.1 kΩ) and ESD protection (ESD5Z24)

---

## 🗂️ Repository Structure

```
wrist_band/
├── hardware/
│   ├── wrist_band.kicad_sch      # KiCad schematic
│   ├── wrist_band.kicad_pcb      # KiCad PCB layout
│   └── gerbers/                  # Production-ready Gerber files
├── firmware/
│   ├── main.py                   # MicroPython entry point
│   └── drivers/                  # Peripheral drivers
├── images/
│   ├── 3d_top.png
│   ├── 3d_bottom.png
│   ├── layout_top.png
│   ├── layout_bottom.png
│   └── schematic.png
├── docs/
│   └── wrist_band.pdf            # Full schematic PDF
└── README.md
```

---

## 🔌 Peripheral Map

| Peripheral | Interface | ESP32-S3 GPIO(s) | I²C Address | Notes |
|---|---|---|---|---|
| **I²C Bus** | I²C | SDA = GPIO5 · SCL = GPIO6 | — | Shared 400 kHz bus |
| **LSM6DSM** (IMU) | I²C | INT1 = GPIO1 | `0x6A` | SDO/SA0 → GND · CS → 3.3 V |
| **MAX30102** (HR / SpO₂) | I²C | INT = GPIO9 | `0x57` | INT active-low |
| **TMP117** (Temperature) | I²C | ALERT = GPIO10 | `0x48` | ADD0 → GND · ALERT active-low |
| **DRV2605L** (Haptic) | I²C | EN/TRIG = GPIO7 | `0x5A` | Enable + trigger shared pin |
| **INMP441** (Microphone) | I²S | SCK = GPIO18 · WS = GPIO17 · SD = GPIO16 | — | 24-bit · L/R → GND (LEFT) |
| **GSR Sensor** | Analog | GPIO4 | — | ADC1_CH3 · amplified by INA333 |
| **BMS Status** | Digital | GPIO21 | — | ~CHG from BQ24090 · LOW = charging |

---

## ⚡ Power Architecture

```
USB-C (5 V VBUS)
       │
       ├─► BQ24090 USB Charger ──────────────► Li-Po Battery (+3.7 V)
       │         │ 500 mA charge current              │
       │         └─ ~CHG → GPIO21                     │
       │                                              │
       └─► Vusb rail ◄──── IRLML6402 (P-FET) ◄───────┘
                                  │
                           TPS63020 Buck/Boost
                                  │
                           3.3 V System Rail
                                  │
                    ┌─────────────┼─────────────┐
                    │             │             │
               XC6206 LDO   ESP32-S3      All Sensors
               (→ +1.8 V)    WROOM-1
               MAX30102 VDD
```

---

## 🔧 Bill of Materials (Key Components)

| Ref | Component | Value / Part Number | Function |
|---|---|---|---|
| U1 | MAX30102 | — | Heart rate & SpO₂ |
| U2 | INA333QDGKRQ1 | G = 1 + 100k/Rg | GSR instrumentation amp |
| U3 | XC6206PxxxMR | 1.8 V LDO | Analog 1.8 V rail |
| U5 | LSM6DSM | — | 6-axis IMU |
| U6 | ESP32-S3-WROOM-1-N8 | 8 MB flash | Main MCU + Wi-Fi/BLE |
| U7 | DRV2605LDGS | — | Haptic LRA driver |
| U8 | TPS63020DSJR | — | Buck/boost 3.3 V |
| U9 | BQ24090DGQ | 500 mA | Li-Po USB charger |
| U10 | TMP117AIDRVR | — | ±0.1 °C temp sensor |
| MK1 | INMP441ACEZ-R7 | — | 24-bit I²S MEMS mic |
| L1 | CKST0402-1.5uH/M1 | 1.5 µH | TPS63020 inductor |
| FB2 | GZ1608D601TFR33 | 600 Ω @ 100 MHz | EMI ferrite bead |
| J2 | USB_C (6P Power) | — | USB-C power input |
| Q8 | IRLML6402 | P-FET | Battery/USB switchover |
| D6 | 1N5819WS | Schottky | Reverse current block |
| D1 | ESD5Z24 | TVS | VBUS ESD protection |

---

## 🚀 Getting Started

### Prerequisites

- [KiCad 9.x](https://www.kicad.org/) — open schematic / PCB files
- [ESP-IDF](https://docs.espressif.com/projects/esp-idf/en/latest/) or [MicroPython for ESP32-S3](https://micropython.org/download/ESP32_GENERIC_S3/)
- Li-Po cell (3.7 V, ≥ 300 mAh recommended)
- USB-C cable for charging / flashing

### Flashing Firmware

```bash
# MicroPython (ampy / mpremote)
mpremote connect /dev/ttyUSB0 run firmware/main.py

# Or with esptool (C++ binary)
esptool.py --chip esp32s3 --port /dev/ttyUSB0 write_flash 0x0 firmware.bin
```

### I²C Bus Scan (MicroPython)

```python
from machine import I2C, Pin
i2c = I2C(0, sda=Pin(5), scl=Pin(6), freq=400_000)
print([hex(a) for a in i2c.scan()])
# Expected: ['0x48', '0x57', '0x5a', '0x6a']
```

---

## 📐 PCB Specifications

| Parameter | Value |
|---|---|
| Layers | 4 |
| Form factor | Wristband / wearable |
| MCU | ESP32-S3-WROOM-1-N8 |
| Supply voltage | 3.7 V Li-Po / 5 V USB-C |
| System rail | 3.3 V (TPS63020 buck/boost) |
| Analog rail | 1.8 V (XC6206 LDO) |
| I²C bus speed | 400 kHz |
| EDA Tool | KiCad 9.0.1 |
| Schematic size | US Legal |

---

## 🧪 Test Points

| Test Point | Signal |
|---|---|
| TP1 | INA333 output (GSR) |
| TP2, TP4 | DRV2605 supply check |
| TP3 | +3.7 V battery rail |
| TP5, TP6 | INA333 differential inputs |
| TP7 | USB VBUS |
| TP8, TP9 | Debug / GPIO |
| TP10, TP11 | MAX30102 supply check |
| TP12, TP13 | IMU supply / debug |

---

## 📄 License

This project is released under the **CERN Open Hardware Licence v2** (CERN-OHL).
See [`LICENSE`](LICENSE) for details.

---

## 👤 Author

**Bejaoui Hamza**
INSAT — Institut National des Sciences Appliquées et de Technologie

---

<div align="center">
<sub>Designed with KiCad 9.0.1 · Built at INSAT</sub>
</div>
