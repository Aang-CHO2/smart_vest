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
