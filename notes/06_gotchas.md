# Gotchas & Warnings

## Strapping Pins — Boot Critical

| GPIO | Internal Pull | Boot requirement | Risk if wrong |
|------|--------------|-----------------|---------------|
| GPIO0 | Pull-UP | HIGH = normal SPI boot | LOW = enters download mode, won't boot |
| GPIO2 | Pull-DOWN | LOW = fine | HIGH blocks download mode |
| GPIO5 | Pull-UP | HIGH = default SDIO timing | |
| GPIO12 (MTDI) | Pull-DOWN | **Must be LOW at boot** | HIGH sets VDD_SDIO to 1.8V → **destroys 3.3V flash** |
| GPIO15 (MTDO) | Pull-UP | HIGH = UART0 debug enabled | LOW silences boot messages |

**WROVER-B specific:** R9 (GPIO12 pull-up) is intentionally absent on this module. Internal pull-down holds GPIO12 LOW → VDD_SDIO stays at 3.3V. **Never add external pull-up to GPIO12 on WROVER-B.**

## Physical Assembly

- Inserting ESP32 module reversed or offset **will damage the board**. Check orientation against silkscreen before powering.
- Inner silkscreen labels = ESP32 pin names. Outer labels = ESP32S3.

## ADC2 + WiFi/ESP-NOW

ADC2 pins (GPIO0, 2, 4, 12–15, 25–27) **cannot be used for analog reads when WiFi or ESP-NOW is active.**  
Use ADC1 (GPIO32–39) for all analog sensors.

## 3.3V Rail Overload

AMS1117 on board: max 500mA total.  
ESP32 peaks at 379mA during ESP-NOW TX.  
Connecting more than ~120mA of sensors to the board's 3.3V rail will cause brownout resets.  
**Fix:** external AMS1117 or LM2596 from 5V rail for sensor power.

## DS18B20 Pull-Up

GPIO internal pull-up (~45kΩ) is **too weak for 1-Wire**. Always add external 4.7kΩ.  
For cables over 2m: try 3.3kΩ or 2.2kΩ to compensate cable capacitance.

## I2C Pull-Up

Board adds no pull-ups. Missing pull-ups → communication failure (often looks like "sensor not found").  
**Always add 4.7kΩ on SDA and SCL to 3.3V.**

## GPIO Not 5V Tolerant

ESP32 IO pins are 3.3V logic. Applying 5V to any IO pin will damage the chip.  
Level shifter required for 5V sensors or peripherals.

## GPIO34–39: No Pull, No Output

These pins are input-only in silicon. The `pinMode(pin, OUTPUT)` call will silently fail or behave unexpectedly. Cannot enable internal pull-up or pull-down — add external resistors if needed.

## GPIO16–17 (WROVER-B)

Connected to PSRAM chip select and clock inside the module.  
Using them as GPIOs will corrupt PSRAM and cause random crashes or NVS corruption.

## Floating GPIOs = LED Flicker

Any unconfigured (high-Z) GPIO will have its indicator LED flickering. Set all unused pins to `OUTPUT, LOW` or `INPUT_PULLDOWN` in `setup()`.

## CHIP_PU (EN) Pin

Board exposes EN as header. Pull below 0.6V to reset. Normally left open (internal pull-up active).  
Do not connect EN to logic outputs that float at startup.

## 5V Rail is 5.19V

XL1583 output is ~5.19V (from resistor divider R11/R12 values). Most 5V-rated components tolerate this. Verify sensitive peripherals rated exactly 5V before connecting.

## USB + DC Jack Simultaneously

Q2 (SI2306) automatically disconnects USB-5V when DC jack supplies power. Safe to have both connected.
