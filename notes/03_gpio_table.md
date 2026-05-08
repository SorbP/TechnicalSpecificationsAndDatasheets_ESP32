# GPIO Table — ESP32-WROVER-B on Freenove Breakout Board

## Restricted / Unavailable Pins

| GPIO | Reason | Action |
|------|--------|--------|
| 6–11 | Internal SPI flash | Never use |
| 16–17 | Internal PSRAM (WROVER-B) | Never use on WROVER-B |
| 34, 35, 36, 39 | Input-only, no internal pull | Input only; cannot pull up/down in HW |
| 0, 2, 5, 12, 15 | Strapping pins — see gotchas | Use with caution; see [06_gotchas.md](06_gotchas.md) |
| 1, 3 | UART0 TX/RX | Debug/programming; avoid for sensors |

## ADC During ESP-NOW

| ADC | Pins | Usable during ESP-NOW/WiFi |
|-----|------|---------------------------|
| ADC1 | GPIO32, 33, 34, 35, 36(VP), 39(VN) | **YES** |
| ADC2 | GPIO0, 2, 4, 12, 13, 14, 15, 25, 26, 27 | **NO** — conflicts with radio |

## Safe GPIO Pool (ESP32-WROVER-B) — Recommended for Autogrow

| GPIO | Dir | Key Alt Functions | Recommended Use |
|------|-----|-------------------|-----------------|
| GPIO13 | I/O | ADC2_CH4, TOUCH4 | DS18B20 (1-Wire) |
| GPIO21 | I/O | I2C SDA (default) | I2C sensors |
| GPIO22 | I/O | I2C SCL (default) | I2C sensors |
| GPIO23 | I/O | VSPI MOSI | Actuator output / SPI |
| GPIO25 | I/O | DAC1 | DAC analog out / actuator |
| GPIO26 | I/O | DAC2 | DAC analog out / actuator |
| GPIO27 | I/O | ADC2_CH7, TOUCH7 | DS18B20 (1-Wire) — best alternative |
| GPIO32 | I/O | ADC1_CH4 | Soil moisture analog (ADC1) |
| GPIO33 | I/O | ADC1_CH5 | Soil moisture analog (ADC1) |
| GPIO34 | Input | ADC1_CH6 | Analog sensor input (no pull possible) |
| GPIO35 | Input | ADC1_CH7 | Analog sensor input |
| GPIO36 (VP) | Input | ADC1_CH0 | Analog sensor input |
| GPIO39 (VN) | Input | ADC1_CH3 | Analog sensor input |
| GPIO4 | I/O | ADC2_CH0 | Digital sensor (not ADC during ESP-NOW) |
| GPIO14 | I/O | ADC2_CH6, MTMS | General I/O (strapping but safe if not driven at boot) |
| GPIO15 | I/O | ADC2_CH3, MTDO | Strapping pin — use with care |

## Recommended Pin Assignments for Autogrow

| Function | GPIO | Notes |
|----------|------|-------|
| DS18B20 bus 1 | GPIO13 | External 4.7kΩ pull-up to 3.3V mandatory |
| DS18B20 bus 2 (if needed) | GPIO27 | Same pull-up required |
| I2C SDA | GPIO21 | External 4.7kΩ pull-up to 3.3V |
| I2C SCL | GPIO22 | External 4.7kΩ pull-up to 3.3V |
| Soil moisture 1 | GPIO32 | ADC1, WiFi-safe |
| Soil moisture 2 | GPIO33 | ADC1, WiFi-safe |
| Soil moisture 3 | GPIO34 | ADC1, input-only |
| Soil moisture 4 | GPIO35 | ADC1, input-only |
| Light level (LDR) | GPIO36 | ADC1, input-only |
| Extra analog in | GPIO39 | ADC1, input-only |
| Pump / valve relay 1 | GPIO23 | Digital out |
| Pump / valve relay 2 | GPIO25 | Digital out (or DAC) |
| Pump / valve relay 3 | GPIO26 | Digital out (or DAC) |
| Fan PWM | GPIO4 | LEDC peripheral |
| UART2 RX (CO2 sensor) | GPIO32 | Reassign from ADC if needed |
| UART2 TX | GPIO33 | Reassign from ADC if needed |

Total usable GPIOs: ~20–24 after all restrictions.
