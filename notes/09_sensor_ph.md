# pH Sensor V2 — SEN0161-V2

- **Interface:** Analog voltage output (BNC electrode)
- **Supply:** 3.3–5 V
- **Signal:** 0–3.4 V analog → **ADC1 only** on ESP32

## Specs

- Range: 0–14 pH
- Operating temp: 0–80 °C
- Electrode zero point: pH 7 ± 0.5
- Internal resistance: <250 MΩ
- Response time: <2 min
- Repeatability: 0.017 mV noise

## Wiring

- Signal → GPIO35 or GPIO36 (ADC1, input-only)
- Electrode probe via BNC connector on board
- Reference filling solution: 3 mol/L KCl

## Calibration

Serial commands:
1. `enterph` → immerse in pH 4.0 buffer → `calph`
2. Rinse → immerse in pH 7.0 buffer → `calph`
3. `exitph`

Values stored in EEPROM. Recalibrate every 1–3 months.
New/stored electrode: soak in 3 mol/L KCl for **8 hours** before first use.

## Library & Code

Library: `DFRobot_PH` (depends on `DFRobot_EC`)

```cpp
#include "DFRobot_PH.h"
#include <EEPROM.h>
DFRobot_PH ph;
ph.begin();
// In loop:
float voltage = analogRead(35) / 4095.0 * 3300.0; // mV, 3.3V ref
float pH = ph.readPH(voltage, waterTemp);
ph.calibration(voltage, waterTemp); // serial-driven cal
```

## Power Draw

<5 mA (analog buffer only)

## Gotchas

- Official README marks ESP32 as "Work Wrong" — ESP32 ADC nonlinearity is the cause. Average 32+ samples + `analogSetAttenuation(ADC_11db)`. ADS1115 external ADC gives best results.
- `calibration()` blocks on `Serial.read()` — always call `Serial.begin()`
- Store electrode in 3 mol/L KCl or pH 7 buffer when not in use — never let it dry
- Do not immerse in strongly acidic chloride solutions long-term
- Remove rubber cap from liquid outlet before immersion
- Requires waterTemp input (DS18B20) for accurate temperature compensation
