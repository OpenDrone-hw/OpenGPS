<!-- Keep this one-view brief at every project stage. State verified design facts;
     keep section order consistent with other OpenDrone hardware repositories. -->

# OpenGPS

A 25 x 25 mm four-layer GNSS and optional compass board for small OpenDrone FPV aircraft.
The u-blox MAX-M10S-00B receives GNSS through a passive Abracon ceramic patch;
a QMC5883P reports magnetic measurements over I2C. The board is a peripheral,
not a flight controller.

## Architecture

- `OpenGPS.kicad_sch` is the single-sheet circuit source.
- E1, Abracon APAE1575R1820ABDC1-T, is an L1 (1575.42 MHz) passive patch mounted sky-facing on L4.
- E1 feeds U1 RF_IN through C6-R3-C7 matching footprints: C6/C7 DNP, R3 0 ohm initially.
- U1, MAX-M10S-00B, supplies position data to the external flight controller over UART.
- U2, QMC5883P, is a separate I2C magnetometer, with 2.7 kohm SDA/SCL pull-ups.
- U3, TPS7A2033PDBVR, converts the connector's nominal 5 V to local 3.3 V.
- U1 VCC and V_IO share 3.3 V and have separate local decoupling capacitors.
- R6/R7 are 0 ohm UART isolation/series footprint provisions.
- J1 is the six-pin power, ground, UART and I2C connection to the flight controller.
- Three test-point footprints are included in the schematic.

## Power

```text
J1 pin 1: +5V_IN
  -> U3 TPS7A2033PDBVR (3.3 V fixed LDO)
       -> 3V3_OUT -> U1 MAX-M10S VCC + V_IO
                   -> U2 QMC5883P VDD
                   -> R4/R5 I2C pull-ups
J1 pin 2: GND -> common board ground planes
```

## Key parts

| Function | Ref | Part | LCSC | Note |
|---|---|---|---|---|
| GNSS | U1 | u-blox MAX-M10S-00B | - | UART, passive-antenna RF input |
| Passive GNSS antenna | E1 | Abracon APAE1575R1820ABDC1-T | - | 1575.42 MHz; 22 x 22 mm characterization plane |
| Magnetometer | U2 | QST QMC5883P | - | I2C, address 0x2C; custom LGA-16 footprint |
| Low-noise LDO | U3 | TI TPS7A2033PDBVR | - | 5 V nominal input, fixed 3.3 V output |
| 6-pin connector | J1 | JST SM06B-SRSS-TBLFSN | - | UART + I2C + power |

LCSC codes are not recorded here because this copy of the schematic was not
checked for verified ordering fields; review Manufacturer, MPN and LCSC
properties in KiCad before procurement.

## Connectors and I/O

| Connector | Ref | Part | Function |
|---|---|---|---|
| 6-pin JST-SH | J1 | SM06B-SRSS-TBLFSN | FC power / UART / compass I2C |

| J1 pin | Board net | Direction from OpenGPS perspective |
|---|---|---|
| 1 | `+5V_IN` | 5 V nominal input |
| 2 | `GND` | Ground |
| 3 | `UART_RX` | In: FC TX to U1 RXD |
| 4 | `UART_TX` | Out: U1 TXD to FC RX |
| 5 | `I2C_SDA` | Bidirectional |
| 6 | `I2C_SCL` | Input clock from FC |

Do not infer a flight-controller mating pin order or cable orientation from
these net names alone; check the corresponding FC schematic.

## Layout rules

- Board: 25 x 25 mm, four layers, nominal 1.6 mm FR-4; JLC04161H-7628 reference stackup.
- L1: components, short RF line and local IC connections; L2: uninterrupted GND reference under RF.
- L3: utility power and digital interconnects; L4: antenna mounting and GND plane.
- RF_IN feed: 50 ohm grounded coplanar waveguide, **0.2695 mm L1 trace width** and **0.1524 mm L1 trace-to-GND gap**, based on the selected stackup (0.2104 mm L1-L2 dielectric).
- The antenna's plated through-hole feed is a separate RF transition. Ground-plane antipad clearance of 0.40 mm is an initial geometry, not a measured impedance match.
- Preserve the antenna ground reference and the direct C6-R3-C7-U1 RF path; avoid signal cuts in L2.
- Short, direct GND returns for C6/C7, U1 GND pins, and bypass capacitors. Tented GND vias may be placed under the U1 body or patch mounting area, away from solder pads and feed clearance.
- Keep high-current loops and magnetic hardware away from U2; its installed compass performance depends on airframe layout.
- Antenna on L4 is installed after SMD assembly using its feed pin and adhesive mounting method.

## Firmware

