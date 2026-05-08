# Autogrow System Guide — Sensors, Actuators, Architecture

## System Architecture

```
[12V PSU]
    │
    └─ Freenove Breakout Board (hub ESP32-WROVER-B)
            │ VCC-5V screw terminals
            │
            ├─ Onboard AMS1117 → 3.3V → ESP32 chip only
            │
            ├─ External AMS1117 → 3.3V sensor rail
            │       ├─ DS18B20 sensor(s) (local)
            │       ├─ BME280 / SHT31 (I2C)
            │       └─ Soil moisture, LDR, etc.
            │
            └─ GPIO screw terminals → MOSFET/relay modules
                    ├─ Water pump(s)
                    ├─ Solenoid valves
                    ├─ Grow lights (PWM)
                    └─ Fans (PWM)

[Remote ESP32 nodes] ──ESP-NOW (802.11b, no router)──→ Hub
    ├─ DS18B20 on 100m cable (1-Wire)
    └─ Soil moisture, humidity, etc.
```

## Sensor Wiring

| Sensor | Interface | GPIO | Extra components | Notes |
|--------|-----------|------|-----------------|-------|
| DS18B20 temperature | 1-Wire | GPIO13 or GPIO27 | 4.7kΩ pull-up to 3.3V (mandatory) | One bus, multiple sensors (addresses) |
| DS18B20 on long cable (>2m) | 1-Wire | GPIO13 or GPIO27 | 3.3kΩ–2.2kΩ pull-up | Lower pull-up compensates cable capacitance |
| DS18B20 parasitic power | 1-Wire | GPIO13 or GPIO27 | 4.7kΩ + enable strong pull-up in code | Required for power-over-data on long runs |
| Capacitive soil moisture | ADC1 analog | GPIO32–35 | None | ADC1 only — works during ESP-NOW |
| BME280 / SHT31 / SHT40 | I2C | GPIO21 (SDA), GPIO22 (SCL) | 4.7kΩ pull-up each line to 3.3V | Board adds no pull-ups |
| DHT22 / AM2302 | Single-wire | GPIO13, 23, 27 | 10kΩ pull-up to 3.3V | |
| LDR light level | ADC1 analog | GPIO36 (VP) | Voltage divider (10kΩ + LDR) | Input-only pin, no pull needed |
| EC sensor (analog) | ADC1 analog | GPIO34, 35 | Signal conditioning circuit | 0–3.3V input range |
| CO2 sensor (UART) | UART2 | GPIO32 (RX), GPIO33 (TX) | — | Reassign UART2 in firmware; keep GPIO1/3 for debug |
| Flow meter (pulse count) | Digital | GPIO23 or GPIO27 | — | Use PCNT peripheral for accuracy |
| Float switch (water level) | Digital | GPIO4, 13, 27 | Pull-down or pull-up in firmware | Simple HIGH/LOW |

## Actuator Wiring

No GPIO-driven MOSFETs exist on the board. All actuators need external switching.

| Actuator | Switch | GPIO | Notes |
|----------|--------|------|-------|
| 12V water pump | N-ch MOSFET (IRLZ44N or similar) + flyback diode | GPIO23, 25, 26 | Gate via screw terminal; flyback across motor |
| Solenoid valve (12V) | N-ch MOSFET + flyback diode | Any safe I/O | Same as pump |
| 3.3V relay module | Direct GPIO | Any safe I/O | Most relay modules accept 3.3V trigger |
| 5V relay module | NPN transistor or level shifter | Any safe I/O | GPIO is 3.3V only |
| LED grow lights (PWM dim) | MOSFET + LEDC | Any safe I/O | 16 LEDC channels, 0–100% duty |
| Fan speed (PWM) | N-ch MOSFET + LEDC | GPIO4 | LEDC peripheral |
| Dosing pump | MOSFET or relay | Any safe I/O | Timed pulses (millisecond precision) |
| DAC analog out (0–3.3V) | Direct | GPIO25 or GPIO26 | 8-bit; drives analog dimmers, VFDs |

## ESP-NOW Node Configuration

| Parameter | Value / Recommendation |
|-----------|----------------------|
| Radio | 802.11b (automatic with ESP-NOW) |
| Range | 50–200m open air; less through walls |
| TX peak current | 379mA — size node PSU accordingly |
| Hub role | Receive from all nodes; control actuators |
| Node role | Deep sleep between readings; wake on timer |
| Node sleep current | 10µA (deep sleep) |
| Typical wake cycle | 60s interval: ~5ms TX + ~10ms overhead + sleep |
| Addressing | Each node has unique MAC address hardcoded in hub |
| Protocol | ESP-NOW v2, no router, fully local |

## Recommended GPIO Assignment (Final)

| GPIO | Function |
|------|----------|
| 13 | DS18B20 bus (1-Wire) — external 4.7kΩ pull-up |
| 21 | I2C SDA |
| 22 | I2C SCL |
| 23 | Relay/MOSFET output 1 |
| 25 | Relay/MOSFET output 2 |
| 26 | Relay/MOSFET output 3 |
| 27 | DS18B20 bus 2 or digital sensor |
| 32 | Soil moisture ADC (ADC1) |
| 33 | Soil moisture ADC (ADC1) |
| 34 | Analog input (ADC1, input-only) |
| 35 | Analog input (ADC1, input-only) |
| 36 | Light level LDR (ADC1, input-only) |
| 39 | Extra analog input (ADC1, input-only) |
| 4 | Fan PWM or digital sensor |

## What Claude Can Help With (Based on This Hardware)

1. **Arduino/ESP-IDF firmware** — sensor reading, ESP-NOW hub/node code, LEDC PWM, PCNT pulse counting
2. **1-Wire library config** — DallasTemperature library, multiple sensors on one bus, CRC error handling on long cables
3. **ADC calibration** — ESP32 ADC is non-linear; espressif provides characterization API; help with soil moisture curve fitting
4. **Power budget calculations** — sizing external PSU, sensor rail regulator, node battery life
5. **ESP-NOW protocol** — pairing, broadcast vs unicast, data structs, error handling
6. **MOSFET selection** — logic-level gate drive from 3.3V, flyback protection, inductive load switching
7. **PCB / stripboard layout** — routing, decoupling placement, wire gauge for 12V loads
8. **Enclosure / IP rating** — housing for outdoor/greenhouse sensor nodes
9. **Nutrient dosing logic** — EC/pH feedback loops, timed pump pulses
10. **Data logging** — SPIFFS/LittleFS on-chip, SD card via SPI, MQTT over WiFi fallback
