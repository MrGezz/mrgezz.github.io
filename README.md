# IcZScripts · SuiteTools — Documentation Site & Shared Library

[![GitHub Pages](https://img.shields.io/badge/Hosted_on-GitHub_Pages-00C8FF?logo=github)](https://MrGezz.github.io)
[![pyRevit](https://img.shields.io/badge/Built_for-pyRevit_6.4-3776AB?logo=python)](https://github.com/pyrevitlabs/pyRevit)
[![Revit](https://img.shields.io/badge/Revit-2023--2026-0696D7)](#)
![IronPython](https://img.shields.io/badge/IronPython-2.7-306998?logo=python&logoColor=white)

The public documentation site for **`IcZScripts.extension`** — a pyRevit suite of BIM/MEP automation tools for Autodesk Revit 2023–2026. The site explains what each tool does, how it fits together, and the shared library that underpins the whole suite. It documents capabilities and architecture only — implementation details stay in the private repository.

🌐 **Live site:** [https://MrGezz.github.io](https://MrGezz.github.io)

---

## Documented Tools

The site documents every tool shipping in the `SuiteTools` ribbon tab — **9 tools across 5 panels**:

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
| **WOT** | Wall Opening Report | Report | MEP ↔ wall intersection detection across linked files, with markers & XLS report |

*Every tool is a modeless WPF app sharing one themeable shell and the `icz` core library documented below.*

---

## 🛠️ The `icz` Shared Library

Every pyRevit tool in the suite once re-solved the same problems: the Revit 2024 `ElementId` breaking change, modeless-window threading, theme duplication, worksharing checks, MEP connector hashing, and batch-export plumbing.

**`icz`** pulls all of it into one dependency-free library — **20 modules** — so each tool imports instead of reimplementing.

- ✅ **No external dependencies** — pure Revit API + WPF, nothing else to install.
- ✅ **IronPython 2.7 safe** — avoids the syntax/comprehension edge-cases that trip up IPy.
- ✅ **Revit 2023 → 2026 safe** — version-guarded `ElementId` conversions and category enums.
- ✅ **Single source of truth** — theme palette, MEP tolerances, export contracts and UI chrome live in exactly one place.

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
| Core | `units` | Feet ⇄ millimetre factor + shared MEP tolerance constant. |
| UI / Shell | `theme` | Single Light/Dark palette pushed into WPF `{DynamicResource}` keys. |
| UI / Shell | `shell` | `ShellWindow` chrome — traffic-light dots, collapsible nav-rail, deferred theme apply. |
| UI / Shell | `modeless` | Wraps `IExternalEventHandler` into a reusable `ExternalCall` for safe UI→model writes. |
| UI / Shell | `review_grid` | Shared review rows (check · ID · Family & Type · badge) with click-to-isolate hooks. |
| UI / Shell | `validation` | Reusable WPF `ValidationRule` subclasses for inline settings-page feedback. |
| UI / Shell | `revit_utils` | Selection/isolation helpers and dynamic shared-parameter binding. |
| MEP | `mep` | O(n) spatial-hash connector engine — unused-connector detection & safe auto-connect. |
| MEP | `naming` | Safe family/type name resolution, handling `FamilyInstance` ambiguities. |
| Data | `params` | Instance→Type parameter read/write with one fill-empty / overwrite policy. |
| Data | `mappings` | JSON-driven classification tables loaded from `spec_mappings.json`. |
| Data | `serdes` | JSON-safe deep-clone / serialisation helpers (staged for a future config rewire). |
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
* **Module explorer** — all 20 library modules as filterable/searchable cards, grouped by domain, each showing its consuming tools.
* **Animated suite metrics** — count-up stats for tools, panels, modules and Revit versions.
* **UI/UX** — scroll-reveal animations, glassmorphism components, sidebar scrollspy, and a full Light/Dark theme toggle tied into CSS variables.
* **Privacy-first** — capabilities and architecture are documented in prose; no proprietary implementation is exposed beyond public import signatures.
* **Tech Stack** — HTML5/CSS3 (no heavy frameworks), Prism.js for the one usage stub, FontAwesome 6 for iconography.

## Local Preview

No build step or server required.

```bash
git clone https://github.com/MrGezz/MrGezz.github.io.git
cd MrGezz.github.io
# Open index.html in any modern browser
```

### Screenshots

Drop tool screenshots into `images/` using these filenames to replace the placeholders:
`suite.png`, `aa.png`, `fit.png`, `psu.png`, `cd.png`, `export.png`, `mepauto.png`, `ro1.png`, `wot.png`.

---
**Maintainer:** [@MrGezz](https://github.com/MrGezz) — BIM Coordinator & Revit API developer.
&copy; 2026 IcZ. Hosted on GitHub Pages.
