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

**15 tools across 6 ribbon panels** on the `SuiteTools` tab. Labels below are the
ones the ribbon actually shows, read from the extension's `bundle.yaml` titles rather than from a
design document. The site is synced to workspace round **r149** (2026-09-24).

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
| **CIT** | Clash Importer | Report | Brings a clash report (Navisworks XML / HTML, CSV, Excel, BCF) back into the model as placed markers on a round-trip-verified coordinate basis; each marker remembers its clash, so a re-import reconciles instead of duplicating. Takes the Interference Check's results directly, already in the model's own feet. |
| **ICX** | Interference Check | Report | Interference detection between two category sets — against this model, linked Revit models and, unlike Revit's own dialog, linked Navisworks coordination models (read through the Navisworks API out of process). Every row says whether it is solid-exact or box-level; a re-run marks New / Gone; read-only. |
| **SFX** | Framing Fix | Structural | Disallows structural framing joins while preserving the as-joined physical length — regrowth compensated into Start/End Extension so faces stay put. |
| **CD** | Model Cleanup | Tools | Eight cleanup passes as one production line: ghosts, overkill lines, connections, system integrity, names, types, nested, orphans — armed individually, committed as one undoable act. |
| **EXP** | Transmit | Tools | One transmittal, every format — batch export to DWG / PDF / NWC / IFC / images / Excel with profiles, a filename builder and a sheet index. |
| **DOC** | Sheet AutoDoc | Tools | Data-driven sheet generation through a universal layout engine: plans, sections, elevations, 3D, dimensions, tags, placement and naming conventions. |
| **SB** | Section Box | Tools | Section-box control: quick box, dialog-driven box, grow, shrink and toggle. Six sub-commands on one pulldown. |

Two of these are pulldowns, so the tab registers **27 commands** in total.
Every tool is a modeless WPF application sharing one themeable shell and the `icz` library.

---

## The `icz` shared library

Every tool once re-solved the same problems: the Revit 2024 `ElementId` breaking change,
modeless-window threading, theme duplication, worksharing checks, MEP connector hashing,
batch-export plumbing. `icz` pulls all of it into one dependency-free library of
**104 modules**, so each tool imports instead of reimplementing.

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
| **Core &amp; host seam** | The things every tool needs before it can do anything. | `revit_compat` &middot; `pycompat` &middot; `host` &middot; `availability` &middot; `units` &middot; `depends` &middot; `licensing` &middot; `dllguard` &middot; `diaglog` &middot; `scripting` &middot; `scan` &middot; `selection` &middot; `design_options` |
| **UI &amp; shell** | One window chrome, one palette, one set of inputs — so fifteen tools look like one product. | `theme` &middot; `shell` &middot; `modeless` &middot; `confirm` &middot; `dialogs` &middot; `review_grid` &middot; `validation` &middot; `tristate` &middot; `settingsform` &middot; `checkcombo` &middot; `reader` &middot; `codetok` &middot; `pdfview` &middot; `sheetpreview` &middot; `reporthtml` &middot; `fmt` |
| **Data visualisation** | The in-window reporting surface shared by the QA/QC tools. | `dashboard` &middot; `piechart` &middot; `radar` &middot; `scorecard` &middot; `colorize` &middot; `vfilters` &middot; `viewbox` &middot; `preview3d` |
| **Revit model** | Reading, selecting and safely changing elements. | `revit_utils` &middot; `modify` &middot; `geom` &middot; `naming` &middot; `tags` &middot; `tag_leader` &middot; `params` &middot; `paramreg` &middot; `spacesync` &middot; `openings` &middot; `famlib` &middot; `famedit` &middot; `vfilters` &middot; `vg_transfer` &middot; `viewbox` |
| **MEP &amp; geometry engines** | The computational core; five of these are drop-ins over the compiled IcZ.Core assembly. | `mep` &middot; `mepdoctor` &middot; `route` &middot; `routing` &middot; `flex` &middot; `riser` &middot; `framing_join` &middot; `layout_native` &middot; `nodecluster` &middot; `wallmath` &middot; `overkill` &middot; `orphans` &middot; `doclayout` &middot; `sheet_overlap` &middot; `nwd` |
| **Quality &amp; health** | The checks behind QAQC Suite and Family QC. | `mcheck` &middot; `mhealth` &middot; `revitwarnings` &middot; `famqa` &middot; `famqa_atom` &middot; `famqa_catalogue` &middot; `famqa_rename` &middot; `famqa_config` &middot; `famqa_logic` &middot; `bimstd` &middot; `wot_delta` |
| **Clash coordination** | Every clash-report format behind one reader, the marker ⇄ clash link, the spatial index and the ICX → CIT handoff. Revit-free, harness-tested offline. | `clashreport` &middot; `clashxml` &middot; `clashhtml` &middot; `clashtable` &middot; `bcf` &middot; `xmlwalk` &middot; `clashstate` &middot; `clashgrid` &middot; `clashhandoff` &middot; `icx_delta` |
| **Data, IO &amp; persistence** | One canonical JSON path, one spreadsheet writer, no third-party packages. | `configstore` &middot; `presets` &middot; `toolstate` &middot; `serdes` &middot; `exportcfg` &middot; `xlsxlite` &middot; `xlsxread` &middot; `report_export` &middot; `lastused` &middot; `perftracker` &middot; `failures` |
| **Worksharing** | Multi-user safety. | `worksets` &middot; `ownership` |
| **Agent bridge** | The in-Revit half of the MCP bridge — the routes an external agent talks to. | `mcpui` &middot; `mcpmodel` &middot; `mcpwrite` &middot; `mcpdynamo` |
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
work with a running Revit session: read the model, dry-run writes that execute and roll back,
drive the ribbon and click named controls, run named smoke scenarios, and capture every page of a
rendered window as PNGs to review against an approved design. Zero dependencies, JSON-RPC 2.0
over stdio, **140 tools** in the default configuration (161 with every gate on), behind four
independent gates — the seven process-lifecycle tools and the live Dynamo tools are off by
default — and three profiles (core 7 · standard 30 · full). The 90 modelling commands run over
two transports behind one prefix: 64 in the first-party `IcZ.Bridge` add-in over a named pipe,
which needs no pyRevit, and 26 inside the pyRevit session. Its sibling **`navisworks-launch`**
(30 tools) drives Navisworks Manage / Simulate the same way, with a dry run whose revert is
measured rather than assumed. They are two of nine local MCP servers in the development
workspace; six answer questions and one drives a local ComfyUI. Both are documented in the
site's *Revit MCP Bridge* section.

