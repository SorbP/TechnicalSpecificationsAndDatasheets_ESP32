# DS18B20 — Waterproof Water Temperature Sensor — DFR0198

- **Interface:** 1-Wire (one GPIO + GND; optional separate VDD)
- **Supply:** 3.0–5.5 V external power — use 3.3 V on ESP32
- **Signal:** Open-drain DQ pulled to supply → 3.3 V logic, directly compatible with ESP32

## Specs

| Resolution | Conversion time | Accuracy |
|-----------|----------------|---------|
| 9-bit | 93.75 ms | ±0.5 °C |
| 10-bit | 187.5 ms | ±0.5 °C |
| 11-bit | 375 ms | ±0.5 °C |
| 12-bit (default) | 750 ms | ±0.5 °C (-10–85 °C), ±1 °C (-30–100 °C) |

Range: -55 to +125 °C. Long-term drift: ±0.2 °C. Unique 64-bit ROM ID (multidrop capable).

## Wiring

| Wire | Connect to |
|------|-----------|
| Red | 3.3 V |
| Black | GND |
| Yellow/White (DQ) | GPIO + **4.7 kΩ pull-up to 3.3 V** |

- Multiple sensors share one GPIO (multidrop on same wire)
- For cables >2 m: try 3.3 kΩ pull-up; >10 m: 2.2 kΩ
- **Do not use parasite power** with ESP32 3.3 V rail — use external VDD for reliable 750 ms conversion

## Library & Code

Libraries: `OneWire` + `DallasTemperature`

```cpp
#include <OneWire.h>
#include <DallasTemperature.h>
OneWire ow(25);                       // GPIO25, 4.7kΩ pull-up to 3.3V
DallasTemperature sensors(&ow);
sensors.begin();
sensors.setResolution(12);

sensors.requestTemperatures();
float t = sensors.getTempCByIndex(0); // or by 64-bit address for multidrop
if (t == DEVICE_DISCONNECTED_C) { /* handle error */ }
```

## Power Draw

| Mode | Current |
|------|---------|
| Standby | 750 nA–1 µA |
| Active conversion | 1–1.5 mA |

## Gotchas

- Default power-on temperature register = **85 °C** — always check for `== 85.0` and `DEVICE_DISCONNECTED_C` before using reading
- Reading too fast after `requestTemperatures()` without `setWaitForConversion(true)` returns stale 85 °C value
- Parasite power fails above 100 °C — use external VDD for water/nutrient sensing
- Multiple sensors: use `sensors.getDeviceAddress()` to enumerate 64-bit ROM IDs and address individually
- Pull-up to 3.3 V (not 5 V) — confirmed: sensor operates 3.0–5.5 V
