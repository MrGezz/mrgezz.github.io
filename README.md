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

**18 tools across 6 ribbon panels** on the `SuiteTools` tab. The panels are IcZ, Parameter,
QualityCheck, Report, Structural and Tools. The labels below are the ones the ribbon shows. They
are read from the `bundle.yaml` titles, not from a design document. The site is synced to
workspace round **r176** (2026-10-14), and Model Cleanup to **r190** (2026-10-18).

| Code | Ribbon label | Panel | What it does |
| --- | --- | --- | --- |
| **AA** | QAQC Suite | IcZ | Modeless model-maintenance and QA/QC workbench for MEP models — a phased parameter pipeline with a pre-scan review grid, health scoring and colour mapping. |
| **FIT** | MEP Workbench | IcZ | Modeless workbench for MEP cleanup, routing and parameter maintenance — nine pages on a grouped rail: Diagnose, Fabricate, Document. Its element-level edits are also on this pulldown as seven single buttons, so each can take a keyboard shortcut. |
| **PSU** | Project Setup | IcZ | Project-level housekeeping in one modeless workbench — twelve setup stations behind a read-only audit cover sheet, each scanning first and committing through the one footer button. |
| **SR** | Script Reader | IcZ | Script library browser, editor and interactive IronPython console. Runs .py scripts from configurable library folders inside a valid Revit API context, modeless and external-event dispatched. |
| **RO1** | BOQ Lookup | Parameter | Bill-of-Quantities population for MEP and construction models: size → nominal width → BOQ designation and description, then a unit-aware bill and a native Revit schedule. |
| **FQC** | Family QC | QualityCheck | Family-definition health check — seventeen checks on the family open in the editor, or eight across a whole folder of .rfa files without opening one. A health percentage, an A–F grade, and every finding. |
| **GUIDE** | Guideline | QualityCheck | The project standards library inside Revit: browse, preview, annotate and edit reference documents — PDF, Excel, Word, Markdown, images — without leaving the model. |
| **WOT** | MEP Opening Tool | Report | MEP penetration and opening tool across host and linked files: find every crossing, place and mark the openings, audit the ones already there, and see what changed since the last run. |
| **CIT** | Clash Importer | Report | Import a clash report — Navisworks XML or HTML, CSV, Excel, BCF — and place a marker family at every clash point on a verified coordinate basis. Each marker remembers its clash, so a re-import reconciles instead of duplicating. |
| **ICX** | Interference Check | Report | Find interferences between two sets of categories — and, unlike Revit's own Interference Check, against linked Revit models and linked Navisworks coordination models as well. Read-only: it opens no transaction. |
| **PLR** | Pressure Loss | Report | Pressure loss through a pipe or duct network from the pipework alone. No mechanical equipment has to be set up. Pick the start, give the pressure, the flow and the fluid, then Calculate. Read-only: it opens no transaction. |
| **ISO** | Pipe Iso | Report | A piping isometric of one run, drawn from the model. Pick where the run starts and, optionally, where it ends. It shows cut lengths, riser elevations and a bill of material. Export a PDF sheet or create a drafting view. |
| **SFX** | Framing Fix | Structural | Structural Framing Fix — disallows framing joins while preserving the as-joined physical length; regrowth is compensated into Start/End Extension so faces stay put. |
| **CD** | Model Cleanup | Tools | Nine cleanup passes in one modeless workbench — ghosts, overkill lines, connections, the system layer, nested cases, orphans and duplicate families — armed individually, committed as one undoable act. |
| **EXP** | Transmit | Tools | One transmittal, every format. DWG, PDF, Navisworks, IFC, images and Excel in a single run, with crisp embedded logos, clean schedule tables, consistent naming and a ready-to-send sheet index. |
| **DOC** | Sheet AutoDoc | Tools | Data-driven sheet generation on the Universal Layout Engine: one detail sheet per staged element — plan, sections, 3D, tags, an optional schedule — docked to anchors defined in a profile, not in code. |
| **SB** | Section Box | Tools | Subject-aware section boxes for 3D views: the Create window, or the one-click Quick Box, Quick Section Box, Grow, Shrink and Toggle — each an individual pushbutton you can bind to a keyboard shortcut. |
| **TS** | Text Search | Tools | Find text notes by what they say, in the whole model, the active view or any one view, and zoom to them. Type part of a note, such as a damper code, and the list follows the keyboard. Replace asks first. |

