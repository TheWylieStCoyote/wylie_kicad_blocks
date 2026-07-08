# wylie_kicad_blocks

Reusable circuit design elements: hierarchical schematic sheets and KiCad
design blocks. Consumed as a git submodule by projects created from
[wylie_kicad_project_template](https://github.com/TheWylieStCoyote/wylie_kicad_project_template).

## Contents

| Circuit | Design block | Sheet | Notes |
|---|---|---|---|
| `PowerIn_AMS1117-3.3` | ✓ | ✓ | VIN (4.75–15 V) → +3V3 @ 1 A; 10 µF in / 22 µF out (output cap required for stability). Uses `wylie:Reg_LDO_AMS1117-3.3`. Sheet variant exposes `VIN` as a hierarchical pin; +3V3/GND are global power symbols. |
| `Decoupling_100nF_x4` | ✓ | — | 4× 100 nF/0402 bank on +3V3. Standalone ERC reports "power pin not driven" by design — the rails are driven by the consuming circuit. Place each cap <2 mm from its IC pin. |

## Layout

```
sheets/                        Reusable hierarchical sheets (.kicad_sch)
blocks/wylie.kicad_blocks/     KiCad design-block library (schematic + optional layout snippets)
```

## Two reuse mechanisms — when to use which

### Hierarchical sheets (`sheets/`)

Full subcircuits meant to appear as a named sheet in a project's hierarchy
(a power supply stage, a USB front-end, an MCU core). **Workflow: copy, don't
reference.** Copy the `.kicad_sch` file into the consuming project, then add it
as a hierarchical sheet pointing at the copied file. Never point a sheet at a
file inside the submodule — you'd risk editing the submodule from a project and
pinning the project to submodule internals.

Sheet conventions: hierarchical pins for every external net, a text note block
stating design assumptions (input range, load, etc.), symbols only from
`wylie_kicad_lib` or KiCad's standard libraries so the copy stays portable.

### Design blocks (`blocks/wylie.kicad_blocks/`)

Smaller stamp-able fragments (a decoupling bank, a filter, a connector +
protection cluster), optionally with matching layout. KiCad copies block
contents on placement, so referencing the library inside the submodule is safe.
Template projects ship a `design-block-lib-table` pointing here as
`wylie_blocks`.

To add a block: open any project that has this submodule, select the circuitry
(or sheet), and use *Save as Design Block* into the `wylie_blocks` library —
then commit here and push.

Target KiCad version: 10.x.
