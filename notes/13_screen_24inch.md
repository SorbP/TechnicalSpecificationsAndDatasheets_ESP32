# Waveshare 2.4" LCD Module — ILI9341

- **Interface:** SPI (4-wire) + DC + RST + BL
- **Supply:** 3.3 V (use 3.3 V for logic compatibility with ESP32)
- **Resolution:** 240×320 px, RGB565, controller ILI9341
- **Size:** Module 70.5×43.3 mm, display area 36.72×48.96 mm

## Pin Assignment (ESP32)

| LCD Pin | ESP32 GPIO | Notes |
|---------|-----------|-------|
| VCC | 3.3 V | |
| GND | GND | |
| DIN (MOSI) | GPIO23 | Hardware SPI |
| CLK (SCLK) | GPIO18 | Hardware SPI |
| CS | GPIO27 | |
| DC | GPIO26 | |
| RST | GPIO25 | |
| BL | GPIO33 | PWM backlight control |

## Library & Code

Library: `TFT_eSPI` (recommended — DMA support, faster than Adafruit)

Configure `User_Setup.h`:
```cpp
#define ILI9341_DRIVER
#define TFT_MOSI 23
#define TFT_SCLK 18
#define TFT_CS   27
#define TFT_DC   26
#define TFT_RST  25
#define TFT_BL   33
```

Usage:
```cpp
#include <TFT_eSPI.h>
TFT_eSPI tft;
tft.init();
tft.setRotation(1);       // landscape
tft.fillScreen(TFT_BLACK);
tft.setTextColor(TFT_WHITE, TFT_BLACK);
tft.drawString("pH: 6.8", 10, 10, 4);
```

## Power Draw

- Backlight: 40–80 mA at full brightness (dominant power user)
- Controller: ~5 mA
- **Dim backlight via PWM on GPIO33 to save power and stay within 3.3 V rail budget**

## Gotchas

- RST requires ≥1 ms LOW at power-on before init
- BL floating = display stays dark — tie HIGH or drive via PWM
- SPI writes: up to 80 MHz; SPI reads: max 10 MHz — use `SPI_MODE0`
- SPI MISO can be shared with 3.5" screen — only one CS active at a time
