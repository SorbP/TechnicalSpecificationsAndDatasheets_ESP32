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

## Project Summary (one paragraph)

SorbP builds an automated growing system (autogrow) using ESP32 microcontrollers.  
A **Freenove Breakout Board (CB9101 V1.5)** serves as the hub: it accepts a 7–12V DC supply, steps it down to 5V/3A (XL1583) and then 3.3V/0.5A (AMS1117), and breaks out all ESP32 GPIOs to screw terminals.  
Remote sensor nodes communicate via **ESP-NOW** (no router, 50–200m range, 802.11b radio).  
Sensors include **DS18B20** on long cables (1-Wire, needs 4.7kΩ pull-up).  
Actuators (pumps, valves, lights) are switched via external MOSFETs/relays wired to GPIO screw terminals.  
Critical constraint: the onboard 3.3V LDO is limited to 0.5A total — sensor power must come from a separate 3.3V regulator fed by the 5V rail.
