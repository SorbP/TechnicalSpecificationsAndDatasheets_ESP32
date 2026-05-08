# EC (Electrical Conductivity) Sensor — DFR0300 V2

- **Interface:** Analog voltage output
- **Supply:** 3.3–5 V
- **Signal:** 0–3.4 V analog (ratiometric) → **ADC1 only** on ESP32 (ADC2 blocked by ESP-NOW)

## Specs

- Range: 0–20 mS/cm (two bands: 0–2 mS/cm low, 2–20 mS/cm high)
- Accuracy: ±5 % (after calibration)
- Temp compensation: 0.0185/°C relative to 25 °C → DS18B20 reading required

## Wiring

- Signal → GPIO34 or GPIO35 (ADC1, input-only pins)
- 100 nF bypass cap on VCC pin
- Probe tip: >2 cm clearance from container walls

## Calibration

Serial commands:
1. `enterec` → immerse in 1413 µS/cm standard solution
2. `calec` → stores K-value to EEPROM address 0x0A
3. `exitec`

Two-point: repeat with 12.88 mS/cm solution. Recalibrate every 1–3 months.

## Library & Code

Library: `DFRobot_EC`

```cpp
#include "DFRobot_EC.h"
#include <EEPROM.h>
DFRobot_EC ec;
ec.begin();
// In loop:
float voltage = analogRead(34) / 4095.0 * 3300.0; // mV, 3.3V ref
float ecVal   = ec.readEC(voltage, waterTemp);      // mS/cm
ec.calibration(voltage, waterTemp); // serial-driven cal, check Serial Monitor
```

## Gotchas

- Library examples use 5V/10-bit formula — change to `/4095.0*3300.0` for ESP32 3.3V/12-bit
- ESP32 ADC is non-linear → use `analogSetAttenuation(ADC_11db)` + average 32+ samples, or use ADS1115 external ADC for precision work
- Temperature input is mandatory — wire DS18B20 in parallel and pass its reading to `readEC()`
- `calibration()` calls `Serial.read()` — always call `Serial.begin()` in `setup()`
