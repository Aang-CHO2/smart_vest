# Architecture

## Stack decisions

| Layer | Choice | Rejected | Reason |
|---|---|---|---|
| MCU | ESP32-S3 | nRF52840 | Native I2S for dual MEMS mics + 240MHz for TFLite bark inference |
| App | Flutter | React Native | Compiled Dart — better 60fps for real-time sensor charts |
| Firmware SDK | ESP-IDF 5.x | Arduino-only | FreeRTOS task control, DMA for I2S, full BLE GATT API |
| PCB | KiCad | Altium | Fully open source, JLCPCB compatible |

---

## System overview
┌─────────────────────────────────┐
│         Flutter app             │  iOS + Android
│  Dashboard · Bark · GPS · Health│
│  flutter_reactive_ble · bloc    │
└──────────────┬──────────────────┘
│ BLE 5.0 GATT
│ 6 services
┌──────────────┴──────────────────┐
│       ESP32-S3 firmware         │
│  FreeRTOS · ESP-IDF 5.x        │
│  GATT server · I2S · SPI · I2C │
└──────────────┬──────────────────┘
│ I2C / SPI / UART / I2S / ADC
┌──────────────┴──────────────────┐
│          Sensor layer           │
│  MAX30102 · ICM-42688 · TMP117 │
│  BMP390 · GPS · SPH0645 ×2    │
│  MyoWare EMG · GSR · DRV2605L │
└─────────────────────────────────┘
---

## Firmware task layout (FreeRTOS)

Each sensor group runs in its own task, posting to a central data queue.
The BLE task reads the queue and notifies the connected phone.

main.cpp
├── Task: sensor_vitals      MAX30102, TMP117, piezo resp    5s interval
├── Task: sensor_motion      ICM-42688, BMP390               100ms
├── Task: sensor_gps         u-blox M10 UART                 1s
├── Task: sensor_audio       I2S → MFCC → TFLite bark model  continuous
├── Task: sensor_wellbeing   ADS1115 (GSR, EMG)              10s
├── Task: ble_server         GATT notify on all services      on data ready
└── Task: ota_monitor        Wi-Fi OTA check                  on demand
## Bus layout

| Bus | Devices |
|---|---|
| I2C 400kHz (via TCA9548A mux) | MAX30102, TMP117, BMP390, DRV2605L, ADS1115 |
| SPI 8MHz | ICM-42688 IMU, W25Q32 NOR flash |
| UART 9600 | u-blox MAX-M10S GPS |
| I2S 16kHz stereo | SPH0645 ×2 MEMS microphones |
| ADC via ADS1115 | MyoWare EMG, Grove GSR, piezo respiration |

---

## BLE GATT profile

Device advertises as `PawSense-XXXX` (last 4 of MAC).

| UUID | Service | Interval |
|---|---|---|
| 0x1800 | Vitals — HR, SpO2, temp, resp | 5s |
| 0x1810 | Motion — IMU, GPS, steps, gait | 100ms / 1s |
| 0x1820 | Audio — bark class, confidence | on event |
| 0x1830 | Environment — pressure, altitude | 30s |
| 0x1840 | Wellbeing — GSR, EMG, HRV, stress | 10s |
| 0x1850 | Device — battery, firmware, OTA | on change |

Full encoding spec → `docs/api/ble_gatt.md`

---

## Flutter app structure

app/lib/
├── main.dart
├── ble/
│   ├── ble_manager.dart         scan + connect
│   ├── gatt_service.dart        UUID constants (source of truth)
│   └── ble_stream_handler.dart  decode bytes → typed models
├── models/
│   ├── vitals.dart
│   ├── motion.dart
│   ├── bark_event.dart
│   └── wellbeing.dart
├── state/
│   ├── vitals_bloc.dart
│   ├── motion_bloc.dart
│   └── bark_bloc.dart
└── screens/
├── dashboard_screen.dart
├── bark_screen.dart
├── map_screen.dart
├── health_screen.dart
└── settings_screen.dart
---

## Bark AI pipeline
SPH0645 I2S (16kHz stereo)
↓
I2S DMA buffer (512 samples)
↓
MFCC extraction (26 coefficients)   ~3ms on ESP32-S3
↓
TFLite CNN inference (INT8 quant)   ~15ms on ESP32-S3
↓
BLE notify: bark_class + confidence
↓
Flutter BarkScreen

Model targets: ≥85% accuracy · 5 classes · <500KB after INT8 quantization

Bark classes: `0` none · `1` alert · `2` play · `3` anxiety · `4` pain · `5` boredom

---

## Hardware constraints

- Vest weight: <180g total (PCB ≤70g, battery ≤50g, textile ~60g)
- PCB size: 70×50mm, 4-layer flex-rigid, IP54 conformal coat
- Target girth: 45–70cm adjustable (medium dogs 10–30kg)
- Battery: 3.7V 2500mAh LiPo, ~14h typical life