No separate flight-controller MCU or application firmware is programmed on
this PCB. MAX-M10S configuration is through its UART using the u-blox GNSS
protocol; QMC5883P is read over I2C by the external FC. Flight-control use
and sensor setup depend on the FC firmware, e.g. [Betaflight](https://github.com/betaflight/betaflight).

## Repo

| | |
|---|---|
| Initial design contributor | [JonTolden](https://github.com/JonTolden) |
| Board maintainer | Not designated in repository |
| Status | See the `status-*` topic on the repo. Never written here. |
| Designed in | KiCad 10 |
| KiCad project | `hardware/OpenGPS.kicad_pro` |
| Root schematic | `hardware/OpenGPS.kicad_sch` (single sheet) |
| Board | `hardware/OpenGPS.kicad_pcb`, 4 layers, JLC04161H-7628 |
| Local library | `hardware/lib.kicad_sym`, `hardware/lib.pretty/`, `hardware/lib.3dshapes/`, nickname `lib` |
| Shared library | `hardware/KiCad-Library/`, submodule of [OpenDrone-hw/KiCad-Library](https://github.com/OpenDrone-hw/KiCad-Library), nickname `OpenDrone`; 3D models and exact component datasheets resolve through `OPENDRONE_LIB` |
| Design rules | `hardware/OpenGPS.kicad_dru`, OpenDrone canonical custom-rule block |
| Fab config | `hardware/fabrication-toolkit-options.json` |
| Board setup | 4 layers, 1.6 mm; 0.1524 mm global minimum clearance and track; 0.50 mm / 0.20 mm nominal through-via |
| License | CERN-OHL-S-2.0 |

## Parts and datasheets

- **Per-repository part index:** the root schematic is authoritative for fitted parts. Export its netlist when a machine-readable part index is needed; do not maintain a second hand-written BOM.
- **Proven shared parts:** `hardware/KiCad-Library/PARTS-USED.md` indexes parts and manufactured boards. This initial untested design does not imply parts have been promoted to that catalogue.
- **Exact shared datasheets:** `hardware/KiCad-Library/datasheet/manifest.json` records committed PDFs and hashes for catalogue symbols using `OPENDRONE_LIB`.
- **Local-only parts:** inspect `hardware/lib.kicad_sym`, `hardware/lib.pretty/`, and `hardware/lib.3dshapes/`. Check each non-generic symbol's Manufacturer, MPN and LCSC fields against actual ordering records.

## Environment

```sh
# from repository root; check the GitHub copy after library migration
kicad-cli sch erc hardware/OpenGPS.kicad_sch
kicad-cli pcb drc --schematic-parity --refill-zones hardware/OpenGPS.kicad_pcb

# machine-readable schematic connectivity / parts
kicad-cli sch export netlist --format kicadsexpr -o OpenGPS.net hardware/OpenGPS.kicad_sch
```

On macOS `kicad-cli` is at
`/Applications/KiCad/KiCad.app/Contents/MacOS/kicad-cli`, and `pcbnew` imports
only under KiCad's bundled Python. Reusable rendering, STEP export and
release packaging follow OpenDrone's [RELEASES.md](https://github.com/OpenDrone-hw/.github/blob/main/RELEASES.md).

## Rules

Identical in every OpenDrone board repo. Do not edit here; edit the template.

- **Never text-edit** `.kicad_sch`, `.kicad_pcb` or `.kicad_dru`. Use KiCad, or
  kicad-skip / the pcbnew API for scripted changes. `.kicad_pro` is JSON and may
  be edited directly for metadata.
- **Metadata yes, connections no.** An agent may write BOM and documentation
  fields (MPN, Manufacturer, LCSC, Cost, Datasheet, text variables). An agent
  may not change nets, wiring, routing, placement, footprint assignment, or any
  value that changes the circuit.
- **Close KiCad before any write to a KiCad file.** KiCad caches library tables
  at process start and overwrites files on save.
- **Reuse before you draw.** Check the `OpenDrone` library and its
  `PARTS-USED.md` first. If the part is there we have already sourced,
  footprinted and shipped it, and its symbol links to the exact committed
  datasheet: place it from `OpenDrone`. Draw a new part into `lib` only when
  the catalogue has nothing that fits, imported with
  `easyeda2kicad` from its LCSC number. Pulling a newer catalogue is a
  deliberate, reviewed commit: `git submodule update --remote
  hardware/KiCad-Library`, then DRC.
- **One person holds a board layout at a time.** KiCad files do not merge. Say
  on Discord that you are taking it. See [CONTRIBUTING.md](CONTRIBUTING.md).
- **Run ERC and DRC before every pull request.** Existing approved findings
  may remain; a new type or increased count must be reviewed before merge.
  Commands are in Environment above.

## Revisions

| Rev | Date | Change |
|---|---|---|
| Rev A | 2026-10-12 | Initial 25 x 25 mm GPS + magnetometer PCB design; prototype not yet validated. |
