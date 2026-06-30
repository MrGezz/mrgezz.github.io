# IcZScripts · SuiteTools — Documentation Site

[![GitHub Pages](https://img.shields.io/badge/Hosted_on-GitHub_Pages-00C8FF?logo=github)](https://MrGezz.github.io)
[![pyRevit](https://img.shields.io/badge/Built_for-pyRevit_6.4-3776AB?logo=python)](https://github.com/pyrevitlabs/pyRevit)
[![Revit API](https://img.shields.io/badge/Revit_API-2023--2026-0696D7?logo=autodesk)](#)

The public documentation site for **`IcZScripts.extension`** — a master-crafted pyRevit suite of BIM/MEP automation tools targeting Autodesk Revit 2023–2026. This portal explains the underlying architecture, workflows, and conventions driving the extension.

🌐 **Live site:** [https://MrGezz.github.io](https://MrGezz.github.io)

---

## 🏗️ Architecture of this Repo

This repository serves as a highly optimized, zero-build documentation portal. It is **not** the pyRevit extension's source code; it is the interactive reference manual for the [`SuiteTools`](#documented-tools) ribbon tab.

* **`index.html`** — The entire documentation portal. It features a bespoke Dark/Light design system, scroll-reveal animations, live-filtering, and an integrated clipboard for code blocks. Fully self-contained.
* **`README.md`** — This file.

---

## 🛠️ Documented Tools

The documentation covers every proprietary tool shipping in the `SuiteTools.tab`:

| Code | Tool | Panel | Purpose |
| :--- | :--- | :--- | :--- |
| **AA** | SPEC / CMMN Automation | IcZ | Full-lifecycle MEP parameter pipeline, phases 0–8. |
| **FIT** | MEP Workbench | IcZ | Fitting Audit · Coordinates · Pipe/Duct→Flex · IFLS Param Sync. |
| **PSU** | Project Setup Utility | IcZ | Batch Link worksets · Project Info · Safe Level Reassignment. |
| **CD** | Model Cleanup | Tools | O(n) Ghost Purge + SubTransaction Connection Fix. |
| **EXP** | ExportDocTools | Tools | NWC / PDF / DWG / COM-driven formatted Excel export. |
| **DOC** | MEPAutoDocTools | Tools | Auto-generate plans, sections, dimensions, tags, and sheets. |
| **RO1** | BOQ Lookup | Parameter | Size → Nominal Width → BOQ designation/description engine. |
| **WOT** | Wall Opening Report | Report | Cross-link MEP ↔ wall intersection detection. |

*Note: Includes the shared library `lib/icz/` detailing the framework for `theme`, `review_grid`, `revit_compat`, and safe `modeless` external events.*

---

## ✨ UI / UX Features

The documentation site is engineered for an elite developer experience (DX):

* **Micro-interactions:** Scroll-triggered reveal animations via `IntersectionObserver`.
* **Interactive Code Blocks:** Embedded Python & Shell blocks with Prism.js highlighting and one-click "Copy to Clipboard".
* **Live Search:** Press `/` to focus, type to instantly filter the tool list.
* **Advanced Theming:** Seamless Light / Dark theme toggling using a CSS variable-driven architecture.
* **Glassmorphism:** Frosted top-bars with `backdrop-filter` for a modern application feel.
* **Responsive Design:** Fluid grids and a collapsible mobile drawer navigation menu.

---

## 🚀 Local Development & Preview

Because the site relies on a modern, zero-build HTML/CSS structure, no Node.js or Webpack server is required.

```bash
git clone https://github.com/MrGezz/MrGezz.github.io.git
cd MrGezz.github.io