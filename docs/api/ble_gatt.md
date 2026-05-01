# BLE GATT API reference

Source of truth for all service and characteristic UUIDs.
Both `firmware/src/ble_server.cpp` and `app/lib/ble/gatt_service.dart` must match this exactly.

**Device name:** `PawSense-XXXX` (last 4 hex digits of MAC)

---

## 0x1800 — Vitals

| Characteristic | UUID | Type | Encoding | Interval |
|---|---|---|---|---|
| Heart rate | 0x1801 | NOTIFY | uint8, bpm | 5s |
| SpO2 | 0x1802 | NOTIFY | uint8, % | 5s |
| Body temperature | 0x1803 | NOTIFY | int16 LE, °C×100 | 5s |
| Respiration rate | 0x1804 | NOTIFY | uint8, breaths/min | 5s |

## 0x1810 — Motion

| Characteristic | UUID | Type | Encoding | Interval |
|---|---|---|---|---|
| Accelerometer | 0x1811 | NOTIFY | int16 LE ×3 (X,Y,Z), mg | 100ms |
| Gyroscope | 0x1812 | NOTIFY | int16 LE ×3 (X,Y,Z), dps×10 | 100ms |
| GPS latitude | 0x1813 | NOTIFY | int32 LE, degrees×1e6 | 1s |
| GPS longitude | 0x1814 | NOTIFY | int32 LE, degrees×1e6 | 1s |
| Step count | 0x1815 | NOTIFY | uint32 LE, cumulative | 1s |
| Gait score | 0x1816 | NOTIFY | uint8, 0–100 (100=normal) | 5s |

## 0x1820 — Audio

| Characteristic | UUID | Type | Encoding | Interval |
|---|---|---|---|---|
| Bark class | 0x1821 | NOTIFY | uint8: 0=none 1=alert 2=play 3=anxiety 4=pain 5=boredom | on event |
| Confidence | 0x1822 | NOTIFY | uint8, 0–100% | on event |
| Audio level | 0x1823 | NOTIFY | uint8, dB ambient | 1s |

## 0x1830 — Environment

| Characteristic | UUID | Type | Encoding | Interval |
|---|---|---|---|---|
| Pressure | 0x1831 | NOTIFY | uint32 LE, Pa | 30s |
| Altitude | 0x1832 | NOTIFY | int16 LE, m×10 | 30s |

## 0x1840 — Wellbeing

| Characteristic | UUID | Type | Encoding | Interval |
|---|---|---|---|---|
| GSR raw | 0x1841 | NOTIFY | uint16 LE, ADC counts | 10s |
| Stress index | 0x1842 | NOTIFY | uint8, 0–100 | 10s |
| EMG | 0x1843 | NOTIFY | uint16 LE, mV×10 | 10s |
| HRV (RMSSD) | 0x1844 | NOTIFY | uint16 LE, ms | 10s |

## 0x1850 — Device

| Characteristic | UUID | Type | Encoding | Interval |
|---|---|---|---|---|
| Battery level | 0x1851 | NOTIFY | uint8, % | on 5% change |
| Firmware version | 0x1852 | READ | UTF-8 string, semver | — |
| OTA trigger | 0x1853 | WRITE | uint8: write 0x01 to start OTA | — |
| Dog profile | 0x1854 | READ+WRITE | JSON UTF-8 bytes | — |

---

## Dart decode examples

```dart
// Heart rate (0x1801) — uint8
int heartRate = data[0];

// Body temperature (0x1803) — int16 LE, ×100
int raw = ByteData.sublistView(Uint8List.fromList(data))
    .getInt16(0, Endian.little);
double tempC = raw / 100.0;

// GPS latitude (0x1813) — int32 LE, ×1e6
int rawLat = ByteData.sublistView(Uint8List.fromList(data))
    .getInt32(0, Endian.little);
double lat = rawLat / 1e6;

// Bark class (0x1821) — uint8
const barkLabels = ['none','alert','play','anxiety','pain','boredom'];
String bark = barkLabels[data[0]];
```
