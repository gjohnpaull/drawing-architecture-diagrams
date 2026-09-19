# drawing-architecture-diagrams

A [Claude Code](https://claude.com/claude-code) skill for producing **professional, print-ready architecture
diagrams** (Azure, AWS, GCP, network topology, hub-and-spoke, landing zones, private endpoints) as editable
**draw.io** files plus PNG and PDF.

![Sample: fictional event-driven order platform on A4](docs/sample-a4.png)

The agent writes the diagram as a short Python script on top of a small design system, then iterates
**lint → render → look → fix** until the page is clean. Every rearrangement is a cheap re-run, and the result
has consistent typography (font 10 at print size), colour, spacing and connector routing.

## What's inside

| Path | Purpose |
|---|---|
| `SKILL.md` | When to use the skill, the workflow, layout bands, design rules, patterns and common mistakes |
| `scripts/archdiagram.py` | Design-system library: pages (A5/A4/A3), containers, subnets, panels, legend, numbered flows, observations, title block, edges; plus `lint` and `render` |
| `examples/event_driven_platform_a4.py` | Worked example (fictional Northwind Traders): event-driven AKS + Service Bus order platform on one A4 page |
| `references/icons.md` | Verified draw.io icon paths and how to find and verify new ones |
| `tests/selftest.py` | Checks that the example lints clean and that every lint rule fires |

**Lint** flags overlapping labels, labels outside the frame, titles covered by icons, icons that render as plain
boxes, oversized icons, ragged label baselines, cells hidden behind filled boxes, and wasted space (empty boxes,
sparse boxes, one-sided gaps, empty areas and empty bands). It is necessary, not sufficient: the skill always
requires viewing the rendered PNG.

## Install

```bash
git clone https://github.com/gjohnpaull/drawing-architecture-diagrams ~/.claude/skills/drawing-architecture-diagrams
```

Requirements: Python 3.9+ and [draw.io desktop](https://github.com/jgraph/drawio-desktop/releases) (used for
PNG/PDF export; set `DRAWIO=<path>` if it is not in a standard location).

## Use

Ask Claude Code for a diagram, for example:

> Create an A4 architecture diagram of this Azure subscription.
> Redraw this architecture so the private endpoints sit in a grid under their services.
> Fit this diagram on A4 at font size 10 with no wasted space.

Or use the library directly:

```python
from archdiagram import Diagram, lint_file, render
d = Diagram("A4L")
d.frame_title("Contoso - Production", "Web platform · West Europe", chip="PRODUCTION")
# ... containers, icons, panels, edges ...
d.save("out.drawio")
print(lint_file("out.drawio"))
render("out.drawio")          # out.png (3x) + out.pdf (page size)
```

```bash
python scripts/archdiagram.py lint out.drawio
python scripts/archdiagram.py render out.drawio
python tests/selftest.py
```

## Licence

MIT - see `LICENSE`. Icons are loaded from the draw.io icon libraries and remain subject to their own terms.
