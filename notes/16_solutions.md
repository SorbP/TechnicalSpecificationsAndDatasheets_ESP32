# Lösningar på identifierade problem

## Problem 1 — SEN0217 vattenfödessensor: 5V-logik

**Orsak:** Sensorns signalpuls är >4.7V. ESP32 GPIO tål max 3.6V — direktkoppling förstör chipet.

**Lösning: Spänningsdelare (2 motstånd)**

```
Grön signal (5V) ──┬── 10kΩ ──── GPIO13
                   │
                  20kΩ
                   │
                  GND
```

Ger ~1.67V HIGH — säkert för ESP32, tillräckligt högt för logik HIGH (VIH = 0.75×3.3V = 2.47V... faktiskt för lågt för guaranteed HIGH).

**Bättre variant: 4.7kΩ + 10kΩ**

```
Grön signal (5V) ──┬── 4.7kΩ ──── GPIO13
                   │
                  10kΩ
                   │
                  GND
```

Ger: 5V × 10/(4.7+10) = 3.40V HIGH — precis under ESP32 max och tydligt HIGH.
Drar: 5/14.7kΩ = 0.34mA — försumbart.

**Kod:** Se [11_sensor_water_flow.md](11_sensor_water_flow.md) — ingen ändring behövs, bara koppla via delaren.

---

## Problem 2 — I2C-adresskollision 0x38

**Orsak:** FT6336U touchkontroller (3.5" skärm) är hårdkodad till 0x38. STEMMA jordsensor kan ställas till 0x38 via AD0/AD1-byglar.

**Lösning: Använd aldrig adress 0x38 för STEMMA-sensorer**

| STEMMA nr | Adress | Inställning |
|-----------|--------|-------------|
| 1 | 0x36 | default (ingen lödning) |
| 2 | 0x37 | AD0-bygel → VIN |
| 3 | 0x39 | AD1-bygel → VIN |
| 4+ | — | Se nedan |

**Om fler än 3 jordsensorer behövs:** Använd TCA9548A I2C-multiplexer (adress 0x70) — 8 kanaler, alla sensorer kan ha samma adress på separata kanaler. Sökterm: `TCA9548A I2C multiplexer`.

**Ingen extrakomponent behövs** om ≤3 jordsensorer används.

---

## Problem 3 — EC/pH ADC-noggrannhet

**Orsak:** ESP32 inbyggd ADC har offset-fel (~100mV) och icke-linjäritet. DFRobots bibliotek är kalibrerat för linjär 10-bit AVR/Arduino på 5V.

### Lösning A: Mjukvarukompensation (ingen extrahårdvara)

Byt ut `analogRead`-formeln i EC- och pH-koden:

```cpp
// setup():
analogSetAttenuation(ADC_11db); // 0–3.3V range

// Kalibrerad läsning med ESP-IDF characterization:
#include <esp_adc_cal.h>
esp_adc_cal_characteristics_t adc_chars;
esp_adc_cal_characterize(ADC_UNIT_1, ADC_ATTEN_DB_11,
                          ADC_WIDTH_BIT_12, 1100, &adc_chars);

// I loop — ersätter /4095.0*3300.0:
uint32_t raw = 0;
for (int i = 0; i < 64; i++) raw += analogRead(pin);
raw /= 64;
float voltage_mV = esp_adc_cal_raw_to_voltage(raw, &adc_chars);

float ecVal = ec.readEC(voltage_mV, waterTemp);
float pH    = ph.readPH(voltage_mV, waterTemp);
```

Noggrannhet: ~1–2%. Tillräckligt för autogrow-dosering och trendövervakning.

### Lösning B: ADS1115 extern ADC (rekommenderas)

16-bit I2C-ADC. Täcker EC + pH + 2 lediga kanaler på ett chip. Kringgår alla ESP32 ADC-problem.

**Koppling:**

```
ADS1115:
  VDD  → 3.3V (extern sensorreglator)
  GND  → GND
  SCL  → GPIO22
  SDA  → GPIO21
  ADDR → GND  → I2C-adress 0x48 (ingen konflikt)
  A0   → EC-sensor signalutgång
  A1   → pH-sensor signalutgång
  A2   → ledig
  A3   → ledig
```

**Kod:**

```cpp
#include <Adafruit_ADS1X15.h>
Adafruit_ADS1115 ads;

// setup():
ads.begin(0x48);
ads.setGain(GAIN_ONE); // ±4.096V, 0.125mV/bit

// loop():
float voltage_ec_mV = ads.readADC_SingleEnded(0) * 0.125;
float voltage_ph_mV = ads.readADC_SingleEnded(1) * 0.125;

float ecVal = ec.readEC(voltage_ec_mV, waterTemp);
float pH    = ph.readPH(voltage_ph_mV, waterTemp);
```

Library: `Adafruit ADS1X15` (Arduino Library Manager)

**Rekommendation:** Börja med Lösning A (noll kostnad). Uppgradera till Lösning B om kalibreringsdrift uppstår i praktiken. ADS1115-modulen (Electrokit 41017430) kan köpas nu och läggas i lådan tills den behövs.

---

## Sammanfattning

| Problem | Lösning | Extrahårdvara |
|---------|---------|---------------|
| Flow sensor 5V-signal | 4.7kΩ + 10kΩ spänningsdelare | 2 motstånd (~2kr) |
| I2C 0x38-kollision | Använd aldrig STEMMA-adress 0x38 | Ingen |
| EC/pH ADC-noggrannhet | Mjukvara (A) eller ADS1115 (B) | Ingen / 169kr |
