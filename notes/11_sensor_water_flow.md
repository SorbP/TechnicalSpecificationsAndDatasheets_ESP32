# Gravity Water Flow Sensor (1/2") — SEN0217

- **Interface:** Digital pulse output (Hall effect)
- **Supply:** 5–12 V (red wire) — **NOT 3.3 V compatible**
- **Signal:** Pulse HIGH >4.7 V at 5 V supply → **must level-shift to 3.3 V for ESP32**

## Specs

- Flow range: 1–30 L/min (accurate 2–30 L/min)
- Accuracy: ±5 %
- Formula: **1 L = 450 pulses**
- Duty cycle: 50 % ± 10 %
- Max working pressure: 1.75 MPa
- Operating current: 15 mA at 5 V

## Wiring

| Wire | Connect to |
|------|-----------|
| Red | 5V rail (VCC-5V screw terminal on breakout board) |
| Black | GND |
| Green (signal) | Level shifter → ESP32 GPIO |

**Level shift options:**
- Resistor divider: 10 kΩ (signal→GPIO) + 20 kΩ (GPIO→GND) → ~1.67 V high (safe)
- 74LVC1T45 level shifter (cleaner signal)

GPIO: any interrupt-capable pin from safe pool → GPIO13, GPIO25, GPIO26, GPIO27

## Code (no library needed)

```cpp
volatile uint32_t pulseCount = 0;
void IRAM_ATTR flowISR() { pulseCount++; }

attachInterrupt(digitalPinToInterrupt(13), flowISR, RISING);

// Every 1000 ms in loop:
float flowLperMin = (pulseCount / 450.0) * 60.0;
pulseCount = 0;
```

## Gotchas

- Direct connection to ESP32 GPIO **will damage the chip** over time (signal is 5 V, ESP32 max 3.6 V) — always level-shift
- Minimum accurate flow: 2 L/min; below 1 L/min output is erratic
- Sensor must be oriented with arrow matching flow direction
- Use `IRAM_ATTR` on ISR function — required on ESP32 for interrupt handlers
