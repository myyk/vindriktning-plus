---
title: Parts list
---

[← Home](README.md) · **Parts** · [Testing](testing.md) · [Wiring](wiring.md) · [Firmware](firmware.md)

# Parts list

Prices are rough street prices as of **October 2026**, in USD unless marked S$. AliExpress prices change daily, so treat them as "what's normal" rather than exact quotes. Links go to **searches** where listings change often, and to specific product pages only for stores where the page stays put.

**Where to buy from Singapore**

- **AliExpress**: cheapest by a wide margin, 1–2 weeks to SG. Most of this build comes from here.
- **Shopee SG**: local resellers, mostly the same AliExpress modules marked up 30–100%. They arrive in 1–3 days, so use Shopee if you're impatient.
- **element14 SG**: genuine branded parts (DFRobot, Adafruit, Sensirion) with local GST pricing. This is the safe option for anything you don't want to gamble on.

---

## Quick summary

| # | Part | Pick | Typical price | Buy |
|---|------|------|---------------|-----|
| 1 | Base unit | IKEA VINDRIKTNING | (you have it) | [IKEA SG](https://www.ikea.com/sg/en/search/?q=vindriktning) |
| 2 | Microcontroller | ESP32-C3 SuperMini | $2–4 | [AliExpress](https://www.aliexpress.com/w/wholesale-esp32-c3-supermini.html) · [Shopee](https://shopee.sg/search?keyword=esp32%20c3%20super%20mini) |
| 3 | CO₂ | **SCD40** module | $10–20 | [AliExpress](https://www.aliexpress.com/w/wholesale-scd40-module.html) · [Shopee](https://shopee.sg/search?keyword=scd40) |
| 4 | Temp / RH | **SHT41** module (SHT45 optional) | $2–4 | [AliExpress](https://www.aliexpress.com/w/wholesale-sht41-module.html) · [Shopee](https://shopee.sg/search?keyword=sht41) |
| 5 | VOC (+NOx) | **SGP41** module (SGP40 if cheaper) | $5–10 | [AliExpress](https://www.aliexpress.com/w/wholesale-sgp41-module.html) · [Shopee](https://shopee.sg/search?keyword=sgp41) |
| 6 | H₂S / sulfur odour | DFRobot Fermion MEMS H₂S (SEN0568) | S$9.45 | [element14 SG](https://sg.element14.com/dfrobot/sen0568/breakout-kit-mems-h2s-sensor-uno/dp/4733258) |
| 7 | Pressure (optional) | **BME280** module | $2–4 | [AliExpress](https://www.aliexpress.com/w/wholesale-bme280-module.html) · [Shopee](https://shopee.sg/search?keyword=bme280) |
| 8 | Misc | wire, resistors, foam tape, heat-shrink | ~$3 | see [below](#8-small-stuff) |
| 9 | Power | 5 V USB-C adapter, ≥1 A | ~$3–5 | (any phone charger) |

**Total, excluding the VINDRIKTNING: about US$30–45 per unit** (add $2–4 for the optional BME280). The SCD40 accounts for roughly half of that.

### If you're building several

- ESP32-C3 SuperMinis come cheapest in 3-, 5- or 10-packs. Buy spares, since quality varies between batches.
- SHT41 modules are cheap enough to buy 5 or 10 at a time.
- Order all the SCD40s from one seller, so you can test one and return the batch if it's bad.

### Reference order (November 2026)

A real order for **three units** placed from Singapore on AliExpress, with one H₂S sensor to try first. "Paid" is the order total, which includes Singapore's 9% GST and any shipping.

| Part | Store | Variant chosen | Qty | Item price | Paid | Per unit, landed |
|---|---|---|---|---|---|---|
| SCD40 CO₂ module | Shenzhen Module Studio Co., Ltd Store | SCD40 Purple | 3 | S$12.05 | S$41.43 | **S$13.81** (≈US$10.40) |
| SGP41 VOC/NOx module | Shenzhen Module Studio Co., Ltd Store | SGP41-No Pins | 3 | S$11.95 | S$39.08 | **S$13.03** (≈US$9.80) |
| SHT41 temp/RH module | HZWDONE Store | SHT41 | 3 | S$2.15 | S$7.03 | **S$2.34** (≈US$1.75) |
| DFRobot Fermion MEMS H₂S (SEN0568) | DFRobot Store (official) | H2S | 1 | S$7.42 | S$8.09 | **S$8.09** (≈US$6.10) |
| 28 AWG silicone wire, 5 colours × 10 m | Beteno Connect Store | WHITE, 28AWG 5x10M | 1 | S$7.75 | S$8.45 | shared across all units |
| **Total (this order)** | | | | | **S$104.08** | |
| ESP32-C3 SuperMini, 20-pack *(bought earlier for another project)* | — | — | 20 | — | ¥440 (≈S$85) | **≈S$4.25** (¥22, ≈US$3.20) |

**Per sensor node:** about **S$33** for an ESP32-C3 + CO₂ + temp/RH + VOC/NOx, or **S$41** with the H₂S sensor, plus a share of the wire. The VINDRIKTNING isn't included.

The SuperMinis were a 20-pack bought in China for ¥440, shipping and taxes included. That's converted at about S$0.19 per yuan (late October 2026). Buying in bulk like this gets the per-board price down to around US$3, the low end of the usual range.

Notes from this order:

- **Choose the exact variant.** The SCD40 and SGP41 listings both bundle several boards (SCD40/SCD41, SGP40/SGP41) under one product. The search-page price is often a cheaper variant, not the one you want.
- **"No Pins" means you solder the headers or wires yourself.** That's fine here, since everything gets wired into the case anyway.
- **The SGP41 deliberately came from a mid-priced, well-reviewed seller.** Much cheaper "SGP41" listings (around S$2) are often an SGP40 or a reject chip; see [Part testing](testing.md#sgp41) for how to tell.
- Order IDs are left out. Status at time of writing: ordered, not yet tested. Results will be added once parts arrive.

---

## 1. IKEA VINDRIKTNING (base unit)

The case, fan and PM2.5 sensor (a Cubic PM1006) are all reused. IKEA's own controller stays in place, so the coloured front LED keeps working while the ESP32 listens to the sensor's serial output.

- [IKEA Singapore search](https://www.ikea.com/sg/en/search/?q=vindriktning)
- IKEA has started selling a newer smart sensor, **ALPSTUGA**, in some markets. If you want several VINDRIKTNINGs, it's worth buying them while they're still on the shelf.

---

## 2. ESP32-C3 SuperMini (microcontroller)

**Why this board:** It's tiny (about 22×18 mm), so it fits inside the case. It takes 5 V on a pin, has native USB-C, and has enough GPIO for UART, I²C and one analog input. ESPHome supports it out of the box, and it's what 3ative's 2026 update uses.

| Option | Price | Notes |
|---|---|---|
| **ESP32-C3 SuperMini** (pick) | $2–4 | [AliExpress](https://www.aliexpress.com/w/wholesale-esp32-c3-supermini.html) · [Shopee](https://shopee.sg/search?keyword=esp32%20c3%20super%20mini) |
| Seeed XIAO ESP32-C3 | ~$5–7 | Better antenna and build quality. Same chip, slightly different pinout. [Seeed](https://www.seeedstudio.com/catalogsearch/result/?q=xiao%20esp32c3) |

**Buying tips**

- Get the version **with headers not soldered**. You'll solder wires directly to it.
- Some SuperMini batches have weak Wi-Fi because of antenna or crystal placement. The firmware has a commented-out `output_power: 8.5dB` line that fixes most of them. If a board still drops off Wi-Fi, use a different board from the pack.
- Avoid the "SuperMini **Plus**" unless you want the external-antenna option. It works, but the pinout differs from the wiring page.

---

## 3. CO₂: Sensirion SCD40

**Why:** It's the only sensor here that measures *real* CO₂ (photoacoustic NDIR). The "eCO₂" from VOC sensors like the SGP30 or CCS811 is a guess based on VOCs, not a CO₂ measurement. The SCD40 is small (about 10×10 mm on a ~20 mm board), it's I²C at address `0x62`, and it's well supported in ESPHome (`scd4x`).

| Option | Price | Accuracy | Notes |
|---|---|---|---|
| **SCD40 module** (pick) | $10–20 | ±(50 ppm + 5%) in 400–2000 ppm | Plenty for a home: you care about 600 vs 1200 ppm, not 612 vs 618. |
| SCD41 module | $18–28 | Slightly tighter, specified to 5000 ppm, adds single-shot mode | Only worth it for battery builds. You're on USB power. |
| SCD30 (what 3ative used) | $25–40 | Better | Larger and pricier. No reason to pick it for a new build. |
| Senseair S8 / MH-Z19C | $15–25 | Good | Large NDIR blocks. They don't fit as easily in this case. |

**Fakes and seconds:** A genuine SCD40 chip costs around **US$18 at qty 1** from authorized distributors. Large Chinese module makers buy far cheaper in volume, so around US$10 for a whole module (as in the [reference order](#reference-order-november-2026)) is plausible. Modules much below that are often recycled, out-of-spec, or B-grade parts. At least one Dutch electronics forum has posted measurements showing fake SCD41s from AliExpress. For a home build where close enough is OK, a mid-priced AliExpress module from a well-reviewed seller is a reasonable gamble. To protect yourself:

1. Buy from a seller with a lot of orders and photos of the actual board.
2. When it arrives, check it **outdoors or by an open window**. It should read roughly 420–480 ppm after a few minutes. Breathe on it and it should jump past 1000 ppm, then come back down.
3. Do this within the AliExpress return window.

If you'd rather not gamble on one unit, Adafruit's SCD-40 and Sensirion's own boards are about 2× the price but genuine: [Adafruit search](https://www.adafruit.com/search?q=SCD-40), or search [element14 SG](https://sg.element14.com/search?st=scd40).

**Links:** [AliExpress](https://www.aliexpress.com/w/wholesale-scd40-module.html) · [Shopee](https://shopee.sg/search?keyword=scd40)

---

## 4. Temperature & humidity: Sensirion SHT41 (or SHT45)

**Why:** The SCD40 also reports temperature and humidity, but it heats itself, so those readings drift. A separate SHT4x gives you clean room T/RH. It's also the compensation input the SGP41 needs, and it costs almost nothing. It's I²C at `0x44`.

| Option | Price | Typical accuracy | Verdict |
|---|---|---|---|
| **SHT41** (pick) | $2–4 | ±0.2 °C, ±1.8 %RH typical; about ±2 %RH typical across the **full 0–100 %RH** range, ±3 % max | Usually only about $0.50 more than the SHT40, and it stays accurate at high humidity. Singapore indoor air often sits at 70–90 %RH, exactly where the SHT40 gets worse. |
| SHT40 | $1–3 | Same typical in the mid-range, but up to ±3 %RH typical / ±6 % max towards the extremes | Fine in drier climates. Not worth the saving here. |
| SHT45 | $4–8 | ±0.1 °C, ±1.0 %RH | Only worth it if you also mount it *outside* the case on a short cable. |
| AHT20 / BME280 | $1–3 | Worse (±1 °C, ±3 %RH) | Not as a replacement. The BME280 is added below for pressure only. |

**Buying tips**

- Many cheap boards are labelled "SHT40/41/45" and you pick the variant in the listing. Check which one actually ships.
- A board with a **PTFE membrane** over the chip is nice in a dusty case. It's optional.
- Get the **3.3 V-only** boards (no regulator) if you can find them. They're smaller and run cooler.

**Links:** [AliExpress SHT41](https://www.aliexpress.com/w/wholesale-sht41-module.html) · [AliExpress SHT45](https://www.aliexpress.com/w/wholesale-sht45-module.html) · [Shopee](https://shopee.sg/search?keyword=sht41)

---

## 5. VOC (+ NOx): Sensirion SGP41 (or SGP40)

**Why:** It gives a VOC Index (cooking, cleaning products, paint, people), plus a NOx Index on the SGP41 (gas stove, traffic air through windows). Both are 1–500 **indices** relative to what the sensor has learned is "normal" for the room, with 100 meaning normal. They're good for spotting events and driving automations, but they aren't ppb readings. It's I²C at `0x59`.

| Option | Price | Verdict |
|---|---|---|
| **SGP41** (pick) | $5–10 | VOC **and** NOx index for about the same money. Good sellers are around $9–10. Be wary of listings under ~$3. |
| SGP40 | $3–7 | VOC only. Fine if it's noticeably cheaper. The firmware handles either one with no changes. |
| SGP30 | $4–8 | **Skip.** It's being phased out, and the eCO₂ it reports is not CO₂. You have the SCD40 for that. |
| ENS160 (+AHT21 combo) | $3–6 | Works, but its readings jump around more than Sensirion's algorithm. Not much cheaper. |
| CCS811 (3ative's original) | $5–10 | Old, power-hungry, needs baseline babysitting. Skip. |

**Notes**

- The SGP4x needs temperature and humidity compensation to be accurate. That's why the SHT41 is in this build.
- Its gas-index algorithm runs at 1 Hz on the ESP itself, and ESPHome handles that automatically. Expect the index to settle after about **12–24 h** on first power-up.
- Its heater draws a few mA all the time. That's irrelevant on USB power.

**Links:** [AliExpress SGP41](https://www.aliexpress.com/w/wholesale-sgp41-module.html) · [AliExpress SGP40](https://www.aliexpress.com/w/wholesale-sgp40-module.html) · [Shopee](https://shopee.sg/search?keyword=sgp41)

---

## 6. H₂S / sulfur odour: DFRobot Fermion MEMS H₂S (SEN0568)

**Why this one:** It's the cheapest *MEMS* H₂S sensor sold on a breakout board, built around a Winsen **GM-602B**. It's tiny (13×13 mm), uses less than 20 mA, runs on 3.3–5 V, and has an analog output. That's one wire to an ADC pin, with no library needed.

| Option | Price | Verdict |
|---|---|---|
| **DFRobot SEN0568** (pick) | S$9.45 at element14 SG | Small, low-power, ready to wire. [element14 SG](https://sg.element14.com/dfrobot/sen0568/breakout-kit-mems-h2s-sensor-uno/dp/4733258) · [DFRobot wiki](https://wiki.dfrobot.com/sen0568/) |
| Bare Winsen GM-602B | ~$3–5 each (sold in 10-packs) | You'd have to build the heater and load circuit yourself. Not worth it for a few units. |
| MQ-136 module | $3–6 | Old-style heated sensor, about 150 mA at 5 V, large and hot. It would cook your temperature readings. Skip. |
| DFRobot Gravity electrochemical H₂S | $40+ | Factory calibrated in real ppm. Only worth it if you need actual numbers. |

**Set expectations here.** MEMS metal-oxide sensors are **qualitative**:

- They react to H₂S, but also to benzene and, to some degree, other reducing gases. Treat the reading as a "sulfur / sewer / rotten-egg smell detector", not a ppm meter.
- They need a **24–48 h burn-in** when new, plus a few minutes of warm-up after every power cycle.
- **Don't use it as a safety alarm.** DFRobot says the same.

**"Or similar" alternatives:** DFRobot sells this same Fermion MEMS breakout format for about ten other gases, including NH₃, CO, CH₄, HCHO, VOC, odour, smoke, ethanol, NO₂ and H₂. They all wire up the same way, with one analog pin each. If your actual concern is bathroom or bin smells, the **NH₃** or **odour** versions are worth considering alongside or instead of H₂S. The ESP32-C3 has spare ADC pins (GPIO0, 1, 4) for a second one. Browse them on [element14 SG](https://sg.element14.com/search?st=dfrobot%20fermion%20mems).

---

## 7. Pressure (optional): Bosch BME280

**Why it's optional:** The SHT41 is better at temperature and humidity, so this isn't a swap. What the BME280 adds is **barometric pressure**, for two things:

1. **SCD40 pressure compensation.** CO₂ sensors read slightly differently as air pressure changes. The firmware feeds the BME280's live reading into the SCD40 automatically. Near sea level in Singapore the correction is small, but it's free once the part is fitted.
2. **A pressure entity in Home Assistant**, for weather trends or just curiosity.

It's I²C at `0x76` (some boards use `0x77`), so it shares the bus with no clashes.

| Option | Price | Verdict |
|---|---|---|
| **BME280 module, 3.3 V version** (pick) | $2–4 | The common purple GY-BME280 boards. Pick the **3.3 V** variant (no regulator), which is smaller and has no 5 V pull-ups. |
| BMP280 | $1–2 | Pressure only, which is all we need here, but ESPHome uses a different platform (`bmp280_i2c`). If you buy this, change the platform line and delete the `humidity:` block. |
| BME680 / BME688 | $8–15 | Adds a gas sensor you don't need with the SGP41. Skip. |

**Watch for mislabels:** Many "BME280" listings ship a **BMP280**, which has no humidity sensor. The chip packages look similar: the BME280 is square and the BMP280 is slightly rectangular. If ESPHome logs a wrong chip ID error, that's what you got. It still works for pressure: switch the platform to `bmp280_i2c` and delete the `humidity:` block.

**If you skip it:** Delete the `bme280: !include optional-bme280.yaml` line in the firmware and everything else works unchanged.

**Links:** [AliExpress BME280](https://www.aliexpress.com/w/wholesale-bme280-3.3v-module.html) · [Shopee](https://shopee.sg/search?keyword=bme280)

---

## 8. Small stuff

| Item | Notes | Link |
|---|---|---|
| 28–30 AWG silicone wire, several colours | Flexible and easy to route inside the case | [AliExpress](https://www.aliexpress.com/w/wholesale-30awg-silicone-wire.html) |
| Resistors: 10 kΩ and 20 kΩ (or 22 kΩ) | Optional divider on the PM1006 line (see [wiring](wiring.md#pm1006-signal-level)) | [AliExpress resistor kit](https://www.aliexpress.com/w/wholesale-resistor-kit.html) |
| Double-sided foam tape | Mounting the modules and keeping them off the ESP | any hardware shop |
| Heat-shrink, small sizes | Insulating the divider and joints | [AliExpress](https://www.aliexpress.com/w/wholesale-heat-shrink-tube-kit.html) |
| Kapton tape (optional) | Insulating the back of the SuperMini | [AliExpress](https://www.aliexpress.com/w/wholesale-kapton-tape.html) |

**Power:** The VINDRIKTNING doesn't come with an adapter. Any 5 V USB-C phone charger rated **≥1 A** is fine. The SCD40's measurement pulses and Wi-Fi together draw more than the bare IKEA unit does.

---

## Cheaper or nicer variants

| Variant | Change | Saves / costs |
|---|---|---|
| **Budget** | Drop the SHT41 and use the SCD40's own T/RH, with an offset, for display and SGP compensation | −$3. Temperature will read a bit warmer and less steady. |
| **Budget+** | Drop the H₂S sensor | −$7. It's the least useful sensor for general air quality. |
| **No soldering on the sensors** | Pick SCD40/SHT41/SGP41 boards with **Qwiic/STEMMA-QT (JST-SH 4-pin)** connectors and daisy-chain them with JST-SH cables | +$3–8 total. Much tidier inside the case. |
| **"I don't want to build it"** | Off-the-shelf ESPHome air-quality units (e.g. Apollo AIR-1) | ~$80–150. Defeats the purpose, but it's there. |

---

*Next: [Part testing →](testing.md)*