Two of these are pulldowns, so the tab registers **30 commands** in total.
Every tool is a modeless WPF application sharing one themeable shell and the `icz` library.

---

## The `icz` shared library

Every tool once re-solved the same problems: the Revit 2024 `ElementId` breaking change,
modeless-window threading, theme duplication, worksharing checks, MEP connector hashing,
batch-export plumbing. `icz` pulls all of it into one dependency-free library of
**112 modules**, so each tool imports instead of reimplementing.

- ✅ **No external dependencies** — pure Revit API + WPF. Nothing to pip-install, no COM.
- ✅ **IronPython 3** — the suite runs the ipy3 engine (measured 3.4.2 in-session);
  a `pycompat` seam keeps modules clean where an interpreter difference still shows.
- ✅ **Revit 2022 → 2026** — version-guarded `ElementId` conversions, category enums and
  API divergences, resolved by name at import rather than caught as exceptions.
- ✅ **Single source of truth** — theme palette, MEP tolerances, export contracts, one
  canonical JSON persistence path and the window chrome each live in exactly one place.
- ✅ **Compiled core** — five seams (`overkill`, `routing`, `layout_native`, `wallmath`,
  `nodecluster`) are drop-ins over a C# `IcZ.Core` assembly. They stop with an error message
  and do not fall back to a slower path with no message.

### Module map

| Domain | Purpose | Modules |
| --- | --- | --- |
| **Core &amp; host seam** | The things every tool needs before it can do anything. | `revit_compat` &middot; `pycompat` &middot; `host` &middot; `availability` &middot; `units` &middot; `depends` &middot; `licensing` &middot; `dllguard` &middot; `diaglog` &middot; `scripting` &middot; `scan` &middot; `selection` &middot; `design_options` |
| **UI &amp; shell** | One window chrome, one palette, one set of inputs — so eighteen tools look like one product. | `theme` &middot; `shell` &middot; `modeless` &middot; `confirm` &middot; `dialogs` &middot; `review_grid` &middot; `validation` &middot; `tristate` &middot; `settingsform` &middot; `checkcombo` &middot; `reader` &middot; `codetok` &middot; `pdfview` &middot; `sheetpreview` &middot; `reporthtml` &middot; `fmt` &middot; `portlist` |
| **Data visualisation** | The in-window reporting surface shared by the QA/QC tools. | `dashboard` &middot; `piechart` &middot; `radar` &middot; `scorecard` &middot; `colorize` &middot; `vfilters` &middot; `viewbox` &middot; `preview3d` |
| **Revit model** | Reading, selecting and safely changing elements. | `revit_utils` &middot; `modify` &middot; `geom` &middot; `naming` &middot; `tags` &middot; `tag_leader` &middot; `params` &middot; `paramreg` &middot; `spacesync` &middot; `openings` &middot; `famlib` &middot; `famedit` &middot; `vfilters` &middot; `vg_transfer` &middot; `viewbox` &middot; `libmodel` |
| **MEP &amp; geometry engines** | The computational core; five of these are drop-ins over the compiled IcZ.Core assembly. | `mep` &middot; `mepdoctor` &middot; `route` &middot; `routing` &middot; `flex` &middot; `riser` &middot; `framing_join` &middot; `layout_native` &middot; `nodecluster` &middot; `wallmath` &middot; `overkill` &middot; `orphans` &middot; `doclayout` &middot; `sheet_overlap` &middot; `nwd` &middot; `mepnet` &middot; `isodraw` &middot; `isosymbols` |
| **Quality &amp; health** | The checks behind QAQC Suite and Family QC. | `mcheck` &middot; `mhealth` &middot; `revitwarnings` &middot; `famqa` &middot; `famqa_atom` &middot; `famqa_catalogue` &middot; `famqa_rename` &middot; `famqa_config` &middot; `famqa_logic` &middot; `bimstd` &middot; `wot_delta` &middot; `sysguard` |
| **Clash coordination** | Every clash-report format behind one reader, the marker ⇄ clash link, the spatial index and the ICX → CIT handoff. Revit-free, harness-tested offline. | `clashreport` &middot; `clashxml` &middot; `clashhtml` &middot; `clashtable` &middot; `bcf` &middot; `xmlwalk` &middot; `clashstate` &middot; `clashgrid` &middot; `clashhandoff` &middot; `icx_delta` |
| **Data, IO &amp; persistence** | One canonical JSON path, one spreadsheet writer, no third-party packages. | `configstore` &middot; `presets` &middot; `toolstate` &middot; `serdes` &middot; `exportcfg` &middot; `xlsxlite` &middot; `xlsxread` &middot; `report_export` &middot; `lastused` &middot; `perftracker` &middot; `failures` &middot; `pdflite` &middot; `isopdf` |
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

