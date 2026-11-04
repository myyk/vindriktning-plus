---
title: Part testing
---

[← Home](README.md) · [Parts](parts.md) · **Testing** · [Wiring](wiring.md) · [Firmware](firmware.md)

# Part testing

Parts from AliExpress arrive in dribs and drabs over a few weeks, and a dud is only easy to return while the order's buyer protection is still running. This page is a **quick bench test for each part as it arrives**: about 5–15 minutes each, on a breadboard, before anything gets soldered into a case.

## Before you start

**Don't confirm receipt on AliExpress until a part has passed.** Confirming early can shorten the time you have to open a dispute. Check the protection countdown on each order, and if a part fails, open the dispute while it's still running.

**Record the failure.** A short phone video of the ESPHome log or web page showing the bad reading makes disputes much easier. Sellers often refund without a return when there's a clear video.

### Bench kit (one-off)

| Item | Why |
|---|---|
| Solderless breadboard + male-male jumper wires | Plug the SuperMini and sensors in without committing |
| USB-C **data** cable | Many cheap cables are charge-only and won't show up on a computer |
| Soldering iron | Most modules ship with loose header pins. Solder headers on for testing; you can still wire them into the case later |
| Multimeter (optional) | Handy for checking 3.3 V / 5 V rails |
| A reference thermometer (optional) | Any trusted thermometer or existing HA temperature sensor |

### Bench firmware

Flash [`esphome/bench-test.yaml`](esphome/bench-test.yaml) to a SuperMini once. It's set up so that:

- it uses **the same pins as the real build** ([Wiring](wiring.md)), so whatever works on the breadboard works in the case;
- **missing sensors are fine**: plug in only what's arrived, and the rest just log "failed" and get skipped;
- readings appear in the **ESPHome log** and on a **web page** at `http://vplus-bench.local` (or the board's IP address), so you don't need Home Assistant;
- the **on-board LED blinks once a second**, so you can see it's running;
- nothing is calibrated or offset, so you see what each sensor reads out of the box.

To flash it, copy `secrets.yaml.example` to `secrets.yaml` next to it, then run `esphome run bench-test.yaml` with the board plugged in over USB. You can also paste the YAML into the ESPHome Device Builder in Home Assistant.

**Re-flash after adding a part?** No. Unplug USB, plug the new part in, plug USB back in. The I²C scan only runs at boot, which is why you power-cycle.

### Bench wiring

All I²C modules connect to the same four rows on the breadboard:

| Module pin | SuperMini pin |
|---|---|
| VCC / VIN | **3V3** (never 5V for these) |
| GND | **GND** |
| SDA | **GPIO6** |
| SCL | **GPIO7** |

The H₂S board is VCC→3V3, GND→GND, A→**GPIO3**. The VINDRIKTNING test has its own wiring, covered [below](#vindriktning).

### Reading the boot log

Right after boot, the log prints the I²C scan:

```
[i2c.idf] Results from bus scan:
[i2c.idf] Found device at address 0x44
[i2c.idf] Found device at address 0x59
[i2c.idf] Found device at address 0x62
```

| Address | Part |
|---|---|
| `0x44` | SHT41 |
| `0x59` | SGP41 / SGP40 |
| `0x62` | SCD40 |
| `0x76` or `0x77` | BME280 |

**If an address you expect is missing, that part fails the first check.** Before blaming the part, though: re-seat the jumpers, check SDA/SCL aren't swapped, and check the solder joints on the headers. Most "dead" modules are a bad header joint.

---

## Quick reference

| Part | Time | Pass if… |
|---|---|---|
| [ESP32-C3 SuperMini](#esp32-c3-supermini) | 5 min | Shows up over USB, flashes, LED blinks, joins Wi-Fi |
| [SHT41](#sht41) | 10 min | Within ~1 °C of a reference; RH jumps when you breathe on it |
| [SCD40](#scd40) | 15 min | ~420–480 ppm by a window; >1000 ppm when you breathe on it, then recovers |
| [SGP41](#sgp41) | 10 min | "Self-test complete" in log; VOC index jumps with hand sanitiser |
| [BME280](#bme280-optional) | 5 min | No "Wrong chip ID"; pressure matches today's weather, adjusted for your floor |
| [MEMS H₂S](#mems-h2s-sensor) | 15 min | Voltage settles after warm-up; rises near a boiled egg or alcohol |
| [VINDRIKTNING](#vindriktning) | 10 min | PM2.5 readings arrive about every 20–30 s; spike near a blown-out match |

---

## ESP32-C3 SuperMini

Test **every board in a multi-pack**, because quality varies between boards in the same batch.

1. **Shows up over USB.** Plug it into your computer. A new serial port should appear: `/dev/cu.usbmodem…` on macOS, `/dev/ttyACM…` on Linux, a COM port on Windows. No port? Try a different cable first, since charge-only cables are the usual culprit. Then try download mode: **hold BOOT, tap RESET, release BOOT**.
2. **Flashes.** Run `esphome run bench-test.yaml`. It should upload and reboot.
3. **Runs.** The blue LED blinks once a second.
4. **Wi-Fi is OK.** Open the web page and check **Wi-Fi Signal** with the board a few metres from your router.
   - Better than about −70 dBm is fine.
   - If it's much worse than your other devices, or it keeps dropping off, uncomment `output_power: 8.5dB` in the YAML and re-flash. That fixes most bad-antenna SuperMinis.
5. **Power rail (optional).** With a multimeter, the 3V3 pin should read 3.2–3.4 V.

**Return it if:** it never shows up over USB with a known-good data cable, won't flash in download mode, or can't hold Wi-Fi even at reduced power when other boards from the same pack can.

## SHT41

Also covers the SHT40 and SHT45.

1. **Scan:** `0x44` is present.
2. **Sanity:** after about 5 minutes the temperature should be within roughly **±1 °C** of your reference, and humidity within roughly **±5 %RH** of any other hygrometer you have. Keep it away from the SuperMini's regulator on the breadboard, which gets warm.
3. **Response:** breathe on it gently for a couple of seconds. Humidity should jump by 10–30 %RH and the temperature should tick up, then both should settle back within a minute or two.

**Return it if:** it's not on the bus, the readings are frozen or never change, or it's 2 °C+ off a trusted reference after 10 minutes away from heat sources.

## SCD40

The most expensive part, and the one most likely to be [B-grade or fake](parts.md) (see the SCD40 section), so it gets the most careful test.

1. **Scan:** `0x62` is present.
2. **Identity:** in the boot log look for a line like `Serial number 12.34.56`. A failure to read a serial number is a bad sign.
3. **Fresh-air check:**
   1. Put the breadboard **outdoors or right at an open window** for **5–10 minutes**. Keep your face away from it.
   2. **SCD40 CO2** should settle around **420–480 ppm**. Outdoor air is about 420 ppm, and readings near busy roads can be a little higher.
4. **Breath check:**
   1. Breathe on it once or twice from about 10 cm.
   2. It should shoot past **1000 ppm**, often several thousand, within one or two readings (the bench firmware reads every 5 s).
   3. It should fall back towards the fresh-air value within a few minutes.
5. **Overnight (optional):** leave it running in a closed bedroom. CO₂ should climb steadily through the night, often to 1000–2000 ppm, and drop once the door opens.

**Don't press any calibration button before this test.** Forced calibration would hide exactly the offset you're trying to detect. The bench firmware has none, on purpose.

**Return it if:** it reads more than about 150 ppm off in fresh air (e.g. 650+ ppm outdoors), barely reacts to breath, gets stuck on one value, or keeps resetting to 0.

## SGP41

Also covers the SGP40. Needs the SHT41 on the bus for compensation. Without it, the test still runs, with slightly worse numbers.

1. **Scan:** `0x59` is present.
2. **Self-test:** in the log, about a second after boot, look for **`Self-test complete`**. **`Self-test failed`** means a defective sensor. Return it.
3. **Settling:** leave it **5 minutes**. The VOC index starts near 0 and climbs to around **100**, which means normal air for this room. The NOx index sits near **1**.
4. **VOC response:**
   1. Put a drop of hand sanitiser or isopropyl alcohol on a tissue and hold it about 10 cm away.
   2. The VOC index should jump well above 100, often to 300–500, within 30 seconds.
   3. Take the tissue away; the index should decay over the next several minutes.
5. **NOx response (optional, SGP41 only):** striking a match near it, or running a gas stove nearby, should bump the NOx index above 1. A NOx index that never moves isn't by itself a reason to return the part, since clean homes really do sit at 1.

**Return it if:** it fails the self-test, isn't on the bus, or the VOC index shows no reaction to alcohol at all after the 5-minute settle.

## BME280 (optional)

1. **Scan:** `0x76` or `0x77` is present. If it's `0x77`, change `address:` in the YAML and re-flash.
2. **Chip check:** if the log says **`Wrong chip ID`**, you were sent a **BMP280** (no humidity sensor). That's a mislabelled item and grounds for a refund. If you'd rather keep it anyway, it still works for pressure if you use the `bmp280_i2c` platform.
3. **Pressure sanity:**
   1. Look up today's sea-level pressure for Singapore (e.g. Changi in any weather app). It's usually somewhere around 1005–1015 hPa.
   2. Subtract about **0.12 hPa per metre** you are above sea level. For example, a 10th-floor flat about 30 m up reads roughly 3.5 hPa lower.
   3. The sensor should be within about **±2 hPa** of that.
4. **Lift test (optional):** carry it up or down a few floors in the lift. Pressure should move by about 0.35 hPa per floor.
5. **Humidity:** a real BME280 reports humidity. It should roughly agree with the SHT41, within about 5 %RH.

**Return it if:** it isn't on the bus, it's a BMP280 sold as a BME280 (unless you choose to keep it), or pressure is way off (more than ~5 hPa) or frozen.

## MEMS H2S sensor

The DFRobot Fermion SEN0568. It's qualitative only, so you're testing that it **responds**, not its accuracy.

1. **Warm-up:** power it and watch **H2S Voltage**. It drifts for the first few minutes as the heater stabilises. When brand-new, it keeps drifting slowly for a day or two; that's normal burn-in.
2. **Baseline:** after about 10 minutes in normal room air it should sit at a steady, low-ish voltage, somewhere well below 3.3 V and above 0 V. Note the value.
3. **Response:**
   1. Hold a freshly peeled hard-boiled egg, or failing that a tissue with alcohol on it, about 5 cm away. MEMS gas sensors respond to alcohol too.
   2. The voltage should rise noticeably within 30–60 seconds.
   3. Move the egg or tissue away; the voltage should drift back down over a few minutes.

**Return it if:** the voltage sits at exactly 0 V or pegged at 3.3 V (check the wiring first), or it shows no response at all to either test after an hour of warm-up.

## VINDRIKTNING

Do this **before** soldering anything permanent. It checks both the IKEA unit and your REST-pad connection.

1. **Stock check:**
   1. Before opening it, power the VINDRIKTNING from a USB-C charger.
   2. The LED should cycle through its colours at start-up, then show green, amber or red.
   3. You should hear the small fan run every so often.
2. **Temporary wiring:** open the case and tack three wires onto the pads:
   - **+5V** → SuperMini **5V**
   - **GND** → **GND**
   - **REST** → **GPIO20**, through the [divider](wiring.md#pm1006-signal-level) if you're using one

   Power everything from the **VINDRIKTNING's** USB-C, not the SuperMini's ([why](wiring.md#flashing-tip)). The bench firmware is already on the board, so no USB to the computer is needed; watch the web page instead.
3. **Readings:** **PM2.5** should start updating about every 20–30 seconds. A clean room is typically single or low double digits.
4. **Response:**
   1. Blow out a match or light an incense stick near the back grille. Keep it safe and brief.
   2. PM2.5 should spike within a reading or two.
   3. The front LED should turn amber or red.

**If there are no readings,** in this order:

1. Re-check the REST joint.
2. Confirm REST really goes to GPIO20.
3. Swap to a different SuperMini.

A dead PM1006 is rare. If the IKEA LED itself never changes colour, though, the unit is faulty: that's a return to IKEA, not AliExpress.

---

## After everything passes

1. Confirm receipt on AliExpress.
2. Flash the real firmware ([`vindriktning-plus.yaml`](firmware.md)).
3. Solder it into the case per [Wiring](wiring.md).

The real firmware applies the temperature offset and calibration helpers that the bench firmware deliberately leaves out.

*Next: [Wiring →](wiring.md)*
