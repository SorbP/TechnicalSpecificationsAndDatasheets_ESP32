# Power System — Freenove Breakout Board CB9101

## Input

| Source | Connector | Voltage | Notes |
|--------|-----------|---------|-------|
| External DC | DC005 barrel jack | 7–12V (abs max 28V) | Recommended: 12V / 3A+ PSU |
| USB | Via ESP32 module | 5V | Powers module only; DC jack takes priority via Q1/Q2 |

## Voltage Rails

| Rail | Regulator | Input | Output | Max Current | Notes |
|------|-----------|-------|--------|-------------|-------|
| VCC-5V | XL1583 buck | VIN | ~5.19V | 3A continuous, 4.7A limit | Available at screw terminals |
| +3V3 | AMS1117-3.3V LDO | VCC-5V | 3.3V | **0.5A total** | All 3V3 headers draw from here |

## XL1583 Key Specs

| Parameter | Value |
|-----------|-------|
| Input range | 3.6–23V (abs max 28V) |
| Output (this board) | ≈5.19V (R11=2K, R12=6.49K) |
| Continuous current | 3A |
| Current limit | 4.7A |
| Switching freq | 380kHz |
| Efficiency (12V→5V/3A) | 89% |
| EN pin: float/HIGH → ON, LOW (<0.8V) → OFF | |

## Critical Power Budget

ESP32 WiFi/ESP-NOW TX peak = **379mA** (802.11b, 19.5dBm).  
AMS1117 on board = max **500mA** total.  
Headroom for sensors on 3.3V = **~120mA** maximum.  
DS18B20 draws ~1.5mA each → ~80 sensors possible in theory, but ESP-NOW transients will cause droop without isolation.

**Rule:** Power sensors from a separate 3.3V regulator fed by the 5V rail.

## Recommended Power Architecture

```
12V DC jack (min 3A)
    │
    └─ XL1583 ──→ VCC-5V / 3A  (screw terminals on board)
                      │
                      ├─ AMS1117 (onboard) ──→ 3.3V → ESP32 module ONLY
                      │                         (max 0.5A, covers WiFi peaks)
                      │
                      └─ External AMS1117-3.3V or LM2596 ──→ 3.3V / 1A+
                                                              → DS18B20s
                                                              → I2C sensors
                                                              → Other 3.3V loads
```

Add **100µF electrolytic + 100nF ceramic (X7R)** in parallel between 3.3V and GND, mounted close to ESP32 module, to buffer WiFi TX transients.

## Decoupling at Sensor Supply

Add 100µF + 100nF at the input of the external sensor regulator.  
Add 10µF + 100nF at the output, close to sensor cluster.
