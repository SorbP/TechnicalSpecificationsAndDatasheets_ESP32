# Notes Index — ESP32 Autogrow Project

Load this file first. Each entry = one topic. Load only what you need.

| File | Load when you need to know about... |
|------|-------------------------------------|
| [01_board_overview.md](01_board_overview.md) | What the Freenove breakout board is, onboard ICs, connectors |
| [02_power_system.md](02_power_system.md) | Voltage rails, current limits, recommended power architecture |
| [03_gpio_table.md](03_gpio_table.md) | Which GPIOs are safe, restricted pins, ADC limitations, pin assignment for autogrow |
| [04_esp32_specs.md](04_esp32_specs.md) | ESP32 chip/module specs, WiFi/ESP-NOW current draw, electrical limits |
| [05_autogrow_guide.md](05_autogrow_guide.md) | Sensor wiring, actuator wiring, ESP-NOW architecture, recommended GPIO assignments |
| [06_gotchas.md](06_gotchas.md) | Boot-critical strapping pins, things that can destroy the board, common mistakes |
| [07_sensor_sht3x.md](07_sensor_sht3x.md) | SHT3x air temperature + humidity sensor (I2C) |
| [08_sensor_ec.md](08_sensor_ec.md) | EC (electrical conductivity) sensor DFR0300 (analog) |
| [09_sensor_ph.md](09_sensor_ph.md) | pH sensor SEN0161-V2 (analog, BNC electrode) |
| [10_sensor_soil_moisture.md](10_sensor_soil_moisture.md) | Adafruit STEMMA capacitive soil moisture + temp (I2C) |
| [11_sensor_water_flow.md](11_sensor_water_flow.md) | Gravity water flow sensor SEN0217 (pulse, 5V signal — level shift required) |
| [12_sensor_ds18b20.md](12_sensor_ds18b20.md) | DS18B20 waterproof water temperature (1-Wire) |
| [13_screen_24inch.md](13_screen_24inch.md) | Waveshare 2.4" LCD ILI9341 (SPI) |
| [14_screen_35inch_touch.md](14_screen_35inch_touch.md) | Waveshare 3.5" capacitive touch LCD ST7796S+FT6336U (SPI+I2C) |
| [15_pin_allocation.md](15_pin_allocation.md) | **Complete pin allocation table** — all components assigned, I2C conflicts, power budget |
| [16_solutions.md](16_solutions.md) | Lösningar: flow sensor level shift, I2C 0x38-konflikt, EC/pH ADC-noggrannhet |
| [17_shopping_list.md](17_shopping_list.md) | **Inköpslista** — alla komponenter med Electrokit art.nr, antal, syfte, totalkostnad |

## Project Summary (one paragraph)

SorbP builds an automated growing system (autogrow) using ESP32 microcontrollers.  
A **Freenove Breakout Board (CB9101 V1.5)** serves as the hub: it accepts a 7–12V DC supply, steps it down to 5V/3A (XL1583) and then 3.3V/0.5A (AMS1117), and breaks out all ESP32 GPIOs to screw terminals.  
Remote sensor nodes communicate via **ESP-NOW** (no router, 50–200m range, 802.11b radio).  
Sensors include **DS18B20** on long cables (1-Wire, needs 4.7kΩ pull-up).  
Actuators (pumps, valves, lights) are switched via external MOSFETs/relays wired to GPIO screw terminals.  
Critical constraint: the onboard 3.3V LDO is limited to 0.5A total — sensor power must come from a separate 3.3V regulator fed by the 5V rail.
