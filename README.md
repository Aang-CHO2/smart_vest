# PawSense

Open-source smart dog vest for real-time health, activity, emotion,
and bark monitoring — connected to a Flutter app over BLE.

> **Form factor:** Vest, not a collar. Enables chest-contact sensing,
> EMG muscle monitoring, and GSR stress detection — none of which exist
> in any commercial product today.

---

## Stack

| Layer | Choice | Why |
|---|---|---|
| MCU | ESP32-S3 (240 MHz, BLE 5.0) | Native I2S for MEMS mics + TFLite bark AI |
| Firmware | ESP-IDF 5.x (Apache 2.0) | FreeRTOS, full peripheral control, Wi-Fi OTA |
| App | Flutter (Dart) | Native 60fps, flutter_reactive_ble, tflite_flutter |
| Bark AI | TensorFlow Lite on-device | <20ms inference, no cloud needed |
| Health digest | Claude API | AI-generated weekly health summaries |
| PCB | 4-layer flex-rigid 70×50mm IP54 | Fits vest saddle pocket, waterproof |

---

## Features

| Sensor group | What it measures |
|---|---|
| Health | Heart rate, SpO2, body temperature, respiration rate |
| Motion | 9-axis IMU, GPS tracking, step count, gait analysis |
| Emotion | GSR stress index, EMG muscle tension, HRV |
| Audio | Bark meaning AI — alert / play / anxiety / pain / boredom |
| Environment | Barometric pressure, altitude |

---

## Target dog

Medium breeds 10–30 kg (Beagle, Border Collie, Cocker Spaniel).
Vest girth 45–70 cm adjustable.

---

## Repository structure
smart_vest/
├── firmware/
│   ├── src/           # Main app, BLE GATT server, FreeRTOS tasks
│   └── lib/           # Per-sensor driver wrappers
│       ├── max30102/  # HR + SpO2
│       ├── icm42688/  # 9-axis IMU
│       ├── tmp117/    # Temperature
│       ├── bmp390/    # Barometer
│       ├── gps/       # u-blox M10
│       ├── mic/       # I2S MEMS microphones
│       ├── emg/       # MyoWare 2.0
│       ├── gsr/       # GSR stress sensor
│       └── haptic/    # DRV2605L
├── app/
│   └── lib/
│       ├── screens/   # Dashboard, Bark, GPS, Health, Settings
│       ├── ble/       # BLE manager + GATT client
│       ├── models/    # Typed sensor data models
│       └── state/     # flutter_bloc state management
├── ml/
│   ├── training/      # TensorFlow bark model pipeline
│   └── tflite/        # Quantized model (target <500KB)
├── hardware/
│   ├── bom/           # DigiKey BOM CSV with MPNs
│   ├── schematics/    # KiCad schematic
│   ├── pcb/           # KiCad PCB layout
│   └── vest/          # Vest pattern + sensor placement
├── docs/
│   └── api/           # BLE GATT UUID reference
└── .github/
└── workflows/     # CI: firmware build, flutter test, ML size check

---

## Getting started

```bash
# Flash firmware
cd firmware
idf.py set-target esp32s3
idf.py build flash monitor

# Run Flutter app
cd app
flutter pub get
flutter run

# Train bark model
cd ml
pip install -r requirements.txt
python training/train.py
```

---

## BLE GATT services

| UUID | Service |
|---|---|
| 0x1800 | Vitals — HR, SpO2, temp, respiration |
| 0x1810 | Motion — IMU, GPS, steps, gait |
| 0x1820 | Audio — bark class + confidence |
| 0x1830 | Environment — pressure, altitude |
| 0x1840 | Wellbeing — GSR, EMG, HRV, stress index |
| 0x1850 | Device — battery, firmware version, OTA |

Full spec → [`docs/api/ble_gatt.md`](docs/api/ble_gatt.md)

---

## Roadmap

- [ ] v0.1 — BOM sourced, breadboard prototype wired
- [ ] v0.2 — All sensors streaming over BLE
- [ ] v0.3 — Flutter Dashboard + GPS screens live
- [ ] v0.4 — Bark AI trained (≥85% accuracy, <500KB)
- [ ] v0.5 — Custom PCB v1 (70×50mm, 4-layer)
- [ ] v1.0 — Custom vest + full app + OTA updates
- [ ] v1.1 — Claude API health digest + vet PDF export

---

## License

Code: [MIT](LICENSE) · Hardware: [CERN OHL-P v2](https://ohwr.org/cern_ohl_p_v2.txt)

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) — PRs welcome across all tracks.
