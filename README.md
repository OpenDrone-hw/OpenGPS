# OpenGPS

A GPS board for the OpenDrone line. It is planned: there is no schematic, no
layout and no chosen GNSS module in this repository yet. The target is a small
board that a flight controller can read for position and return-to-home, and
that fits the OpenFrame 3" and 5" frames.

[![Status](https://img.shields.io/endpoint?url=https://opendrone.be/api/status/OpenGPS.json)](https://github.com/OpenDrone-hw/.github/blob/main/CONTRIBUTING.md#the-life-of-a-project)

## How to help

Nothing here is decided, so the useful contributions come first:

- Which GNSS module and interface (UART, I2C compass) suit small FPV builds.
- What size, mounting and cable position work on a 3" and a 5" frame.
- Prior art and its faults, as an issue.

Read [CONTRIBUTING.md](CONTRIBUTING.md) for how OpenDrone hardware is developed
and how a project moves from planned to alpha.

## Licence

Hardware licensed under [CERN-OHL-S-2.0](https://ohwr.org/cern_ohl_s_v2.txt),
see [LICENSE](LICENSE).
