# IcZScripts &middot; SuiteTools — Showcase Site

[![GitHub Pages](https://img.shields.io/badge/Hosted_on-GitHub_Pages-00BCD4?logo=github)](https://mrgezz.github.io)
[![pyRevit](https://img.shields.io/badge/Built_for-pyRevit_6.4-3776AB?logo=python)](https://github.com/pyrevitlabs/pyRevit)
[![Revit](https://img.shields.io/badge/Revit-2022--2026-0696D7)](#)
![IronPython](https://img.shields.io/badge/IronPython-3-306998?logo=python&logoColor=white)
[![License](https://img.shields.io/badge/License-All_rights_reserved-8FA6B2)](LICENSE)

The public showcase for **`IcZScripts.extension`** — a pyRevit suite of BIM/MEP automation
tools for Autodesk Revit 2022–2026. The site explains what each tool does, how the pieces fit,
and the shared library underneath. It documents **capabilities and architecture only**;
the implementation lives in a separate private repository.

🌐 **Live site:** https://mrgezz.github.io

---

## The suite

**14 tools across 6 ribbon panels** on the `SuiteTools` tab. Labels below are the
ones the ribbon actually shows, read from a live Revit 2026.4 session rather than from a
design document.

| Code | Ribbon label | Panel | What it does |
| --- | --- | --- | --- |
| **AA** | QAQC Suite | IcZ | Modeless model-maintenance and QA/QC workbench for MEP models — a phased parameter pipeline with a pre-scan review grid, health scoring and colour mapping. |
| **FIT** | MEP Workbench | IcZ | Fittings and accessories bench: cap open ends, auto-connect, join/disconnect, split joints, make parallel, rotate axis. Seven sub-commands on one pulldown. |
| **PSU** | Project Setup | IcZ | Project-level housekeeping — link worksets, guarded Project Info, elevation-safe level swap. |
| **SR** | Script Reader | IcZ | Script library browser, editor and interactive IronPython console, with syntax colouring and a live namespace. |
| **RO1** | BOQ Lookup | Parameter | Bill-of-Quantities population: size → nominal width → BOQ designation and description. |
| **FQC** | Family QC | QualityCheck | Family-definition health check that runs in the family editor — per-check pass/warn/fail, an overall score and an HTML report. |
| **GUIDE** | Guideline | QualityCheck | The project standards library inside Revit: browse, preview, annotate and edit reference documents — PDF, Excel, Word, Markdown, images — without leaving the model. |
| **WOT** | MEP Opening Tool | Report | MEP penetration and opening detection across host plus linked files, with a tri-state element scope, markers, a reservation audit of existing openings, a live schedule and a spreadsheet report. |
| **CIT** | Clash Importer | Report | Brings a clash report (Navisworks XML / HTML, CSV, Excel, BCF) back into the model as placed markers on a round-trip-verified coordinate basis; each marker remembers its clash, so a re-import reconciles instead of duplicating. |
| **SFX** | Framing Fix | Structural | Disallows structural framing joins while preserving the as-joined physical length — regrowth compensated into Start/End Extension so faces stay put. |
| **CD** | Model Cleanup | Tools | Eight cleanup passes as one production line: ghosts, overkill lines, connections, system integrity, names, types, nested, orphans — armed individually, committed as one undoable act. |
| **EXP** | Transmit | Tools | One transmittal, every format — batch export to DWG / PDF / NWC / IFC / images / Excel with profiles, a filename builder and a sheet index. |
| **DOC** | Sheet AutoDoc | Tools | Data-driven sheet generation through a universal layout engine: plans, sections, elevations, 3D, dimensions, tags, placement and naming conventions. |
| **SB** | Section Box | Tools | Section-box control: quick box, dialog-driven box, grow, shrink and toggle. Six sub-commands on one pulldown. |

Two of these are pulldowns, so the tab registers **26 commands** in total.
Every tool is a modeless WPF application sharing one themeable shell and the `icz` library.

---

## The `icz` shared library

Every tool once re-solved the same problems: the Revit 2024 `ElementId` breaking change,
modeless-window threading, theme duplication, worksharing checks, MEP connector hashing,
batch-export plumbing. `icz` pulls all of it into one dependency-free library of
**84 modules**, so each tool imports instead of reimplementing.

- ✅ **No external dependencies** — pure Revit API + WPF. Nothing to pip-install, no COM.
- ✅ **IronPython 3** — the suite runs the ipy3 engine (measured 3.4.2 in-session);
  a `pycompat` seam keeps modules clean where an interpreter difference still shows.
- ✅ **Revit 2022 → 2026** — version-guarded `ElementId` conversions, category enums and
  API divergences, resolved by name at import rather than caught as exceptions.
- ✅ **Single source of truth** — theme palette, MEP tolerances, export contracts, one
  canonical JSON persistence path and the window chrome each live in exactly one place.
- ✅ **Compiled core** — five seams (`overkill`, `routing`, `layout_native`, `wallmath`,
  `nodecluster`) are drop-ins over a C# `IcZ.Core` assembly and fail loudly rather than
  silently falling back.

### Module map

| Domain | Purpose | Modules |
| --- | --- | --- |
| **Core &amp; host seam** | The things every tool needs before it can do anything. | `revit_compat` &middot; `pycompat` &middot; `host` &middot; `availability` &middot; `units` &middot; `fmt` &middot; `depends` &middot; `licensing` &middot; `diaglog` &middot; `scripting` |
| **UI &amp; shell** | One window chrome, one palette, one set of inputs — so fourteen tools look like one product. | `theme` &middot; `shell` &middot; `modeless` &middot; `confirm` &middot; `dialogs` &middot; `review_grid` &middot; `validation` &middot; `tristate` &middot; `settingsform` &middot; `checkcombo` &middot; `reader` &middot; `codetok` &middot; `pdfview` &middot; `sheetpreview` &middot; `reporthtml` |
| **Data visualisation** | The in-window reporting surface shared by the QA/QC tools. | `dashboard` &middot; `piechart` &middot; `radar` &middot; `scorecard` &middot; `colorize` &middot; `vfilters` &middot; `viewbox` &middot; `preview3d` |
| **Revit model** | Reading, selecting and safely changing elements. | `revit_utils` &middot; `selection` &middot; `modify` &middot; `geom` &middot; `naming` &middot; `tags` &middot; `params` &middot; `paramreg` &middot; `spacesync` &middot; `openings` &middot; `famlib` &middot; `famedit` |
| **MEP &amp; geometry engines** | The computational core; five of these are drop-ins over the compiled IcZ.Core assembly. | `mep` &middot; `mepdoctor` &middot; `route` &middot; `routing` &middot; `flex` &middot; `framing_join` &middot; `layout_native` &middot; `nodecluster` &middot; `wallmath` &middot; `overkill` &middot; `orphans` &middot; `doclayout` |
| **Quality &amp; health** | The checks behind QAQC Suite and Family QC. | `mcheck` &middot; `mhealth` &middot; `famqa` &middot; `famqa_config` &middot; `famqa_logic` &middot; `bimstd` |
| **Clash coordination** | Every clash-report format behind one reader, and the marker ⇄ clash link. Revit-free, harness-tested offline. | `clashreport` &middot; `clashxml` &middot; `clashhtml` &middot; `clashtable` &middot; `bcf` &middot; `xmlwalk` &middot; `clashstate` |
| **Data, IO &amp; persistence** | One canonical JSON path, one spreadsheet writer, no third-party packages. | `configstore` &middot; `serdes` &middot; `exportcfg` &middot; `xlsxlite` &middot; `xlsxread` &middot; `lastused` &middot; `perftracker` &middot; `failures` |
| **Worksharing** | Multi-user safety. | `worksets` &middot; `ownership` |
| **Agent bridge** | The in-Revit half of the MCP bridge — the routes an external agent talks to. | `mcpui` &middot; `mcpmodel` &middot; `mcpwrite` |
| **Out-of-process** | The persistent CPython worker used by the compiled Native tier. | `cpyworker` |

### Integration

Drop the `icz` folder into your pyRevit extension's `lib/` directory — pyRevit puts `lib/`
on `sys.path` automatically.

```python
from icz import theme
from icz.shell import ShellWindow      # shared window chrome
from icz.modeless import ExternalCall  # ExternalEvent-safe model writes
from icz.revit_compat import eid       # Revit 2024+ ElementId shim
```

#### Highlight: `revit_compat`

Revit 2024 replaced `ElementId.IntegerValue` with `ElementId.Value` (Int64). One shim lets
the whole codebase span 2022–2026:

```python
from icz.revit_compat import eid, make_eid, is_valid

element_id_int = eid(my_element.Id)         # ElementId -> int, any Revit version
new_id         = make_eid(element_id_int)   # int -> ElementId, any Revit version
if is_valid(my_element):                    # guards against mid-run deletions
    ...
```

*Only the public call surface is published; internal logic stays private.*

---

## Revit MCP bridge

The suite also ships an **attach-by-default MCP server** (`revit-launch`) that lets an AI agent
work with a running Revit session over pyRevit routes: read the model, dry-run writes that
execute and roll back, drive the ribbon and click named controls, and capture every page of a
rendered window as PNGs to review against an approved design. Zero dependencies, JSON-RPC 2.0
over stdio, **43 tools** in the default configuration with the seven process-lifecycle tools
gated off (it can launch Revit two-phase when that gate is switched on). It is one of seven
local MCP servers in the development workspace; the other six answer questions. It is documented in the
site's *Revit MCP Bridge* section.

---

## Site features

A single-page, zero-build portal.

* **Interactive architecture diagram** — hover a tool to trace the `icz` modules it consumes,
  or hover a module to see which tools depend on it.
* **Live interface previews** — every tool's real UI, embedded as a hand-authored mockup
  under `mockups/`, auto-fitted down to phone widths.
* **Module explorer** — the library as filterable cards grouped by domain, each naming its
  consuming tools.
* **Light / dark theme** — a CyanogenMod-inspired charcoal blue-grey with the `#00BCD4` cyan
  accent, and a Material blue-grey light variant (`#0097A7`). The choice persists across visits.
* **Collapsible sidebar** — collapses to a 68 px icon rail on desktop, an off-canvas drawer on
  mobile; the same pattern as the suite's own `ShellWindow` nav-rail.
* **Privacy-first** — capabilities and architecture in prose only. No proprietary
  implementation beyond public import signatures, and **no client project identifiers**:
  every model name shown in a preview is masked.
* **Tech stack** — HTML5/CSS3 with no framework, Prism.js for the one code stub,
  Font Awesome 6 for iconography, `localStorage` for theme and nav state.

## Local preview

No build step, no server.

```bash
git clone https://github.com/MrGezz/mrgezz.github.io.git
cd mrgezz.github.io
# open index.html in any modern browser
```

## Repository layout

| Path | What it is |
| --- | --- |
| `index.html` | The entire site — markup, styles and script in one file |
| `mockups/` | Hand-authored interface previews, one per tool, embedded by `index.html` |
| `404.html` | Themed not-found page |
| `favicon.svg`, `robots.txt`, `sitemap.xml` | Site plumbing |
| `.nojekyll` | Serve the tree as authored; skip Jekyll processing |

---

**Maintainer:** [@MrGezz](https://github.com/MrGezz) — BIM Coordinator & Revit API developer.
Licensed under [all rights reserved](LICENSE); see that file for what viewing and quoting are
permitted. &copy; 2026 IcZ. Hosted on GitHub Pages.
