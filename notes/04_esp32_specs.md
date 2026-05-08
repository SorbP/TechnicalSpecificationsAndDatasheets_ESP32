# ESP32 Module & Chip Specs

## Module Comparison

| Parameter | ESP32-WROVER-B | ESP32-WROOM-32E |
|-----------|----------------|-----------------|
| Chip | ESP32-D0WD | ESP32-D0WD-V3 |
| Flash | 4MB | 4/8/16MB |
| PSRAM | 8MB | None |
| Supply voltage | 3.0–3.6V | 3.0–3.6V |
| Min PSU capability | 500mA | 500mA |
| Restricted GPIOs | 6–11, **16–17** | 6–11 |
| GPIO12 pull-up (R9) | NOT populated (important) | Populated |
| Operating temp | -40 to +85°C | -40 to +105°C (E2) |

## Chip Specs (ESP32-D0WD)

| Parameter | Value |
|-----------|-------|
| CPU | Xtensa LX6 dual-core, 240MHz |
| SRAM | 520KB |
| ROM | 448KB |
| GPIO total | 34 (5 strapping, 6 input-only) |
| ADC resolution | 12-bit, 18 channels (2 SAR ADCs) |
| DAC | 2 × 8-bit (GPIO25, GPIO26) |
| Capacitive touch | 10 channels |
| UART | 3 |
| I2C | 2 |
| SPI | 4 |
| LED PWM (LEDC) | 16 channels |
| Operating voltage | 2.3–3.6V (3.3V nominal) |
| Deep sleep current | 10µA |

## Current Consumption — WiFi / ESP-NOW

ESP-NOW uses the 802.11b radio.

| Mode | Average | Peak |
|------|---------|------|
| 802.11b TX, 1Mbps, 19.5dBm | 239mA | **379mA** |
| 802.11g TX, 54Mbps, 15dBm | 190mA | 276mA |
| 802.11n TX, MCS7, 13dBm | 183mA | 258mA |
| 802.11b/g/n RX | 112–118mA | 118mA |
| Modem sleep | ~15mA | — |
| Deep sleep | **10µA** | — |
| Off | 0.1µA | — |

## GPIO Electrical Limits

| Parameter | Value |
|-----------|-------|
| IO output current (source) | 40mA max per pin |
| IO input current (sink) | 28mA max per pin |
| All IO combined max | 1100mA (WROVER-B) |
| Internal pull-up/down | ~45kΩ |
| IO voltage range | 0 – 3.3V (not 5V tolerant) |

## Deep Sleep for Battery-Powered Sensor Nodes

- Deep sleep = 10µA → practical for coin cell / small LiPo nodes
- Wake sources: timer, touch, external GPIO (EXT0/EXT1)
- EXT0: single GPIO, only RTC domain pins (GPIO0, 2, 4, 12–15, 25–27, 32–39)
- EXT1: multiple GPIOs, logic AND/OR
- ULP co-processor can sample ADC during deep sleep without waking main CPUs
