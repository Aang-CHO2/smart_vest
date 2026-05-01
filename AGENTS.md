# AGENTS.md

Instructions for AI agents (OpenClaw, Claude Code, Copilot, etc.) working in this repo.

## Project summary

PawSense is an open-source smart dog vest. MCU: ESP32-S3. App: Flutter. Bark AI: TFLite on-device. Target: medium dogs 10–30kg.

## Four tracks

| Track | Directory | Language | Key constraint |
|---|---|---|---|
| Firmware | `firmware/` | C++ · ESP-IDF 5.x | FreeRTOS tasks, BLE UUIDs in `docs/api/ble_gatt.md` |
| Flutter app | `app/` | Dart · Flutter | BLE via flutter_reactive_ble; state via flutter_bloc |
| Bark ML | `ml/` | Python · TensorFlow | Model must be <500KB after INT8 quantization |
| Hardware | `hardware/` | KiCad + CSV | IP54, PCB ≤70×50mm, vest weight <180g |

## Rules — never break these

1. **BLE GATT UUIDs are the contract.** Never change a UUID without updating both `firmware/src/ble_server.cpp` AND `app/lib/ble/gatt_service.dart`.
2. **One FreeRTOS task per sensor group.** No multi-sensor logic in `main.cpp`.
3. **All Flutter state through flutter_bloc.** No sensor data in widget state.
4. **TFLite model hard limit: 500KB.** Run `ml/tflite/validate_size.py` after every training run.
5. **Conventional commits required.** `feat:` `fix:` `docs:` `chore:` `test:` prefixes.
6. **Target dog: 10–30kg medium breeds.** Flag any assumption that differs.

## Key files

| File | Purpose |
|---|---|
| `docs/api/ble_gatt.md` | BLE characteristic encoding — single source of truth |
| `app/lib/ble/gatt_service.dart` | UUID constants for Flutter app |
| `firmware/src/ble_server.cpp` | UUID constants for firmware |
| `hardware/bom/pawsense_bom.csv` | Full BOM with DigiKey MPNs |
| `ml/tflite/pawsense_bark.tflite` | Binary — CI generated, never commit manually |

## Open-source library index

| Library | License | Purpose |
|---|---|---|
| ESP-IDF 5.x | Apache 2.0 | Firmware SDK |
| SparkFun MAX3010x | MIT | HR/SpO2 driver |
| finani/ICM42688 | MIT | IMU driver |
| SparkFun TMP117 | MIT | Temp driver |
| libdriver/bmp390 | MIT | Baro driver |
| u-blox/ubxlib | Apache 2.0 | GPS driver |
| Adafruit DRV2605 | BSD | Haptic driver |
| Adafruit ADS1X15 | BSD | ADC driver |
| flutter_reactive_ble | MIT | BLE manager |
| flutter_bloc | MIT | State management |
| tflite_flutter | Apache 2.0 | On-device ML |

## OpenClaw commands

```
@pawsense write firmware for <sensor>
@pawsense scaffold Flutter <screen> screen
@pawsense update BOM CSV
@pawsense create GitHub issue: <description>
@pawsense review <file>
```
