# Technical Specifications & Datasheets — ESP32 Project

Förvar för tekniska datablad, pinout-diagram och specifikationer kopplade till Stefan Perssons ESP32-baserade autogrow/sensorsystem.

## Projektöversikt

Systemet bygger på ESP32-mikrokontrollers för mätning och styrning i ett automatiserat odlingssystem (autogrow). Arkitekturen inkluderar:

- **ESP32-enheter** som kommunicerar via **ESP-NOW** (direkt chip-till-chip, utan router, 50–200m räckvidd)
- **DS18B20**-temperatursensorer på långa kablar (upp till ~100m)
- **Freenove ESP32 Wrover/WROOM breakout board** som huvudplattform
- Strömförsörjning via 3,3V, stabiliserad med bypass-kondensatorer (100µF + 100nF)

## Mappar

```
datasheets/     — Original PDF-datablad för alla komponenter
pinouts/        — Pinout-diagram och kortbeskrivningar
notes/          — Tekniska anteckningar, beräkningar, sammanfattningar
```

## Komponenter i projektet

| Komponent | Typ | Anteckningar |
|-----------|-----|--------------|
| ESP32-WROVER/WROOM | Mikrokontroller | WiFi + BT, 3,3V, 2,3–3,6V tolerans |
| Freenove Breakout Board | Breakout | Se `datasheets/` för PDF |
| DS18B20 | Temperatursensor | 1-Wire, klarar ~100m kabel |
| 100µF 16V radiell (Electrokit 41017679) | Kondensator | Buffrar WiFi-strömtoppar |
| 100nF keramisk (X7R föredras) | Kondensator | HF-avkoppling nära chip |

## Tekniska lärdomar (sammanfattning)

- **Spänningsfall**: Sikta på max 100–200mV kabellfall för mikroelektronik. ESP32 tolererar 2,3–3,6V → totalt 1V budget.
- **Stabilisering**: 100µF elektrolyt + 100nF keramisk parallellt mellan 3,3V och GND, monterade nära chipet.
- **Långa kablar**: DS18B20 klarar ~100m. Pull-up-motstånd (4,7kΩ) krävs på data-linjen.
- **ESP-NOW**: Kommunikation utan AP, låg latens, bra för sensornoder långt från routern.

## Användning med Claude Code

Det här repot är länkat som teknisk referens i Claude-sessionerna för autogrow-projektet. När datablad läggs till här uppdateras `notes/` med en sammanfattning av vad som är relevant för projektet.

### Lägg till ett datablad

1. Lägg PDF i `datasheets/`
2. Kör: `git add . && git commit -m "Add datasheet: <komponentnamn>" && git push`

Eller mata Claude med filen — sammanfattning skrivs automatiskt till `notes/`.

## Repo

- GitHub: https://github.com/SorbP/TechnicalSpecificationsAndDatasheets_ESP32
- Ägare: SorbP (Stefan Persson)
