---
title: VINDRIKTNING+
permalink: /
---

**Home** · [Parts](parts.md) · [Testing](testing.md) · [Wiring](wiring.md) · [Firmware](firmware.md)

# VINDRIKTNING+

Turn an **IKEA VINDRIKTNING** PM2.5 sensor into a eight-reading Home Assistant air-quality node, using an **ESP32-C3 SuperMini** and three small Sensirion sensors plus an optional MEMS gas sensor. It all fits inside the original case.

| Reading | Sensor | Approx. cost |
|---|---|---|
| PM2.5 | Stock PM1006 (already inside) | — |
| CO₂ (real, NDIR) | Sensirion **SCD40** | $12–20 |
| Temperature / humidity | Sensirion **SHT41** | $2–4 |
| VOC index + NOx index | Sensirion **SGP41** | $4–8 |
| H₂S / sulfur odour (qualitative) | DFRobot Fermion MEMS **H₂S** | ~$7 |
| Barometric pressure (optional) | Bosch **BME280** | $2–4 |
| Brain + Wi-Fi | **ESP32-C3 SuperMini** | $2–4 |

**About US$30–45 per unit** on top of the VINDRIKTNING, with most parts from AliExpress. Details, alternatives and buying tips are on the [parts page](parts.md).

## Design goals

- **Good enough, cheap.** It's for a home: readings should be trustworthy for automations and trends, not lab work. Where a pricier part only improves accuracy you wouldn't notice, this build takes the cheaper one.
- **Keep the IKEA look and LED.** The ESP only *listens* to the particle sensor, so the stock controller keeps running.
- **Extendable.** One I²C bus and three spare analog pins leave room for more sensors.
- **ESPHome-native.** Every sensor uses a built-in ESPHome component, with no custom code.

## Pages

1. **[Parts list](parts.md)**: what to buy, where, and why, with cheaper and nicer options.
2. **[Part testing](testing.md)**: quick pass/fail checks for each part as it arrives, so duds go back before the return window closes.
3. **[Wiring](wiring.md)**: pin map, power, signal levels, placement inside the case.
4. **[Firmware](firmware.md)**: ESPHome config, calibration, burn-in, HA automation ideas.

## Credits and references

- [3ative/IKEA-Air-Quality-Sensor](https://github.com/3ative/IKEA-Air-Quality-Sensor), the original project. Its assembly video is [Part 1 – IKEA Air Quality ESPHome Convert](https://youtu.be/YmqtMTO5NVc).
- [ESPHome PM1006 component](https://esphome.io/components/sensor/pm1006/)
- [RevSpace VINDRIKTNING teardown](https://www.revspace.nl/VINDRIKTNING), with pad mapping and PM1006 notes.

---

*Prices checked October 2026.*
