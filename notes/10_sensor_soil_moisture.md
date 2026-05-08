# Adafruit STEMMA Soil Sensor — Capacitive Moisture + Temperature — #4026

- **Interface:** I2C
- **Supply:** 3.3–5 V (logic follows VIN)
- **Signal:** 3.3 V logic when VIN = 3.3 V → directly compatible with ESP32

## Specs

- Moisture output: ~200 (very dry) to ~2000 (very wet); typical soil ~300–500 dry, ~1000+ wet
- Temperature: ATSAMD10 die sensor, ±2 °C
- No absolute units — relative capacitive reading; characterize per soil type
- I2C addresses: **0x36** (default), 0x37, 0x38, 0x39 (via AD0/AD1 jumpers)
- Up to 4 sensors on same I2C bus with different addresses

## Wiring

- JST-PH 4-pin: GND, VIN (3.3V), SDA (GPIO21), SCL (GPIO22)
- **Board has onboard 10 kΩ pull-ups** — no external pull-ups needed
- If sharing bus with SHT3x (4.7 kΩ external): combined ~3.2 kΩ — acceptable
- Only the probe prong goes into soil — do not submerge PCB

## Library & Code

Library: `Adafruit_seesaw`

```cpp
#include "Adafruit_seesaw.h"
Adafruit_seesaw ss;
Wire.begin(21, 22);
ss.begin(0x36);
uint16_t moisture = ss.moisture_read();
float temp        = ss.getTemp();
```

## Gotchas

- Chip is ATSAMD10 running seesaw firmware — can be reflashed if needed
- With 3 sensors (each 10 kΩ pull-up): effective pull-up ~3.3 kΩ — still fine
- Address 0x38 conflicts with FT6336U touch controller on 3.5" Waveshare screen — do not use soil sensor address 0x38 if using that screen
- Calibration not possible — characterize dry/wet thresholds empirically per soil type
