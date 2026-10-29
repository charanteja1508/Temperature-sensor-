# 8051 Temperature Monitoring System (AT89C51)

A beginner-friendly embedded-systems project that reads an analog temperature sensor and displays the temperature on a 16×2 LCD using an AT89C51 microcontroller.

## Important hardware note

The AT89C51 has **no built-in ADC**. This project therefore uses an **LM35 temperature sensor + ADC0804 analog-to-digital converter + 16×2 LCD**. The firmware is written in 8051 C for Keil C51.

## Features
- Reads temperature from an LM35 sensor through ADC0804.
- Displays temperature in °C on a 16×2 character LCD.
- Uses a simple polling method for ADC conversion.
- Includes source code, wiring table, and a block diagram.

## Components
| Qty | Component | Purpose |
|---:|---|---|
| 1 | AT89C51 (40-pin DIP) | Microcontroller |
| 1 | ADC0804 | Analog-to-digital conversion |
| 1 | LM35 | Temperature sensor |
| 1 | 16×2 HD44780-compatible LCD | Display |
| 1 | 11.0592 MHz crystal | MCU clock |
| 2 | 33 pF capacitors | Crystal load capacitors |
| 1 | 10 kΩ resistor | RESET network / pull-up as wired |
| 1 | 10 kΩ potentiometer | LCD contrast |
| 1 | 10 kΩ potentiometer or resistor network | ADC0804 clock setup, if using RC clock |
| 1 | 10 kΩ resistor | ADC0804 CS/WR pull-up as needed |
| 1 | 150 pF capacitor | ADC0804 RC oscillator (typical starting value) |
| 1 | 10 µF capacitor | Reset timing |
| 1 | 100 nF capacitor | Supply decoupling |
| 1 | 5 V regulated supply | Power |
| — | Breadboard, jumpers | Prototyping |

Check each component's datasheet and the exact package pinout before wiring. Use a regulated 5 V supply and a common ground.

## Wiring

### AT89C51 power and clock
- Pin 40 (VCC) → +5 V; pin 20 (GND) → ground.
- Pin 31 (EA/VPP) → +5 V to use internal program memory.
- Crystal 11.0592 MHz between pins 18 (XTAL2) and 19 (XTAL1); one 33 pF capacitor from each crystal terminal to ground.
- Reset: pin 9 (RST) needs a power-on reset network. Typical circuit: 10 µF capacitor from +5 V to RST and 10 kΩ resistor from RST to ground; verify polarity and reset behavior for your board.

### LCD (4-bit mode)
| LCD pin | Connect to |
|---|---|
| 1 VSS | Ground |
| 2 VDD | +5 V |
| 3 V0 | Wiper of 10 kΩ contrast potentiometer (ends to +5 V and GND) |
| 4 RS | P2.0 (AT89C51 pin 21) |
| 5 RW | Ground |
| 6 E | P2.1 (pin 22) |
| 11 D4 | P2.4 (pin 25) |
| 12 D5 | P2.5 (pin 26) |
| 13 D6 | P2.6 (pin 27) |
| 14 D7 | P2.7 (pin 28) |
| 15 LED A | +5 V through a suitable current-limiting resistor if not already fitted |
| 16 LED K | Ground |
| LCD pins 7–10 | Not connected in 4-bit mode |

### ADC0804
| ADC0804 pin/signal | Connect to |
|---|---|
| VIN+ | LM35 output |
| VIN− | Ground |
| VCC | +5 V |
| AGND and DGND | Ground |
| D0–D7 | AT89C51 Port 1 pins P1.0–P1.7 (ADC D0 to P1.0, etc.) |
| CS | Ground (always enabled) |
| RD | P3.7 |
| WR | P3.6 |
| INTR | P3.2 |
| CLK IN / CLK R | Use the ADC0804 datasheet's recommended RC clock circuit; common starting point is 10 kΩ and 150 pF. Do not leave the clock unconnected. |

### LM35
- LM35 VS → +5 V, GND → ground, VOUT → ADC0804 VIN+.
- **Confirm the pinout for your exact LM35 package**; flat-face pin order can vary by manufacturer/package.
- Keep sensor and ADC grounds common.

## Measurement calculation

With ADC0804 configured for a 0–5 V input span, one 8-bit count is approximately 5/256 V = 19.53 mV. The LM35 produces approximately 10 mV/°C, so:

`Temperature (°C) ≈ ADC_count × 1.953`

This assumes Vref/2 is left at its default configuration (full-scale span near 5 V). If you change the ADC reference or analog input range, update the conversion factor.

## Build with Keil
1. Create a new Keil C51 project and select AT89C51.
2. Add `src/main.c` to the project.
3. Ensure the compiler uses 8051/C51 mode and build to create the HEX file.
4. Program the chip with a compatible programmer, or simulate the design in Proteus if you have the required models.
5. Confirm the actual ADC clock and conversion timing in the datasheet and on hardware.

## Repository layout
```text
8051-temperature-monitor/
├── README.md
├── LICENSE
├── .gitignore
├── src/
│   └── main.c
├── docs/
│   └── wiring.md
└── simulation/
    └── block-diagram.png
```

## Limitations / calibration
- This is a basic demonstration, not a precision instrument.
- ADC reference voltage, sensor tolerance, noise, and wiring affect accuracy.
- The program uses an approximate integer conversion and displays whole degrees Celsius.
- Test first with a current-limited 5 V supply. Never connect mains voltage to this circuit.

## License
MIT; see `LICENSE`.
