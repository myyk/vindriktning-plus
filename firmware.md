---
title: Firmware
---

[← Home](README.md) · [Parts](parts.md) · [Testing](testing.md) · [Wiring](wiring.md) · **Firmware**

# Firmware (ESPHome)

**Config:** [`esphome/vindriktning-plus.yaml`](esphome/vindriktning-plus.yaml)
**Secrets template:** [`esphome/secrets.yaml.example`](esphome/secrets.yaml.example)
**Optional add-on:** [`esphome/optional-bme280.yaml`](esphome/optional-bme280.yaml) (pressure, pulled in by the `packages:` block)

> Passes `esphome config` on **ESPHome 2026.10.1** with no warnings, both with and without the BME280 package. It has not been compiled or run on hardware yet.

## What changed from 3ative's version

3ative's repo was updated for the ESP32-C3 in **February 2026** ([`Ikea_v2_esp32_upgrade.yaml`](https://github.com/3ative/IKEA-Air-Quality-Sensor/blob/main/Ikea_v2_esp32_upgrade.yaml)), so it's less dated than the videos. It uses an SCD30 + BME280. This config keeps its structure and swaps the sensors:

| 3ative v2 | This build | Why |
|---|---|---|
| SCD30 (CO₂/T/RH) | **SCD40** (`scd4x`) | Smaller and cheaper. |
| BME280 (T/RH/pressure) | **SHT41** (`sht4x`) for T/RH, plus an **optional BME280** for pressure only | The SHT41 is better at T/RH. The BME280's pressure also feeds the SCD40 (`ambient_pressure_compensation_source`). |
| — | **SGP41** (`sgp4x`) VOC + NOx index | New, compensated by the SHT41. |
| — | **MEMS H₂S** on `adc` | New. Raw voltage plus a "relative to clean air" ratio. |
| Arduino framework | **ESP-IDF** framework | ESPHome's recommended framework for the C3. |
| Fan binary sensor on GPIO8 | removed | GPIO8 is a strapping pin, and the signal needs level shifting anyway. |
| CO₂ calibration button (SCD30) | kept, ported to `scd4x.perform_forced_calibration` | |
| — | API + OTA **encryption keys** | Current ESPHome best practice. |

Also updated for current ESPHome: the SGP4x outputs are now `voc_index` / `nox_index`. The old `voc` / `nox` names are deprecated and will be removed in 2027.2.0.

## First-time setup

1. Copy `secrets.yaml.example` to `secrets.yaml` and fill it in. Generate the two keys with `openssl rand -base64 32`.
2. Change `name` / `friendly_name` at the top of the YAML for each unit (`aq-bedroom`, `aq-kitchen`, …).
3. With the SuperMini **not** in the VINDRIKTNING, plug it into your computer and run `esphome run vindriktning-plus.yaml`, or use the ESPHome Device Builder add-on in Home Assistant. Install it, then let OTA handle future updates.
4. In the logs, the I²C scan should list **0x44, 0x59, 0x62** (plus **0x76** or **0x77** with the BME280; change `address:` in `optional-bme280.yaml` if yours is 0x77). If one is missing, check that board's wiring before anything else.

## Calibration and burn-in

| Sensor | What to do |
|---|---|
| **SCD40 CO₂** | Automatic self-calibration is **on**. It assumes the room sees fresh air (~420 ppm) at least once every week or so, which a normal ventilated room does. For a quick start, put the unit by an open window for about 5 min, then press **CO2 Force Calibration** in HA (the reference defaults to 420 ppm). |
| **SHT41 temperature** | The case gets warm. Leave the unit next to a trusted thermometer for a day, then adjust the `offset: -1.0` filter under `sht4x`. Humidity is affected too: warmer air reads drier. If RH reads low compared with a reference, reducing the heat source (moving the SHT41) is better than offsetting it. |
| **SGP41 VOC/NOx** | Nothing to do. The index learns its baseline over the first **~12–24 h**. Readings before that are not meaningful. |
| **H₂S** | Let it **burn in for 24–48 h** when new. Then, with the room smelling normal, press **H2S Set Baseline Now**. From then on, **H2S Relative** reads about 1.0 in clean air and rises with sulfur-type smells. Treat it as an event detector, not a ppm meter. |

## Useful Home Assistant ideas

- **Ventilation reminder:** CO₂ > 1000 ppm for 10 min → notification or turn on a fan.
- **Air purifier control:** PM2.5 drives fan speed. 3ative's [`Automation_Master.yaml`](https://github.com/3ative/IKEA-Air-Quality-Sensor/blob/main/Automation_Master.yaml) has a ready-made pattern for IKEA's air filter.
- **Cooking detector:** VOC index > 250 or NOx index > 50 → kitchen hood on.
- **Smell alert:** H₂S Relative > 1.5 for 5 min → check the drains or bin.

## Docs

- ESPHome: [PM1006](https://esphome.io/components/sensor/pm1006/) · [BME280](https://esphome.io/components/sensor/bme280/) · [SCD4x](https://esphome.io/components/sensor/scd4x/) · [SHT4x](https://esphome.io/components/sensor/sht4x/) · [SGP4x](https://esphome.io/components/sensor/sgp4x/) · [ADC](https://esphome.io/components/sensor/adc/)
- [DFRobot SEN0568 wiki](https://wiki.dfrobot.com/sen0568/)
- [3ative/IKEA-Air-Quality-Sensor](https://github.com/3ative/IKEA-Air-Quality-Sensor)