The suite also ships an **attach-by-default MCP server**, `revit-launch`. It lets an agent work
with a running Revit session. The agent can read the model and dry-run writes that execute and roll
back. It can drive the ribbon, click named controls and run named smoke scenarios. It can also
capture every page of a rendered window as PNGs, to review against an approved design. The server
has zero dependencies and speaks JSON-RPC 2.0 over stdio. It offers **140 tools** in the default
configuration and 161 with every gate on. Four independent gates apply. The seven process-lifecycle
tools and the live Dynamo tools are off by default. There are three profiles: core 7, standard 30
and full. The 90 modelling commands run over two transports behind one prefix. 64 run in the
first-party `IcZ.Bridge` add-in over a named pipe, which needs no pyRevit. 26 run inside the
pyRevit session. Its sibling **`navisworks-launch`** drives Navisworks Manage and Simulate the same
way. It offers 23 tools by default and 30 with every gate on. Its dry run has a revert that is
measured, not assumed.

They are two of **16 MCP servers**: 13 in the development workspace and 3 standalone projects.
Together they offer **414 tools** at default gates and 446 with every gate on. The count leaves
out `comfy-mcp`, whose catalogue is third-party. The roster has six lanes.

* **Knowledge, 3 servers:** `revit-knowledge`, `pyrevit-knowledge` and `ironpython-knowledge`.
  They look up an API or a reference. No application needs to run.
* **Live sessions, 2 servers:** `revit-launch` and `navisworks-launch`.
* **Memory and documents, 3 servers:** `icz-memory`, `icz-docs` and `icz-skills`.
* **Senses, 2 servers:** `icz-senses` reads screens and audio. `scrapling` searches and fetches the web.
* **Beyond Revit, 3 servers:** `blender-launch`, `mod-organizer` and `comfy-mcp`.
* **Standalone projects, 3 servers:** `excel-knowledge` with 46 tools, `icz-learn` with 5 tools
  and `icz-reverse` with 36 tools. Each one lives in its own private repository.

The site shows the roster as a grid that you can filter by lane. The *Revit MCP Bridge* section
holds the reference for the two bridges.

## Around the suite

The suite is one repository of several. Two other projects are tools for the work around it.
`IcZ Obfuscator` makes a source-protected copy of a pyRevit extension that still runs, in four
tiers. `IcZ Subagent` is a model-workflow policy: worker agents run on pinned tiers, and a gate
hook checks every launch. Three standalone servers have their own repositories: `excel-knowledge`,
`icz-learn` and `icz-reverse`. The BIM & AI Guide is the companion course site. The site shows each
one on a card in the *Around the suite* section. A repository marked private has no link there on
purpose.

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

