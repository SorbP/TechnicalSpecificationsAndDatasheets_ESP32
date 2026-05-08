# Inköpslista — ESP32 Autogrow System

## Electrokit — Beställ direkt

| Art.nr | Produkt | Antal | Pris/st | Syfte |
|--------|---------|-------|---------|-------|
| **41017430** | ADC ADS1115, 4-kanal 16-bit I2C | 1 | 169 kr | EC + pH exakt spänningsläsning |
| **41017679** | Kondensator 100µF 16V radiell | 2 | — | ESP32 WiFi-buffert (redan beställd +1 extra) |

**Sök på Electrokit för resterande:**

| Sökterm | Antal | Syfte |
|---------|-------|-------|
| `100nF keramisk X7R` | 10 | HF-avkoppling: ESP32, ADS1115, AMS1117, sensorer |
| `AMS1117 3.3 SOT-223` | 2 | Extern 3.3V-regulator för sensorrälsa |
| `10µF 16V elektrolyt radiell` | 4 | AMS1117 in/ut-filter (2 per regulator) |
| `4,7 kΩ 1/4W` | 10 | I2C SDA/SCL pull-ups + DS18B20 1-Wire pull-up |
| `4,7 kΩ 1/4W` | — | (ingår ovan) |
| `10 kΩ 1/4W` | 10 | Spänningsdelare flödessensor (övre), I2C-test |
| `4,7 kΩ` eller `10 kΩ` | — | (ingår ovan) |

**Obs:** Köp ett motståndssortiment om du inte redan har ett — billigare och praktiskt. Sök: `motståndssortiment 1/4W`.

---

## Fullständig komponentlista med syfte

### Måste ha (systemproblem utan dessa)

| Komponent | Värde/Typ | Antal | Electrokit-sökterm | Syfte |
|-----------|-----------|-------|-------------------|-------|
| ADS1115 ADC-modul | 4-kanal, 16-bit, I2C | 1 | **41017430** | EC + pH mätning utan ADC-fel |
| Motstånd | 4.7kΩ, 1/4W | 5 | `4,7kΩ motstånd` | DS18B20 pull-up + I2C SDA + I2C SCL |
| Motstånd | 10kΩ, 1/4W | 3 | `10kΩ motstånd` | Spänningsdelare flödessensor (övre) |

### Starkt rekommenderat (stabilitet och isolering)

| Komponent | Värde/Typ | Antal | Electrokit-sökterm | Syfte |
|-----------|-----------|-------|-------------------|-------|
| LDO-regulator | AMS1117-3.3V, SOT-223 | 2 | `AMS1117 3.3` | Separat 3.3V-rälsa för sensorer |
| Kondensator | 100nF keramisk X7R | 10 | `100nF X7R keramisk` | HF-avkoppling vid varje sensor + chip |
| Kondensator | 10µF 16V elektrolyt | 4 | `10µF 16V radiell` | AMS1117 in/ut-filter |
| Kondensator | 100µF 16V elektrolyt | 1 | **41017679** | Extra ESP32-buffert (redan beställd) |

### Bra att ha

| Komponent | Värde/Typ | Antal | Sökterm | Syfte |
|-----------|-----------|-------|---------|-------|
| Kopplingsbräda | 400 hål | 1 | `kopplingsbräda breadboard` | Prototypning |
| Kopplingstråd | M-M + M-F set | 1 | `dupont kopplingskabel` | Anslutningar |
| Stripboard / mönsterkort | — | 1 | `stripboard veroboard` | Permanent montering av AMS1117-krets |
| JST-PH 4-pin kabel | 100–200 mm | 2–4 | `JST PH 4-pin` | STEMMA jordsensor-förlängning |

---

## Elektriska värden att verifiera vid inköp

- AMS1117-3.3V: SOT-223-kapsel (inte SOT-89 eller TO-252) — kontrollera paketsymbol
- Kondensatorer 100nF: X7R-dielektrikum (inte Y5V/Z5U — sämre temperaturstabilitet)
- Motstånd: 1/4W (0.25W) räcker för alla pull-up-tillämpningar i det här systemet

---

## Totalkostnad (uppskattning)

| Post | Kr |
|------|----|
| ADS1115 (Electrokit 41017430) | 169 |
| AMS1117-3.3V ×2 | ~20 |
| Kondensatorer (100nF ×10, 10µF ×4) | ~30 |
| Motstånd (sortiment eller styck) | ~20–50 |
| Kopplingsbräda + kablar (om saknas) | ~50–100 |
| **Total tillkommande** | **~290–370 kr** |

Allt utom ADS1115 kan beställas tillsammans eller köpas lokalt (Kjell & Company har motstånd och kondensatorer).
