<!-- Keep this one-view brief at every project stage. Fill it from verified
     repository facts as the design develops; omit sections that do not yet
     apply instead of adding plans or placeholders.

     Keep the section order identical in every OpenDrone repo, so a reader and an
     agent find the same thing in the same place anywhere. Delete a section that
     does not apply rather than leaving it empty. Target 150 lines: if a section
     grows past a screen, the detail belongs in the schematic, not here. State
     current fact only. No plans, no TODOs, no history outside Revisions. -->

# <Board>

<What the board is, in three sentences at most. Topology and the load-bearing
ICs. What it is not, if a reader would otherwise assume it.>

## Architecture

<The signal and power chain, block by block, in prose. Roughly ten lines. Say
why, not just what: the parts of the design a reader could not infer from the
schematic. Sub-sheet names in backticks so a reader can open the right one.>

## Power

```
<ASCII tree: source, each regulator with its part and output, and what each
rail feeds. One block, no prose.>
```

## Key parts

| Function | Ref | Part | LCSC | Note |
|---|---|---|---|---|
| <MCU> | U1 | | | |
| | | | | |

## Connectors and I/O

| Connector | Ref | Part | Function |
|---|---|---|---|
| | | | |

<Pinout table or pin map, only where the pinout is not visible from the
schematic sheet name.>

## Layout rules

<Only constraints a future editor would break by accident: keep-outs, RF
clearances, thermal copper, differential pairs, antenna keepouts, current paths
that must stay short. Delete the section if the board has none.>

## Firmware

<Which firmware, which target, how it gets on the board the first time. Link
upstream. Do not restate upstream documentation.>

## Repo

| | |
|---|---|
| Maintainer | <GitHub team or handle> |
| Status | See the `status-*` topic on the repo. Never written here. |
| Designed in | KiCad 10 |
| KiCad project | `hardware/<name>.kicad_pro` |
| Root schematic | `hardware/<name>.kicad_sch` <plus sub-sheets, listed> |
| Board | `hardware/<name>.kicad_pcb`, <N> layers, <stackup> |
| Local library | `hardware/lib.kicad_sym`, `hardware/lib.pretty/`, `hardware/lib.3dshapes/`, nickname `lib` |
| Shared library | `hardware/KiCad-Library/`, submodule of [OpenDrone-hw/KiCad-Library](https://github.com/OpenDrone-hw/KiCad-Library), nickname `OpenDrone`; 3D models and exact component datasheets resolve through the project text variable `OPENDRONE_LIB` |
| Design rules | `hardware/<name>.kicad_dru`, canonical block plus <board-specific rules, or none> |
| Fab config | `hardware/fabrication-toolkit-options.json` |
| Board setup | Standard: 6 layers, 0.09 mm clearance and track, via 0.35 on 0.20 drill |
| License | CERN-OHL-S-2.0 |

<!-- Mechanical repos: replace the KiCad rows with the CAD tool -->

## Parts and datasheets

- **Per-repository part index:** the root schematic and its listed sub-sheets
  are authoritative for what this board fits. Export the netlist with the
  command below when a script-readable board index is needed; do not maintain
  a second hand-written BOM.
- **Proven shared parts:**
  `hardware/KiCad-Library/PARTS-USED.md` is the catalogue index. Its `Boards`
  column identifies every repository using each LCSC part; filter that column
  for `<Board>` to get this repository's proven shared-part view.
- **Exact datasheets:**
  `hardware/KiCad-Library/datasheet/manifest.json` maps shared symbols to the
  committed PDFs and their SHA-256 hashes. The PDFs live in
  `hardware/KiCad-Library/datasheet/`, and symbol links resolve there through
  `OPENDRONE_LIB`.
- **Local-only parts:** inspect `hardware/lib.kicad_sym`,
  `hardware/lib.pretty/`, and `hardware/lib.3dshapes/`, then verify supplier
  fields in the board schematic. Do not duplicate a shared part or datasheet.

## Environment

```sh
# schematic and board checks
kicad-cli sch erc hardware/<name>.kicad_sch
kicad-cli pcb drc --schematic-parity --refill-zones hardware/<name>.kicad_pcb

# netlist, for scripted analysis
kicad-cli sch export netlist --format kicadsexpr -o /tmp/<name>.net hardware/<name>.kicad_sch
```

On macOS `kicad-cli` is at
`/Applications/KiCad/KiCad.app/Contents/MacOS/kicad-cli`, and `pcbnew` imports
only under KiCad's bundled Python. Reusable scripts for renders, STEP export,
and packaging art come from Incutec hardware tooling. The OpenDrone release
standard is
[RELEASES.md](https://github.com/OpenDrone-hw/.github/blob/main/RELEASES.md).
Board-specific scripts, where a board has any, live in `hardware/tools/`.

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
| <rev1> | <YYYY-MM-DD> | First release. |
