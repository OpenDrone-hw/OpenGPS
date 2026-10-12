# OpenGPS

OpenGPS is a compact GNSS and magnetometer board for the OpenDrone hardware line, designed for small FPV aircraft including the OpenFrame 3" and 5" platforms. The initial 25 x 25 mm PCB design combines a u-blox MAX-M10S receiver, passive ceramic patch antenna, and optional I2C compass. Position data is sent to the flight controller over UART; the magnetometer uses I2C.

[![Status](https://img.shields.io/endpoint?url=https://opendrone.be/api/status/OpenGPS.json)](https://github.com/OpenDrone-hw/.github/blob/main/CONTRIBUTING.md#the-life-of-a-project)

## Specifications

| Specification | Value |
|---|---|
| Board size | 25 x 25 mm |
| PCB | 4 layers, 1.6 mm FR-4 |
| GNSS receiver | u-blox MAX-M10S-00B |
| Antenna | Abracon APAE1575R1820ABDC1-T, passive 1575.42 MHz ceramic patch |
| Magnetometer | QST QMC5883P, optional I2C compass |
| Power input | 5 V nominal |
| Local supply | 3.3 V, TI TPS7A2033PDBVR low-noise LDO |
| External interface | 6-pin JST-SH: power, ground, UART, I2C |
| RF interconnect | 50 ohm grounded coplanar waveguide, L1 signal / L2 ground |
| PCB stackup | JLCPCB JLC04161H-7628 reference |

## Hardware design

The MAX-M10S receives GNSS signals from the sky-facing ceramic patch on the back of the PCB. A three-element RF matching footprint (C6-R3-C7) connects the antenna feed to RF_IN; the initial assembly uses a 0 ohm series resistor and leaves both shunt capacitors unpopulated.

A local TPS7A2033 regulator supplies 3.3 V to the GNSS receiver and QMC5883P magnetometer. The GPS UART and compass I2C bus are brought out through the six-pin connector. The compass is optional from the flight controller's perspective and should be assessed for interference once mounted on an aircraft.

The four-layer arrangement uses components and the RF trace on L1, a continuous reference ground on L2, power/utility routing on L3, and a ground plane with the ceramic patch on L4. The antenna was selected for published characterization on a 22 x 22 mm ground plane; performance in this finished assembly has not yet been measured.

## Connector pinout

J1 is a six-pin JST-SH connector (SM06B-SRSS-TBLFSN). Signal names below are from the **OpenGPS board's perspective**.

| Pin | Signal | Function |
|---|---|---|
| 1 | `+5V_IN` | Input power |
| 2 | `GND` | Ground |
| 3 | `UART_RX` | Data from flight controller TX to GNSS RXD |
| 4 | `UART_TX` | Data from GNSS TXD to flight controller RX |
| 5 | `I2C_SDA` | Magnetometer data |
| 6 | `I2C_SCL` | Magnetometer clock |

Check the flight controller's connector orientation and voltage supply before connecting a cable. UART TX and RX must cross between the two boards.

## Design files

The native KiCad 10 files live in [`hardware/`](hardware/):

- `OpenGPS.kicad_pro`, `OpenGPS.kicad_sch`, and `OpenGPS.kicad_pcb`
- `OpenGPS.kicad_dru` for project-specific design rules
- `lib.kicad_sym`, `lib.pretty/`, and `lib.3dshapes/` for project-local components
- `KiCad-Library/`, the OpenDrone shared library submodule

See [`AGENTS.md`](AGENTS.md) for the architecture, layout parameters, parts, and reproducible checks. Clone with submodules when working locally.

## Verification and contribution

The designer reported a clean PCB DRC after layout. The copied GitHub project still requires a recorded final ERC and DRC (including schematic/PCB parity), component sourcing review, and physical prototype validation. No antenna S11, acquisition, C/N0, compass interference, or flight-test results are claimed for this revision.

Contributions and design reviews are welcome, especially for antenna tuning, magnetometer placement, 3"/5" mechanical integration, and prototype measurements. Coordinate concurrent KiCad changes on [OpenDrone Discord](https://discord.gg/v3sWmTcx3R) and follow [CONTRIBUTING.md](CONTRIBUTING.md).

## Licence

Hardware licensed under [CERN-OHL-S-2.0](https://ohwr.org/cern_ohl_s_v2.txt); see [LICENSE](LICENSE).
