# Complete Pin Allocation — ESP32-WROVER-B Autogrow Hub

## Final Assignment Table

| GPIO | Function | Component | Notes |
|------|----------|-----------|-------|
| 18 | SPI SCLK | LCD (2.4" or 3.5") | Hardware SPI |
| 19 | SPI MISO | LCD 3.5" (optional) | |
| 21 | I2C SDA | SHT3x + STEMMA soil | External 4.7 kΩ pull-up; STEMMA has onboard 10 kΩ |
| 22 | I2C SCL | SHT3x + STEMMA soil | External 4.7 kΩ pull-up |
| 23 | SPI MOSI | LCD | Hardware SPI |
| 25 | DS18B20 DQ / LCD RST | Water temp sensor | 4.7 kΩ pull-up to 3.3 V; shared with LCD RST — reassign LCD RST if DS18B20 active |
| 26 | SPI DC | LCD | |
| 27 | SPI CS | LCD | |
| 32 | Touch RST | 3.5" LCD touch | |
| 33 | LCD BL (PWM) | LCD backlight | LEDC PWM |
| 13 | Touch INT / Flow pulse | 3.5" LCD FT6336U / Water flow | Active-LOW; level-shift flow sensor signal |
| 34 | EC sensor analog | DFR0300 | ADC1, input-only, no pull |
| 35 | pH sensor analog | SEN0161-V2 | ADC1, input-only, no pull |
| 36 | Spare ADC1 | — | Input-only |
| 39 | Spare ADC1 | — | Input-only |
| 4 | Actuator output | Relay/MOSFET pump 1 | |

**Note on GPIO25:** If DS18B20 and LCD are both used, assign LCD RST to a different free GPIO (e.g. GPIO4 if not used for actuator) and keep GPIO25 for DS18B20.

## I2C Bus Summary

| Address | Device |
|---------|--------|
| 0x44 | SHT3x (ADDR→GND) |
| 0x45 | SHT3x (ADDR→VDD, default DFRobot board) |
| 0x36 | STEMMA soil #1 |
| 0x37 | STEMMA soil #2 |
| **0x38** | **FT6336U touch (3.5" screen) — conflicts with STEMMA default** |
| 0x39 | STEMMA soil #4 |

**Resolution:** Change STEMMA soil sensors away from 0x38 via AD0 jumper if using 3.5" touch screen.

## SPI Bus Summary

Both LCD screens share MOSI/SCLK/MISO. Differentiated by CS pin. Never activate both CS simultaneously.

## Pull-Up Summary

| Line | Value | Note |
|------|-------|------|
| I2C SDA (GPIO21) | 4.7 kΩ to 3.3 V | External required; STEMMA adds 10 kΩ in parallel → ~3.2 kΩ combined |
| I2C SCL (GPIO22) | 4.7 kΩ to 3.3 V | Same |
| DS18B20 DQ (GPIO25) | 4.7 kΩ to 3.3 V | External required; reduce to 2.2–3.3 kΩ for cables >2 m |
| Flow sensor signal | Resistor divider | 10 kΩ + 20 kΩ → 1.67 V (level shift from 5 V) |

## Power Budget (3.3 V rail)

| Component | Typical | Peak |
|-----------|---------|------|
| ESP32-WROVER-B (ESP-NOW TX) | 239 mA | **379 mA** |
| SHT3x | <1 mA | 1.5 mA |
| STEMMA soil sensor | ~3 mA | ~5 mA |
| LCD 2.4" (BL 50%) | ~25 mA | 80 mA |
| LCD 3.5" (BL 50%) | ~55 mA | 120 mA |
| **Total (ESP-NOW + one LCD 50% BL)** | **~320 mA** | **~464 mA** |

**Warning:** One LCD at 50% backlight + ESP-NOW TX peak = ~464 mA → dangerously close to AMS1117 500 mA limit.
**Mitigation:** Reduce backlight further via PWM, or power LCD from separate 3.3 V regulator from 5 V rail (same as sensors).

## Sensors on Separate 3.3 V Regulator (from 5 V rail)

Move these to external AMS1117-3.3V fed from VCC-5V screw terminal:
- DS18B20(s)
- SHT3x
- STEMMA soil sensor(s)
- EC sensor board
- pH sensor board
- LCD backlight (if needed)

This leaves onboard AMS1117 exclusively for ESP32 chip — covers 379 mA peak comfortably within 500 mA limit.
