# IcZScripts · SuiteTools — Documentation Site & Shared Library

[![GitHub Pages](https://img.shields.io/badge/Hosted_on-GitHub_Pages-00C8FF?logo=github)](https://MrGezz.github.io)
[![pyRevit](https://img.shields.io/badge/Built_for-pyRevit_6.4-3776AB?logo=python)](https://github.com/pyrevitlabs/pyRevit)
[![Revit](https://img.shields.io/badge/Revit-2023--2026-0696D7)](#)
![IronPython](https://img.shields.io/badge/IronPython-2.7_%E2%87%8B_3-306998?logo=python&logoColor=white)

The public documentation site for **`IcZScripts.extension`** — a pyRevit suite of BIM/MEP automation tools for Autodesk Revit 2023–2026. The site explains what each tool does, how it fits together, and the shared library that underpins the whole suite. It documents capabilities and architecture only — implementation details stay in the private repository.

🌐 **Live site:** [https://MrGezz.github.io](https://MrGezz.github.io)

---

## Documented Tools

The site documents every tool shipping in the `SuiteTools` ribbon tab — **10 tools across 5 panels**:

| Code | Tool | Panel | Purpose |
| --- | --- | --- | --- |
| **AA** | Project QC Automation | IcZ | Full-lifecycle MEP parameter pipeline (phases 0–8) with a pre-scan review grid |
| **FIT** | MEP Workbench | IcZ | Fittings/Accessories audit · Write X/Y coordinates · Pipe/Duct→Flex · copy params between systems |
| **PSU** | Project Setup | IcZ | Link worksets · guarded Project Info · elevation-safe level swap |
| **CD** | Model Cleanup | Tools | Ghost Purge + Connection Fix + Nested Fix (review-then-run) |
| **EXP** | IssueOne | Tools | Batch export to DWG / PDF / NWC / IFC / images / Excel with profiles, filename builder & sheet index |
| **DOC** | Auto-Doc Tools | Tools | Auto plans/sections/elevations/3D, dimensions, tags, sheet placement & naming conventions |
| **RO1** | BOQ Lookup | Parameter | Size → Nominal Width → BOQ designation/description |
| **GUIDE** | Guideline Library | QualityCheck | One-click access to the company standards (PDF/Word) from shared folders |
| **FQC** | Family QC | QualityCheck | Health-check the open family against a firm profile — per-check pass/warn/fail + overall score, HTML report |
| **WOT** | Wall Opening Report | Report | MEP ↔ wall intersection detection across linked files, with a tri-state element scope, markers & XLS report |

*Every tool is a modeless WPF app sharing one themeable shell and the `icz` core library documented below.*

---

## 🛠️ The `icz` Shared Library

Every pyRevit tool in the suite once re-solved the same problems: the Revit 2024 `ElementId` breaking change, modeless-window threading, theme duplication, worksharing checks, MEP connector hashing, and batch-export plumbing.

**`icz`** pulls all of it into one dependency-free library — **35+ modules** — so each tool imports instead of reimplementing.

- ✅ **No external dependencies** — pure Revit API + WPF, nothing else to install.
- ✅ **Dual-engine (IronPython 2.7 ⇋ 3)** — the suite runs on the IronPython 3 engine, with ipy2.7 retained for Revit ≤2023; a shared `pycompat` seam keeps every module clean across both.
- ✅ **Revit 2023 → 2026 safe** — version-guarded `ElementId` conversions and category enums.
- ✅ **Single source of truth** — theme palette, MEP tolerances, export contracts, one canonical JSON persistence path, and UI chrome live in exactly one place.

### Installation & Integration

Drop the `icz` folder into your pyRevit extension's `lib/` directory. pyRevit adds `lib/` to `sys.path` automatically.

```python
from icz import theme
from icz.shell import ShellWindow      # shared window chrome
from icz.modeless import ExternalCall  # ExternalEvent-safe model writes
from icz.revit_compat import eid       # Revit 2024+ ElementId shim
```

### Module Map

| Domain | Module | Purpose |
|---|---|---|
| Core | `revit_compat` | `ElementId` 32-bit ⇄ 64-bit shim for Revit 2024+. The one change every module relies on. |
| Core | `pycompat` | The IronPython 2.7 ⇄ 3 seam (`to_text`, `text_type`, `iteritems`…) — the prerequisite that unblocked the ipy3 rollout. |
| Core | `host` | Host-application seam: one place to reach the active document, transactions, bundle files and the shared `IcZ.Core.dll` — delegates to pyRevit when present, native handle when not. |
| Core | `availability` | Launch-time document guards so a tool clicked with no project open aborts with a clear notice, not a `NoneType` crash. |
| Core | `units` | Feet ⇄ millimetre factor + shared MEP tolerance constant. |
| UI / Shell | `theme` | Single Light/Dark palette pushed into WPF `{DynamicResource}` keys, plus shared `set_resource_ref` / `mdl2_font` chrome helpers. |
| UI / Shell | `shell` | `ShellWindow` chrome — traffic-light dots, collapsible nav-rail, deferred theme apply, injected scrollbar + tooltip dictionaries. |
| UI / Shell | `modeless` | Wraps `IExternalEventHandler` into a reusable `ExternalCall` for safe UI→model writes. |
| UI / Shell | `review_grid` | Shared review rows (check · ID · Family & Type · badge) with click-to-isolate hooks. |
| UI / Shell | `validation` | Reusable WPF `ValidationRule` subclasses for inline settings-page feedback. |
| UI / Shell | `tristate` | Hierarchical tri-state selection tree (`bool?` parent semantics) — one parent toggles all leaves; first used by WOT's MEP element scope. |
| UI / Shell | `dialogs` | One hybrid-safe file/folder picker over the .NET dialogs — no pyRevit `forms` dependency. |
| UI / Shell | `settingsform` | Schema-driven WPF form built from a config dict — typed editors, no raw-JSON editing (used by Family QC + others). |
| UI / Shell | `revit_utils` | Selection/isolation helpers and dynamic shared-parameter binding. |
| MEP | `mep` | O(n) spatial-hash connector engine — unused-connector detection & safe auto-connect. |
| MEP | `naming` | Safe family/type name resolution, handling `FamilyInstance` ambiguities. |
| Data | `params` | Instance→Type parameter read/write with one fill-empty / overwrite policy; `describe()` returns per-parameter metadata (storage / read-only / modifiable) for edit-time validation. |
| Data | `configstore` + `serdes` | The one canonical JSON persistence path suite-wide: `serdes` deep-clone + the single atomic `.tmp→.bak→rename` writer, `configstore` load / deep-merge / `read_json` (best-effort or fail-loud seed reads). Every tool's config load/save routes through the pair. |
| Data | `paramreg` | Managed-parameter registry (`param_registry.json`) — retarget a firm by swapping JSON, no code change. *(AA's classification tables live in `spec_mappings.json`, loaded by an AA-local `mappings` helper.)* |
| Worksharing | `worksets` | Find/create/assign worksets and test partition writability en masse. |
| Worksharing | `ownership` | Worksharing checkout/editability guards for multi-user models. |
| Export / IO | `exportcfg` | Shared `profiles.json` handler (schema v2) for the export tools. |
| Export / IO | `xlsxlite` | Minimal pure-Python `.xlsx` writer — real OOXML, no COM, no third-party packages. |
| Export / IO | `perftracker` | Per-format count/elapsed/warn/error summary at the end of a batch run. |
| Export / IO | `failures` | `IFailuresPreprocessor` warning suppression so batch ops don't stall on popups. |
| Export / IO | `lastused` | Persistent per-purpose folder/file memory with slots (profile, template, output…). |

### Highlight: `revit_compat` (ElementId version shim)

Revit 2024 replaced `ElementId.IntegerValue` with `ElementId.Value` (Int64). This shim lets the codebase run seamlessly across versions — the public surface is simply:

```python
from icz.revit_compat import eid, make_eid, is_valid

element_id_int = eid(my_element.Id)         # ElementId -> int, any Revit version
new_id         = make_eid(element_id_int)   # int -> ElementId, any Revit version
if is_valid(my_element):                    # guards against mid-run deletions
    ...
```

*(Only the public call surface is published here; internal logic remains private.)*

---

## 🎨 Site Features & Tech Stack

This documentation site is a single-page, zero-build portal.

* **Interactive architecture diagram** — hover any tool to trace the `icz` modules it consumes, or hover a module to see which tools depend on it.
* **Module explorer** — the library modules as filterable/searchable cards, grouped by domain, each showing its consuming tools.
* **Animated suite metrics** — count-up stats for tools, panels, modules and Revit versions.
* **CyanogenMod-inspired theme** — charcoal blue-grey surfaces with the signature `#00BCD4` cyan accent in dark mode, and a Material blue-grey light variant (`#0097A7` cyan-700 accent). Both modes drive every component through CSS variables; the toggle choice persists across visits.
* **Collapsible sidebar** — on desktop the navigation collapses to a 68 px icon rail (native tooltips, active-item indicator, persisted state) — the same pattern as the suite's `ShellWindow` nav-rail. On mobile it stays an off-canvas drawer.
* **Thin themed scrollbars** — 6 px accent-tinted scrollbars (WebKit + Firefox `scrollbar-width: thin`) that re-tint automatically with the Light/Dark toggle.
* **UI/UX** — scroll-reveal animations, glassmorphism components, sidebar scrollspy, and a full Light/Dark theme toggle tied into CSS variables.
* **Privacy-first** — capabilities and architecture are documented in prose; no proprietary implementation is exposed beyond public import signatures.
* **Tech Stack** — HTML5/CSS3 (no heavy frameworks), Prism.js for the one usage stub, FontAwesome 6 for iconography, `localStorage` for theme + nav-state persistence.

## Local Preview

No build step or server required.

```bash
git clone https://github.com/MrGezz/MrGezz.github.io.git
cd MrGezz.github.io
# Open index.html in any modern browser
```

### Screenshots

Drop tool screenshots into `images/` using these filenames to replace the placeholders:
`suite.png`, `aa.png`, `fit.png`, `psu.png`, `cd.png`, `export.png`, `mepauto.png`, `ro1.png`, `wot.png`, `familyqc.png`.

---
**Maintainer:** [@MrGezz](https://github.com/MrGezz) — BIM Coordinator & Revit API developer.
&copy; 2026 IcZ. Hosted on GitHub Pages.