* **The eighteen tools at a glance** — one row per tool: panel, the ribbon's own one-liner, how it
  touches the model, what goes in and out, what is new. Hover a row to light its ribbon button.
* **Four workflow chapters** — set up and clean · coordinate · quantify, draw, issue · standards,
  families, scripts — each opening with the hand-off and a linked flow strip; the sidebar follows.
* **At-a-glance strip on every tool** — does / touches the model / works with — with the feature
  list capped at four and the window's pages collapsed behind a summary that opens on arrival.
* **An agent can drive it** — the orient / read / dry-run / commit story and the terminal, right
  after Architecture; the MCP section keeps the reference half and a collapsed bridge history.
* **Routing field** — the hero background is a live canvas: pipe-like runs walk a grid, a crossing
  flashes red as a clash and is then placed as a cyan marker, and the cursor is an obstacle.
  Paused when off-screen; a single static frame under `prefers-reduced-motion`.
* **The ribbon, as Revit shows it** — the SuiteTools tab rebuilt inline: six panels, eighteen
  buttons, the two pulldowns open, every tooltip taken from the tool's own `bundle.yaml`. Click a
  button to jump to its section; the lit button follows the section on screen.
* **Jump to anything** — `Ctrl`/`⌘` `K` opens a palette over every section, tool, carded module,
  MCP tool and round, with subsequence matching.
* **Wired architecture diagram** — all 112 modules in six domain bands, hubs weighted at rest;
  hover a tool to draw the wires to the modules it consumes, hover a module to wire it to its
  consumers, click to pin, or run the tour across all eighteen.
  Every edge is re-measured from the tool folders' `from icz… import` lines at each sync.
* **What's new since September** — thirteen product stops on a calendar rail from r150 to r190, each
  linking to the section it explains. The earlier eight stops sit behind a summary.
* **Try the contract** — an illustrative terminal in the MCP section plays the JSON-RPC exchange
  for a status call, a dry-run delete, commit + confirm, a refused `Save()`, a smoke scenario and
  a Navisworks clash run.
* **Keyboard** — `/` filters the sidebar, `j`/`k` step sections, `t` flips the theme, `g g`
  returns to the top, `?` shows the card. Cards tilt toward the cursor; there is one easter egg.
* **Interactive architecture diagram** — hover a tool to trace the `icz` modules it consumes,
  or hover a module to see which tools depend on it.
* **Three new tool sections** — Pressure Loss, Pipe Iso and Text Search each have a section with
  a does / touches the model / works with strip, a window-pages summary and a mockup.
* **MCP roster grid** — all 16 servers by lane, with a filter, the tool count of each server and
  the standalone projects named.
* **Around the suite** — a section of cards for the other repositories and the guide site.
* **Live interface previews** — every tool's real UI, embedded as a hand-authored mockup
  under `mockups/`, auto-fitted down to phone widths. `cd.html`, Model Cleanup's plan of findings,
  is the newest; `plr.html`, `iso.html` and `textsearch.html` came just before it.
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
| `mockups/` | Hand-authored interface previews, one per tool (`cd.html`, Model Cleanup's plan of findings, is the newest), embedded by `index.html` |
| `404.html` | Themed not-found page |
| `og.html`, `og.png` | The link-preview card and the template it is rendered from — a designed 1200×630 card, never a screenshot of the page; the regeneration recipe is in the template's head comment |
| `favicon.svg`, `robots.txt`, `sitemap.xml` | Site plumbing |
| `.nojekyll` | Serve the tree as authored; skip Jekyll processing |

---

**Maintainer:** [@MrGezz](https://github.com/MrGezz) — BIM Coordinator & Revit API developer.
Licensed under [all rights reserved](LICENSE); see that file for what viewing and quoting are
permitted. &copy; 2026 IcZ. Hosted on GitHub Pages.
