# SHT3x — Air Temperature & Humidity — SEN0330/SEN0331/SEN0333/SEN0334

- **Interface:** I2C (up to 1 MHz; 400 kHz fast mode typical)
- **Supply:** 2.15–5.5 V → use 3.3 V on ESP32
- **Signal:** Open-drain, matches VDD (3.3 V safe)

## Specs

| Variant | Temp accuracy | RH accuracy | Temp range | RH range |
|---------|--------------|-------------|------------|----------|
| SHT30 | ±0.2 °C (0–65 °C) | ±2 %RH (10–90 %RH) | -40–125 °C | 0–100 %RH |
| SHT31 | ±0.2 °C (0–90 °C) | ±2 %RH (0–100 %RH) | -40–125 °C | 0–100 %RH |
| SHT35 | ±0.1 °C (20–60 °C) | ±1.5 %RH (0–80 %RH) | -40–125 °C | 0–100 %RH |

Resolution: 0.01 °C / 0.01 %RH. Long-term drift: <0.03 °C/yr, <0.25 %RH/yr.
Measurement time: 2.5 ms (low rep.) / 12.5 ms (high rep.)

## Wiring

- SDA → GPIO21, SCL → GPIO22
- I2C address: **0x44** (ADDR→GND) / **0x45** (ADDR→VDD, default on DFRobot board)
- External 4.7 kΩ pull-ups on SDA and SCL to 3.3 V
- 100 nF decoupling cap on VDD close to chip

## Library & Code

Library: `DFRobot_SHT3x`

```cpp
#include <DFRobot_SHT3x.h>
DFRobot_SHT3x sht3x(&Wire, 0x45, 4); // addr, RST pin
Wire.begin(21, 22);
sht3x.begin();
sht3x.softReset();
float t  = sht3x.getTemperatureC();
float rh = sht3x.getHumidityRH();
```

## Power Draw

| Mode | Current |
|------|---------|
| Idle (single-shot) | 0.2 µA |
| Measuring | 600 µA typ, 1.5 mA max |
| Average (1/s, low rep.) | 1.7 µA |

## Gotchas

- Prolonged >80 %RH causes +3 %RH offset — recoverable after return to normal
- Do not use built-in heater unless fighting condensation — skews readings
- ALERT pin stays HIGH if status register not cleared → call `clearStatusRegister()` after reset
- Factory calibrated, no user calibration needed
