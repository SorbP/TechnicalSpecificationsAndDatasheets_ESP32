# Waveshare 3.5" Capacitive Touch LCD — ST7796S + FT6336U

- **Interface:** SPI (display) + I2C (touch controller)
- **Supply:** 3.3 V (use 3.3 V for direct ESP32 compatibility)
- **Resolution:** 320×480 px IPS, RGB 262K colors, controller ST7796S
- **Touch:** FT6336U capacitive, I2C address **0x38**
- **Size:** Module 61×92.44 mm, display area 48.96×73.44 mm

## Pin Assignment (ESP32)

| LCD Pin | ESP32 GPIO | Notes |
|---------|-----------|-------|
| VCC | 3.3 V | |
| GND | GND | |
| MISO | GPIO19 | Optional (reads only) |
| MOSI | GPIO23 | Shared with 2.4" LCD |
| SCLK | GPIO18 | Shared with 2.4" LCD |
| LCD_CS | GPIO27 | Shared SPI bus, different CS |
| LCD_DC | GPIO26 | |
| LCD_RST | GPIO25 | |
| LCD_BL | GPIO33 | PWM |
| TP_SDA | GPIO21 | Shared I2C bus |
| TP_SCL | GPIO22 | Shared I2C bus |
| TP_INT | GPIO13 | Active-LOW interrupt |
| TP_RST | GPIO32 | |

## Library & Code

Display: `TFT_eSPI` — configure `User_Setup.h`:
```cpp
#define ST7796_DRIVER
#define TFT_MOSI 23
#define TFT_SCLK 18
#define TFT_CS   27
#define TFT_DC   26
#define TFT_RST  25
```

Touch (FT6336U at 0x38 via I2C):
```cpp
#include <Wire.h>
Wire.begin(21, 22);
// Read touch (6 bytes from 0x03):
Wire.beginTransmission(0x38);
Wire.write(0x03);
Wire.endTransmission();
Wire.requestFrom(0x38, 6);
// Parse X/Y from bytes [1-4]
```
Or use `FT6X36` library.

## Power Draw

- Backlight: 80–120 mA at full brightness
- Controller + touch: ~10 mA
- Total: **~90–130 mA** — significant; use PWM dimming

## Gotchas

- FT6336U I2C address 0x38 **conflicts** with Adafruit STEMMA soil sensor address 0x38 — do not use soil sensor at 0x38 if this screen is connected; change soil sensor to 0x37 via AD0 jumper
- Max SPI clock for ST7796S: ~27 MHz (reliable); use lower for long cables
- TP_INT is edge-triggered FALLING — use `attachInterrupt(pin, isr, FALLING)` with `INPUT_PULLUP`
- Supply voltage and logic level must match — 3.3 V supply → 3.3 V logic throughout
- 3V3 pin on module sources ~200 mA max when VCC = 5 V
- SPI can be shared with 2.4" screen via separate CS pins — do not activate both simultaneously
