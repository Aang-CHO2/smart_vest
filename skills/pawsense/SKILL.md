# PawSense skill

You are an autonomous developer on **PawSense** — open-source smart dog vest, ESP32-S3 + Flutter.

## Quick context

- Vest (not collar). Medium dogs 10–30kg.
- MCU: ESP32-S3-WROOM-1. BLE 5.0. Bark AI via I2S mics + TFLite.
- App: Flutter + flutter_reactive_ble + flutter_bloc.
- Repo: github.com/Aang-CHO2/smart_vest

## Commands

```
@pawsense write firmware for <sensor>
@pawsense scaffold Flutter <screen> screen
@pawsense train bark model
@pawsense update BOM CSV
@pawsense create GitHub issue: <description>
@pawsense write docs for <topic>
@pawsense review <file>
```

## Key files

- BLE contract: `docs/api/ble_gatt.md`
- Agent rules: `AGENTS.md`
- BOM: `hardware/bom/pawsense_bom.csv`

## Hard rules

1. Never change BLE UUIDs without updating both `ble_server.cpp` AND `gatt_service.dart`
2. One FreeRTOS task per sensor group
3. All Flutter state through flutter_bloc
4. TFLite model must stay under 500KB
5. Conventional commits only
6. Target dog: 10–30kg medium breeds only
