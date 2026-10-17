# Overdrive Guitar Pedal

A through-hole guitar overdrive pedal designed in KiCad. Op-amp gain stage with diode soft-clipping, a tone control, a volume control, true-bypass switching with an indicator LED, and battery or DC power. Everything (pots, jacks, LED) mounts directly to the PCB, so the board sits behind the enclosure face with nothing but the footswitch wired by hand.

## Features

- Non-inverting TL072 gain stage with symmetric 1N4148 clipping
- Gain, Tone and Volume controls
- True bypass via a 3PDT footswitch, with an LED that lights only when the effect is engaged
- Powered by a 9V battery or a DC jack (the switched jack disconnects the battery when an adapter is plugged in)
- Battery power switches on automatically when a cable is plugged into the input jack (stereo jack ring wired to battery negative)
- Fully through-hole, hand-solderable

## Circuit overview

Signal path, left to right:

1. **Input**: R6 (10k) and C4 (22nF) AC-couple the guitar into the op-amp's non-inverting input. R8 (1M) to ground gives the input a discharge path.
2. **Bias**: R4/R3 (10k/10k) from the 9V rail make a 4.5V virtual ground, filtered by C3 (47uF). R5 (1M) feeds that reference into the op-amp input so the signal rides on 4.5V.
3. **Gain stage (U1A)**: RV1 (500k) in parallel with C6 (100pF) sets the feedback resistance against the R2 (1k) / C5 (220nF) ground leg. D1 and D2 (1N4148, antiparallel) across the feedback network clip the output.
4. **Tone**: RV2 (10k) with C1 (22nF) forms a variable low-pass after the gain stage.
5. **Output**: C7 (0.1uF) blocks the 4.5V DC offset, R7 (1M) pulls the node to ground, and RV3 (100k) sets output volume.

The unused op-amp half (U1B) is tied off, and C8 (0.1uF) decouples the TL072 supply.

## Controls

| Ref | Control | Value | Taper | Part |
|---|---|---|---|---|
| RV1 | Gain | 500k | Linear | Bourns PTV09A-4020U-B504 |
| RV2 | Tone | 10k | Linear | Bourns PTV09A-4020S-B103 |
| RV3 | Volume | 100k | Log (audio) | Bourns PTV09A-4020S-A104 |

## Bypass wiring

The footswitch is wired off-board to a 1x9 header (SW1). Each pole's common is the middle lug of its column:

| Pole | Common | Engaged | Bypass |
|---|---|---|---|
| A | IN | OV_IN (into effect) | jumper to pole B |
| B | OUT | OV_OUT (from effect) | jumper to pole A |
| C | LED cathode | GND | no connect |

IN is the guitar signal, OUT goes to the amp, and OV_IN / OV_OUT are the effect's input and output.

## Bill of materials

The full list is in [`BOM.csv`](BOM.csv). Key parts:

| Ref | Part | Notes |
|---|---|---|
| U1 | TL072 | DIP-8 (through-hole) package |
| D1, D2 | 1N4148 | DO-35 |
| D3 | 5mm red LED | |
| J4, J5 | Neutrik NMJ6HFD2 | Switched 1/4" stereo jacks (input and output) |
| J1 | Tensility 54-00130 | 5.5 x 2.5mm right-angle DC jack |
| SW1 | 3PDT stomp switch | Panel-mounted, wired to the SW1 header |
| RV1-RV3 | Bourns PTV09A | See Controls table |
| Passives | Axial 1/4W resistors, radial ceramic and electrolytic caps | All through-hole |

## Mechanical notes

- Pots, jacks and the LED are PCB-mounted, so the board layout and the enclosure drilling have to match exactly. Take hole positions from the PCB, not separately.
- The PCB mounts on standoffs with M3 screws (3.2mm clearance holes, non-plated).
- The footswitch mounts to the enclosure with its bushing and nut; only its 9 wires connect to the board.
- The enclosure is 3D printed. Use heat-set M3 inserts rather than printed threads for any screw that gets removed more than once.
- Enclosure size is driven by the artwork, so confirm dimensions before finalizing the board outline.

## Simulation

The overdrive stage can be simulated with KiCad's built-in ngspice simulator. Tips from the build:

- Simulate only the Overdrive and DC Bias sheets. Exclude connectors and the footswitch, which have no SPICE models.
- Replace the input with a VSIN source (for example 500mV amplitude, 1kHz) on the OV_IN net.
- Give the TL072 a SPICE model and check its pin assignments for every unit, including the power unit.
- Potentiometers simulate as fixed resistors, so set each wiper position manually.
- AC sweeps show frequency response only, not clipping. Use transient analysis for clipping.

## Repository contents

Adjust this list to match the repo.

- `UmarPedal.kicad_pro`, `UmarPedal.kicad_sch`, `UmarPedal.kicad_pcb`: KiCad project, schematic and board
- `BOM.csv`: bill of materials exported from KiCad
- Custom symbols and footprints for the Tensility 54-00130, Neutrik NMJ6HFD2 and Bourns PTV09A parts

Designed with KiCad 10.

## Known TODOs

- Confirm U1 is ordered as a DIP-8 part (for example TL072CP), not the SOIC variant.
- Make sure RV1, RV2 and RV3 carry their own values and footprints in the schematic so the BOM lists three distinct pots.
- Add the 1x9 header part that matches the SW1 footprint.
- Confirm the DC jack wiring and polarity against your power supply (the pedal standard is center-negative).
- Consider reverse-polarity protection on the power input.
- Check that the C6 part (rated 3kV) physically fits its footprint, or switch to a 50V 100pF ceramic.

## Build notes

1. Solder the low-profile parts first (resistors, diodes), then caps, the IC socket, and finally the pots, jacks and LED.
2. Wire the footswitch to the SW1 header following the bypass table above.
3. Test with a 9V supply before closing the enclosure. The 4.5V bias node should read about 4.5V.

## License

Add a license of your choice here.