## BIM & AI Guide

The site links to the companion **[BIM & AI Guide](https://bim-with-ai.ugezz94.workers.dev)** — a
free course from zero Dynamo and zero code to pyRevit, the Revit API and Navisworks coordination,
with an AI assistant as the pair-programmer. Its decks are reveal.js pages generated from the same
scripts that build the PowerPoint files; the site is handed out by link rather than found by
search, and this page is one of the places the link lives. The guide's *Practical AI Automation in
BIM* deck describes this suite as its "real in-house extension".

---

## Site features

A single-page, zero-build portal, in the CyanogenMod-inspired cyan-on-charcoal theme.

* **Routing field** — the hero background is a live canvas: pipe-like runs walk a grid, a crossing
  flashes red as a clash and is then placed as a cyan marker, and the cursor is an obstacle.
  Paused when off-screen; a single static frame under `prefers-reduced-motion`.
* **The ribbon, as Revit shows it** — the SuiteTools tab rebuilt inline: six panels, fifteen
  buttons, the two pulldowns open, every tooltip taken from the tool's own `bundle.yaml`. Click a
  button to jump to its section; the lit button follows the section on screen.
* **Jump to anything** — `Ctrl`/`⌘` `K` opens a palette over every section, tool, carded module,
  MCP tool and round, with subsequence matching.
* **Wired architecture diagram** — hover a tool to draw the wires to the modules it consumes,
  hover a module to wire it to its consumers, click to pin, or run the tour across all fifteen.
  Every edge is re-measured from the tool folders' `from icz… import` lines at each sync.
* **What landed, round by round** — a scroll-snap timeline from r116 to r149; each stop opens
  to what was measured and links to the tools it touched.
* **Try the contract** — an illustrative terminal in the MCP section plays the JSON-RPC exchange
  for a status call, a dry-run delete, commit + confirm, a refused `Save()`, a smoke scenario and
  a Navisworks clash run.
* **Keyboard** — `/` filters the sidebar, `j`/`k` step sections, `t` flips the theme, `g g`
  returns to the top, `?` shows the card. Cards tilt toward the cursor; there is one easter egg.
* **Interactive architecture diagram** — hover a tool to trace the `icz` modules it consumes,
  or hover a module to see which tools depend on it.
* **Live interface previews** — every tool's real UI, embedded as a hand-authored mockup
  under `mockups/`, auto-fitted down to phone widths.
* **Learn section** — the companion BIM & AI Guide, linked from the hero, the sidebar and the footer.
* **Module explorer** — the library as filterable cards grouped by domain, each naming its
  consuming tools.
* **Light / dark theme** — a CyanogenMod-inspired charcoal blue-grey with the `#00BCD4` cyan
  accent, and a Material blue-grey light variant (`#0097A7`). The choice persists across visits.
* **Collapsible sidebar** — collapses to a 68 px icon rail on desktop, an off-canvas drawer on
  mobile; the same pattern as the suite's own `ShellWindow` nav-rail.
* **Privacy-first** — capabilities and architecture in prose only. No proprietary
  implementation beyond public import signatures, and **no client project identifiers**:
  every model name shown in a preview is masked.
* **Tech stack** — HTML5/CSS3 with no framework, a canvas for the routing field, inline SVG for
  the architecture wires, Prism.js for the one code stub, Font Awesome 6 for iconography,
  `localStorage` for theme and nav state. No build step, no bundle, one file.

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
| `mockups/` | Hand-authored interface previews, one per tool (`icx.html` is the newest), embedded by `index.html` |
| `404.html` | Themed not-found page |
| `og.html`, `og.png` | The link-preview card and the template it is rendered from — a designed 1200×630 card, never a screenshot of the page; the regeneration recipe is in the template's head comment |
| `favicon.svg`, `robots.txt`, `sitemap.xml` | Site plumbing |
| `.nojekyll` | Serve the tree as authored; skip Jekyll processing |

---

**Maintainer:** [@MrGezz](https://github.com/MrGezz) — BIM Coordinator & Revit API developer.
Licensed under [all rights reserved](LICENSE); see that file for what viewing and quoting are
permitted. &copy; 2026 IcZ. Hosted on GitHub Pages.
