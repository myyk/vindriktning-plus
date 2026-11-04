---
title: Wiring
---

[← Home](README.md) · [Parts](parts.md) · [Testing](testing.md) · **Wiring** · [Firmware](firmware.md)

# Wiring

> **Status: draft.** This combines the pinouts used by 3ative's 2026 ESP32-C3 build and other published VINDRIKTNING mods. Check your board's silkscreen before soldering. SuperMini clones occasionally move things.

For opening the case and the general soldering approach, follow 3ative's Part 1 video: [IKEA Air Quality ESPHome Convert](https://youtu.be/YmqtMTO5NVc). That video uses a Wemos D1 mini, so take the **pin numbers from this page** instead.

## Overview

```
 VINDRIKTNING PCB                ESP32-C3 SuperMini
 ───────────────                 ──────────────────
  +5V  ──────────────────────────  5V
  GND  ──────────────────────────  GND
  REST ───[10k]──┬───────────────  GPIO20   (UART RX, PM1006 data)
                 └─[20k]── GND              (divider optional, see below)

                                   3V3 ──┬──────┬──────┬──────┐
                                   GND ──┼──┬───┼──┬───┼──┬───┼──┐
                                 GPIO6 ──┼──┼─┬─┼──┼─┬─┼──┼─┐ │  │   SDA
                                 GPIO7 ──┼──┼─┼┬┼──┼─┼┬┼──┼─┼┐│  │   SCL
                                         │  │ │││  │ │││  │ │││  │
                                       SCD40   SHT41   SGP41   H2S (VCC/GND)
                                                                │
                                 GPIO3 ─────────────────────────┘ (A out)
```

## Pin table

| From | To SuperMini | Notes |
|---|---|---|
| VINDRIKTNING **+5V** pad | **5V** | Powers the ESP from the IKEA USB-C input. |
| VINDRIKTNING **GND** pad | **GND** | |
| VINDRIKTNING **REST** pad | **GPIO20** | PM1006 TX line. Same pin 3ative uses. |
| SCD40 VCC / SHT41 VCC / SGP41 VCC | **3V3** | Power **all I²C boards from 3.3 V** (reason below). |
| BME280 VCC (optional) | **3V3** | Same bus as the others. |
| All I²C GND | **GND** | |
| All I²C SDA | **GPIO6** | Shared bus. |
| All I²C SCL | **GPIO7** | Shared bus. |
| H₂S **VCC** | **3V3** | Keeps its output under 3.3 V. |
| H₂S **GND** | **GND** | |
| H₂S **A** (analog out) | **GPIO3** | ADC1 channel. Safe to use alongside Wi-Fi. |

**I²C addresses:** SHT4x `0x44`, SGP4x `0x59`, SCD4x `0x62`, BME280 `0x76` (or `0x77`). Nothing clashes, so all four share one bus. The optional BME280 wires exactly like the other I²C boards: 3V3, GND, SDA→GPIO6, SCL→GPIO7.

**Pins to avoid:** GPIO2, GPIO8 and GPIO9 are ESP32-C3 **strapping pins**. GPIO8 is also the blue LED and GPIO9 the BOOT button. Anything pulling on them at power-up can stop the board from booting. Some guides put the PM1006 on GPIO8; don't. **Spare pins** for later sensors: GPIO0, GPIO1, GPIO4 (analog-capable), plus GPIO5 and GPIO10.

## Why everything runs from 3.3 V

- Most cheap breakout boards pull SDA/SCL up to **their own VCC**. Power one from 5 V and you put 5 V on the ESP32-C3's I²C pins, which aren't 5 V-tolerant.
- The H₂S board's output can swing up to its supply voltage. At 3.3 V it can't exceed what the ADC pin can handle.
- The SuperMini's 3.3 V regulator is good for a few hundred mA. Peak load here is ESP Wi-Fi bursts (about 300 mA briefly), the SCD40's measurement pulses, the SGP41 heater (a few mA) and the H₂S heater (<20 mA). That's within budget, but use a decent **≥1 A** USB-C adapter.

## PM1006 signal level

The PM1006 talks at roughly 4.5–5 V logic, and the ESP32-C3 is a 3.3 V part. Plenty of people wire REST straight to the ESP and it works, partly because IKEA's board has series resistors on that line. A **10 kΩ / 20 kΩ divider** costs a few cents and brings the signal down to about 3 V:

```
REST ──[10k]──●── GPIO20
              │
            [20k]
              │
             GND
```

Solder the resistors inline in the wire and cover them with heat-shrink.

## Optional: fan state

3ative's YAML reads a "fan running" signal. The fan's low side sits near **5 V when the fan is off**, so it would need a divider like the one above, plus another pin (use **GPIO10**, not GPIO8). It isn't in this build's firmware. The PM readings already tell you when the unit is sampling.

## Placement inside the case

The case is small and warm, and the PM1006 and its fan take up most of it. Rules of thumb:

1. **Sensors go in the airflow.** The SCD40, SGP41 and H₂S want room air, so mount them against the inside of the **back grille**, where the fan pulls air through. Don't seal them in a dead pocket.
2. **Keep the SHT41 as far as possible from the ESP32 and the SCD40.** Both make heat, and every degree they add shows up as a "too warm, too dry" room. If you can, put the SHT41 right at the intake grille, or on a short lead just *outside* the case.
3. **The ESP32 goes low and away from the sensors.** The bottom of the case is a common spot.
4. **The BME280 can go anywhere.** Pressure isn't affected by heat or airflow, so tuck it in wherever there's room.
5. **Don't cover the SCD40's white membrane or the SGP41's opening** with tape or glue.
6. Use foam tape to keep boards from shorting against each other. Kapton tape on the back of the SuperMini helps.

## Flashing tip

On many SuperMini clones the **5V pin is tied straight to USB VBUS**. Don't plug the SuperMini's USB into your computer while the VINDRIKTNING is also powered, or the two supplies fight. Either:

- flash the ESP **before** installing it (first flash over USB, then OTA from then on), or
- unplug the VINDRIKTNING's own USB-C while the SuperMini is on your computer.

If the board won't enter flash mode, hold **BOOT**, tap **RESET**, release BOOT.

---

*Next: [Firmware →](firmware.md)*
