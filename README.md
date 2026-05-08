# Technical Specifications & Datasheets — ESP32 Autogrow Project

Teknisk referens för SorbP's ESP32-baserade autogrow/sensorsystem.  
Innehåller extraherad kunskap från alla datablad och manualer — optimerad för minimal token-åtgång vid recall.

## Snabbstart — Hur man använder det här repot

**I en ny Claude-session:** Ladda `notes/00_INDEX.md` för att se vad som finns, ladda sedan bara de filer som är relevanta för din fråga.

```
Ladda index:     notes/00_INDEX.md
Kraftsystem:     notes/02_power_system.md
GPIO-tabell:     notes/03_gpio_table.md
Sensor/aktuator: notes/05_autogrow_guide.md
Varningar:       notes/06_gotchas.md
```

## Systembeskrivning

| Komponent | Roll |
|-----------|------|
| Freenove Breakout Board (CB9101 V1.5) | Hub — 7–12V DC → 5V/3A → 3.3V → ESP32 + GPIO screw terminals |
| ESP32-WROVER-B | Huvudchip på hub — WiFi, ESP-NOW, dual-core 240MHz, 8MB PSRAM |
| ESP32-WROOM-32E | Alternativt chip (ingen PSRAM, fler GPIOs: 16/17 tillgängliga) |
| Fjärrnoder (ESP32) | Skickar sensordata via ESP-NOW utan router, 50–200m räckvidd |
| DS18B20 | Temperatursensorer, 1-Wire, upp till ~100m kabel |
| Kapacitiv jordfuktighetssensor | ADC1 analog (GPIO32–39, WiFi-säker) |
| BME280 / SHT31 | Temperatur + luftfuktighet, I2C (GPIO21/22) |
| MOSFET/relä (externt) | Pumpar, ventiler, lampor — styrs från GPIO screw terminals |

## Kritiska fakta (TL;DR)

- **3.3V-rälsen är begränsad till 0.5A totalt.** ESP32 tar upp till 379mA vid ESP-NOW TX. Mata sensorer från separat 3.3V-regulator kopplad till 5V-rälsen.
- **ADC2 fungerar inte under ESP-NOW.** Använd ADC1 (GPIO32–39) för alla analoga sensorer.
- **GPIO6–11 och GPIO16–17 (WROVER-B) får aldrig användas.** Flash- respektive PSRAM-korruption.
- **GPIO12 får inte ha extern pull-up på WROVER-B.** HIGH vid boot sätter flash-spänning till 1.8V och förstör chipet.
- **DS18B20 kräver extern 4.7kΩ pull-up.** Intern pull-up (~45kΩ) är för svag för 1-Wire.
- **I2C kräver externa 4.7kΩ pull-ups** på SDA och SCL — kortet har inga.

## Mappstruktur

```
notes/
  00_INDEX.md          Index — ladda det här först
  01_board_overview.md Kortet, ICs, LED-beteende
  02_power_system.md   Spänningsrälsar, strömbudget, rekommenderad arkitektur
  03_gpio_table.md     Tillgängliga GPIOs, begränsningar, rekommenderade pin-tilldelningar
  04_esp32_specs.md    Chip-specs, WiFi/ESP-NOW strömförbrukning, elektriska gränser
  05_autogrow_guide.md Sensorinkoppling, aktuatorer, ESP-NOW-arkitektur
  06_gotchas.md        Strapping-pins, destruktiva misstag, vanliga fallgropar

pinouts/
  CB9101_Board.png         Freenove breakout board
  ESP32_WROOM_Pinout.png   WROOM-32E pinout
  ESP32_Wrover_Pinout.png  WROVER-B pinout
  ESP32S3_Pinout.png       S3 pinout

datasheets/              Original PDF-datablad (lokal kopia, ej pushade)
```

## Vad Claude kan hjälpa med (baserat på den här hårdvaran)

1. ESP32 Arduino/ESP-IDF firmware — sensor-läsning, ESP-NOW hub/nod-kod, PWM, pulsmätning
2. DS18B20 1-Wire setup — DallasTemperature-bibliotek, flera sensorer på en buss, CRC-felhantering
3. ADC-kalibrering — ESP32 ADC är icke-linjär; kurvanpassning för jordfuktighetssensorer
4. Strömbudget — dimensionera extern PSU, sensorrägulator, batteritid för fjärrnoder
5. ESP-NOW-protokoll — pairing, unicast/broadcast, datastrukturer, felhantering
6. MOSFET-val — logic-level gate vid 3.3V, återledningsdiod för induktiva laster
7. Kretskortsdesign — koppling, avkopplingsplacering, ledningsdimensionering
8. Doseringslogik — EC/pH återkopplingsslingor, tidstyrda pumpimpulser
9. Dataloggning — LittleFS, SD-kort via SPI, MQTT-fallback via WiFi
10. Djupsömn och batteridrift — wakekällor, ULP-processor för ADC-sampling i sömn

## Repo

- GitHub: https://github.com/SorbP/TechnicalSpecificationsAndDatasheets_ESP32
- Ägare: SorbP
- Original datablad: `~/OneDrive/Backups/ESP32/Freenove_Breakout_Board_for_ESP32-main/`
