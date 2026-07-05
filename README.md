# IcZScripts · SuiteTools — Documentation Site & Shared Library

[![GitHub Pages](https://img.shields.io/badge/Hosted_on-GitHub_Pages-00C8FF?logo=github)](https://MrGezz.github.io)
[![pyRevit](https://img.shields.io/badge/Built_for-pyRevit_6.4-3776AB?logo=python)](https://github.com/pyrevitlabs/pyRevit)
[![Revit](https://img.shields.io/badge/Revit-2023--2026-0696D7)](#)
![IronPython](https://img.shields.io/badge/IronPython-2.7-306998?logo=python&logoColor=white)

The public documentation site for **`IcZScripts.extension`** — a pyRevit suite of BIM/MEP automation tools for Autodesk Revit 2023–2026. The site explains what each tool does, how it works, and the conventions behind the suite.

🌐 **Live site:** [https://MrGezz.github.io](https://MrGezz.github.io)

---

## Documented Tools

The site documents every tool shipping in the `SuiteTools` ribbon tab:

| Code | Tool | Panel | Purpose |
| --- | --- | --- | --- |
| **AA** | Project QC Automation | IcZ | Full-lifecycle MEP parameter pipeline, phases 0–8 |
| **FIT** | MEP Workbench | IcZ | Fitting Audit · Coordinates · Pipe/Duct→Flex · IFLS Param Sync |
| **PSU** | Project Setup Utility | IcZ | Link worksets · Project Info · Levels · Security Vault |
| **CD** | Model Cleanup | Tools | Ghost Purge + Connection Fix + Nested Resolutions |
| **EXP** | IssueOne | Tools | NWC / PDF / DWG / formatted Excel export via COM |
| **RO1** | BOQ Lookup | Parameter | Size → Nominal Width → BOQ designation/description |
| **WOT** | Wall Opening Report | Report | MEP ↔ wall intersection detection across linked files |

*(The suite also leverages a robust underlying Shared Library, documented below.)*

---

## 🛠️ The `icz` Shared Library

Every pyRevit tool in the suite kept re-solving the same problems: the Revit 2024 `ElementId` breaking change, modeless-window threading, theme duplication, worksharing checks, and MEP connector hashing.

**`icz`** pulls all of it into one dependency-free library, so each tool imports instead of reimplementing.

- ✅ **No external dependencies** — pure Revit API + WPF, nothing else to install.
- ✅ **IronPython 2.7 safe** — avoiding syntax/comprehension edge-cases that trip up IPy.
- ✅ **Revit 2023 → 2026 safe** — version-guarded `ElementId` conversions and category enums.
- ✅ **Single source of truth** — theme palette, MEP tolerances, and UI chrome live in exactly one place.

### Installation & Integration

Drop the `icz` folder into your pyRevit extension's `lib/` directory. pyRevit adds `lib/` to `sys.path` automatically.

```python
from icz import theme, revit_compat, mep, worksets
```

### Module Map

| Module | Purpose |
|---|---|
| `revit_compat` | `ElementId` 32-bit ⇄ 64-bit shim for Revit 2024+. The **one change** every module relies on. |
| `mep` | O(n) spatial-hash connector engine. Matches open pipes/ducts and handles safe auto-connecting. |
| `modeless` | 1-line `ExternalEvent` wrapper (`ExternalCall`). Safely connects WPF clicks to Revit actions. |
| `theme` & `shell` | Single light/dark palette and reusable WPF window chrome (traffic lights, nav-rail). |
| `review_grid` | Click-to-navigate review-grid rows (ID, Name, Badge) used for interactive pre-scan reviews. |
| `mappings` | JSON-driven classification structures loaded from `spec_mappings.json`. |
| `params` | Read/write helpers navigating Instance→Type parameter fallback chains and overwrite policies. |
| `naming` | Safe type-name / family-and-type label resolution (handling `FamilyInstance` ambiguities). |
| `ownership` | Lookup and guarded checking of checkout statuses for workshared models. |
| `worksets` | Utilities to find, create, and assign elements to worksets en masse. |
| `units` | Feet ⇄ millimeter conversions + shared constants (like `MEP_TOLERANCE_FT`). |
| `revit_utils` | Selection/isolation UI helpers and dynamic shared-parameter binding routines. |

### Highlight: `revit_compat` (ElementId Version Shim)

Revit 2024 replaced `ElementId.IntegerValue` with `ElementId.Value` (Int64). This shim allows the codebase to run seamlessly across versions.

```python
from icz.revit_compat import eid, make_eid, is_valid

element_id_int = eid(my_element.Id)         # ElementId -> int, any Revit version
new_id         = make_eid(element_id_int)   # int -> ElementId, any Revit version

if is_valid(my_element):                    # Guards against mid-run element deletions
    pass
```

### Highlight: `modeless` (ExternalEvent Plumbing)

Solves the classic modeless-window trap. Wraps `IExternalEventHandler` so UI click actions run in a valid API context.

```python
from icz.modeless import ExternalCall

class MyWindow(forms.WPFWindow):
    def __init__(self, xaml):
        forms.WPFWindow.__init__(self, xaml)
        self.nav = ExternalCall("Navigate")     # Created in valid API context

    def _on_row_click(self, sender, args):
        def _do(uiapp):                         # Runs safely on Revit's main thread
            uiapp.ActiveUIDocument.ShowElements(ids)
        self.nav.raise_with(_do)
```

---

## 🎨 Site Features & Tech Stack

This documentation site is a single-page, zero-build portal serving as a reference.

* **UI/UX:** Scroll-reveal animations, interactive glassmorphism components, and live code block copying.
* **Navigation:** Sidebar navigation grouped by ribbon panel with interactive scrollspy.
* **Theme Support:** Fully custom Light/Dark theme toggle tied into CSS variables.
* **Tech Stack:** HTML5/CSS3 (No heavy frameworks), Prism.js for code highlights, and FontAwesome 6 for iconography.

## Local Preview

No build step or server required. Just clone and open!

```bash
git clone https://github.com/MrGezz/MrGezz.github.io.git
cd MrGezz.github.io
# Open index.html in any modern browser
```

---
**Maintainer:** [@MrGezz](https://github.com/MrGezz) — BIM Coordinator & Revit API developer.
&copy; 2026 IcZ. Hosted on GitHub Pages.