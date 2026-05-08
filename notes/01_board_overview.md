# Board Overview — Freenove Breakout Board for ESP32

**SKU:** FNK0091 | **PCB:** CB9101 V1.5

## What It Does

Converts ESP32 module pin headers → screw terminal blocks (KF128) + duplicate pin headers.  
Adds per-GPIO LED indicators and onboard DC power regulation.

## Compatible Modules (plug-in, interchangeable)

| Module | Flash | PSRAM | Restricted GPIOs |
|--------|-------|-------|-----------------|
| ESP32-WROVER-B | 4MB | 8MB | 6–11, **16–17** |
| ESP32-WROOM-32E | 4/8/16MB | None | 6–11 |
| ESP32S3 WROOM | — | — | See S3 silkscreen row |

Board silkscreen has two label rows: **inner = ESP32**, **outer = ESP32S3**.

## Onboard Components

| Ref | Part | Function |
|-----|------|----------|
| U1 | XL1583 | Buck DC-DC: VIN → 5V/3A |
| U2 | AMS1117-3.3V | LDO: 5V → 3.3V/0.5A |
| Q1 | SI2305 (P-ch) | VIN power path switch (not user-accessible) |
| Q2 | SI2306 (N-ch) | USB-5V selector (not user-accessible) |
| U3–U8 | 74HC04 ×6 | Schmitt inverter pairs → GPIO LED buffers (36 ch) |
| L1 | 47µH / 3A | XL1583 output inductor |
| D40 | 1N5822 / 3A | XL1583 freewheeling Schottky |
| C1, C3 | 220µF / 16V | XL1583 bulk filter caps |
| DC1 | DC005 | Barrel jack, 7–12V input |
| P1–P2 | 20-pin headers | ESP32 module socket |
| P3–P12 | KF128 screw terminals | GPIO access for sensors/actuators |
| P13–P16 | 4-pin headers | 3V3 and GND breakouts at board corners |

## GPIO LED Behavior

- LED ON = GPIO HIGH
- LED OFF = GPIO LOW
- LED flickering = GPIO floating (Schmitt trigger oscillates on noise) — **normal, not a fault**
- Fix: set all unused GPIOs to OUTPUT LOW or INPUT with pull in firmware

## 74HC04 Buffer (LED circuit)

- Double-inversion = non-inverting buffer per GPIO
- Input impedance ~45kΩ — negligible effect on ADC, I2C, strapping pins
- GPIO connects directly to buffer input; screw terminal connects directly to GPIO
- The buffer only drives the LED; sensor/actuator lines at the terminal see the raw GPIO
- At 3.3V: VIH ≥ 2.5V, VIL ≤ 0.825V
