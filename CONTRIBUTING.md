# Contributing to PawSense

## Tracks open for contribution

- **Firmware** (C++ / ESP-IDF) — sensor drivers, BLE GATT, power optimisation
- **Flutter app** (Dart) — screens, charts, BLE reliability, UX
- **Bark ML** (Python / TensorFlow) — dataset labelling, model architecture
- **Hardware** (KiCad) — PCB review, vest pattern, sensor placement
- **Docs** — setup guides, calibration, breed-specific baselines

## Workflow

1. Fork → `git checkout -b feat/your-feature`
2. Follow conventions in `AGENTS.md`
3. Run CI checks locally
4. Open PR against `develop`

## Commit style

```
feat(firmware): add BMP390 FreeRTOS task
fix(app): BLE reconnect after background kill
docs(api): clarify GSR encoding units
chore(bom): update GPS DigiKey price
test(ml): add MFCC extraction unit test
```

## PR checklist

- [ ] CI passes (firmware build + flutter test + model size)
- [ ] BLE UUIDs unchanged — or both `ble_server.cpp` and `gatt_service.dart` updated
- [ ] TFLite model under 500KB if ML changes included
- [ ] `AGENTS.md` updated if architecture decisions changed

## Bug reports

Include: firmware version · phone OS · dog breed and weight · steps to reproduce · app screenshot.
