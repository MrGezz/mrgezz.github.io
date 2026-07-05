# Web Update Report — MrGezz.github.io

**Date:** 2026-07-06
**Scope:** Refresh the SuiteTools documentation site to reflect the current `IcZScripts.extension`, add presentation/interactivity ("wow factor"), expand the shared-library documentation, strip legacy branding, and avoid exposing proprietary code. README aligned to match.

---

## 1. What changed at a glance

| Area | Before | After |
|---|---|---|
| Tools documented | 7 | **9** |
| Ribbon panels | 4 | **5** |
| Library modules shown | ~13 (6 detailed) | **20** (all, with a filterable explorer) |
| Real code snippets | 31 blocks (internal logic) | **1** public-surface usage stub |
| Interactive elements | scrollspy, search, theme toggle | + architecture diagram, module explorer, count-up metrics |
| Branding | neutral, but sourced from `GmBH`/`DX`/`DEAXO` metadata | fully scrubbed to **IcZ** |

---

## 2. Tools added / updated

**Newly documented**

- **GUIDE — Guideline Library** (`QualityCheck.panel`). One-click access to company standards (PDF/Word) scanned from shared folders. The tool's original tooltip ("A LIST OF STANDARD BY DEAXO") was rewritten to neutral "company standards" language on the public site.
- **DOC — Auto-Doc Tools** (`Tools.panel`). Auto-generates plans, sections, elevations, isometric 3D, dimensions, tags and sheet placement, and enforces naming conventions. Documented its top ≥ cut ≥ bottom view-range hierarchy and its port onto the shared theme + version-safe element Ids.

**Refreshed**

- **IssueOne (EXP)** re-cast from a COM-centric exporter to its current form: six formats (DWG/PDF/NWC/IFC/images/Excel), reusable **profiles v2**, token-based **filename builder**, split output folders, mid-batch **warning suppression**, and a **pure-Python XLSX** path (COM now opt-in only). Tied to the shared `exportcfg`, `xlsxlite`, `perftracker`, `failures`, `lastused` modules.
- **AA, FIT, PSU, CD, RO1, WOT** descriptions tightened to match the current `bundle.yaml` tooltips (e.g. FIT's four pages and per-page gear/Settings view; CD's Nested Align/Resolve; WOT's review grid + filters + XLS report).

---

## 3. "Wow factor" & informativeness

- **Interactive architecture diagram** — a layered view (ribbon → `icz` library → Revit API). Hovering any tool lights up exactly the modules it consumes; hovering a module reverse-highlights its consuming tools. Keyboard-focusable.
- **Module explorer** — all 20 modules as cards grouped by domain (Core, UI/Shell, MEP, Data, Worksharing, Export/IO), each tagged with its consuming tools, with a live text filter and domain chips.
- **Animated suite metrics** — count-up counters (9 tools / 5 panels / 20 modules / Revit 2023–26) that run on scroll into view.
- **Polish** — retained glassmorphism, scroll-reveal, gradient hero, sidebar scrollspy and Light/Dark toggle; added workflow "step" strips for AA in place of code.

---

## 4. Code-privacy handling

Per the decision to **keep tiny usage stubs only**, all 31 internal-logic Python blocks (duplicate-grouping loops, flex-conversion math, BOQ size parsing, NWC flag handling, level-offset math, etc.) were removed. They were replaced with prose capability descriptions, sub-cards, and workflow steps. A single **public-surface stub** remains in the Shared Library section — imports plus call signatures only (`ShellWindow`, `ExternalCall`, `eid`) — with an explicit note that internal logic stays private.

---

## 5. Branding scrub

The site had no visible `GmBH`/`DEAXO` strings, but tool metadata did. Verified the final `index.html` and `README.md` contain **no** `GmBH`, `DEAXO`, `ACCDocs` paths, or bare `DX` tokens. The AA tool is presented as "Project QC Automation"; GUIDE as "Guideline Library"; workset/branding specifics (e.g. `DX_*` names) are not surfaced publicly.

---

## 6. Files changed

- `index.html` — full rebuild (new Architecture section, GUIDE + Auto-Doc sections, IssueOne refresh, 20-module explorer, count-up + diagram JS, nav reorganized into 5 panels).
- `README.md` — rewritten to mirror the site (9-tool table, 20-module map, feature list, privacy stance, screenshot filenames).

---

## 7. Appendix — Legacy script provenance (what improved vs. the originals)

Context requested separately: a summary of the improvements in the three legacy DX scripts in `IcZWorkSpace`. Each has since evolved into a productionized suite tool. The through-line is the same across all three: **blocking prompts → modeless WPF UI, hardcoded values → JSON config, console prints → in-window reporting, monolithic inline logic → shared `icz` modules, and single-version → Revit 2023–2026 safe.**

### `DX_B37_X and Y coordinates filling.py` → FIT ▸ *Write X/Y Coordinates*

*Original (64 lines):* wrote world X/Y into hardcoded params `H.2 Position X` / `H.3 Position Y` for the current selection; hardcoded offsets (`OFFSET_X_MM = 651200`, `OFFSET_Y_MM = 597200`); single flat `Transaction`; `print()` feedback; no overwrite guard; point-based families only.

*Improvements now in the suite:*
- Offset and target parameter names are **editable and persisted** (`coords_config.json`) instead of hardcoded.
- An **overwrite guard** protects existing values.
- Runs inside the **modeless MEP Workbench** with in-window feedback instead of console prints.
- Operates over a **scope** (model/view/selection) rather than selection-only, and is version-safe via `icz.revit_compat`.

### `XXX_BIM_MI_Script_002_DX.py` → FIT ▸ *Pipe/Duct → Flex*

*Original (625 lines):* a WinForms (`System.Windows.Forms`) picker titled "DX | Pipe to Flex Pipe" to choose flex type / diameter / workset, converting rigid pipe to flex; console logging.

*Improvements now in the suite:*
- Rebuilt on **WPF** inside the shared themeable shell (WinForms dropped).
- Adds **Replace** vs **Containment** modes with three containment sub-modes, plus tunable rank / snap / smoothing / branch count — persisted to `flex_config.json`.
- Reconnection and connector logic consolidated into the shared **`icz.mep`** engine.
- Neutral naming (no "DX" title) and Revit 2023–2026 safe.

### `XXX_BIM_MI_Script_001_DX.py` → AA ▸ *Project QC Automation*

*Original (2664 lines):* a monolithic RevitPythonShell script — "BOM SPEC Extended Parameters Update" — driving the pipeline through chained blocking `TaskDialog` prompts: Phase 0 duplicate check, Phase 0.5 slope correction, … Phase 7 IFC category assignment, inline size formatters (`convert_pipe_size`, duct/cable-tray formatting), and hardcoded workset routing (`DX_STB`, `DX_EXH`, `DX_ELT`, `DX_RR`) with inline SPEC mappings.

*Improvements now in the suite:*
- Reworked into a **dedicated modeless WPF UI** with a **pre-scan review grid** (isolate/zoom, approve before commit) and a post-run **metric-chip report** — replacing the chain of `TaskDialog` prompts.
- Classification/routing tables externalized to **`spec_mappings.json`** (`icz.mappings`) instead of hardcoded inline dicts.
- Shared logic factored into **`icz`** modules — `params`, `mep`, `worksets`, `ownership`, `naming` — instead of one 2,600-line file.
- **Version-safe** across Revit 2023–2026 (`icz.revit_compat`) and JSON-driven so it runs across multiple projects.

*Note:* these `DX_*`/`XXX_*` files are the private originals; none of their internal logic or `DX`/`DEAXO` naming is exposed on the public site.
