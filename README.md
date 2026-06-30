# IcZScripts · SuiteTools — Documentation Site

[![GitHub Pages](https://img.shields.io/badge/Hosted_on-GitHub_Pages-00C8FF?logo=github)](https://MrGezz.github.io)
[![pyRevit](https://img.shields.io/badge/Built_for-pyRevit_6.4-3776AB?logo=python)](https://github.com/pyrevitlabs/pyRevit)
[![Revit](https://img.shields.io/badge/Revit-2023--2026-0696D7)](#)

The public documentation site for **`IcZScripts.extension`** — a pyRevit suite of BIM/MEP automation
tools for Autodesk Revit 2023–2026. The site explains what each tool does, how it works, and the
conventions behind the suite.

🌐 **Live site:** [https://MrGezz.github.io](https://MrGezz.github.io)

---

## What this repo is

This repository hosts a single-page, zero-build documentation portal. It is **not** the extension's
source code — it is the reference site that documents the [`SuiteTools`](#documented-tools) ribbon tab.

* `index.html` — the entire documentation portal (sidebar nav, live search, theme toggle, per-tool
  cards, code samples). Fully self-contained; only CDN dependencies are FontAwesome and Prism.js.
* `README.md` — this file.

---

## Documented tools

The site documents every tool shipping in the `SuiteTools.tab`:

| Code | Tool | Panel | Purpose |
| --- | --- | --- | --- |
| **AA** | SPEC / CMMN Automation | GmBH | Full-lifecycle MEP parameter pipeline, phases 0–8 |
| **FIT** | MEP Workbench | GmBH | Fitting Audit · Coordinates · Pipe/Duct→Flex · IFLS Param Sync |
| **PSU** | Project Setup Utility | GmBH | Link worksets · Project Info · Levels · Security Vault |
| **CD** | Model Cleanup | Tools | Ghost Purge + Connection Fix |
| **EXP** | ExportDocTools | Tools | NWC / PDF / DWG / formatted Excel export |
| **DOC** | MEPAutoDocTools | Tools | Auto-generate plans, sections, elevations, dims, tags, sheets |
| **RO1** | BOQ Lookup | Parameter | Size → Nominal Width → BOQ designation/description |
| **WOT** | Wall Opening Report | Report | MEP ↔ wall intersection detection across links |
| **GUIDE** | Guidelines | QualityCheck | One-click standards (PDF/DOCX) browser |

Plus the shared library `lib/icz/` — `theme`, `review_grid`, `revit_compat`, `modeless`.

---

## Site features

* **Sidebar navigation** grouped by ribbon panel, with scrollspy highlighting the active tool.
* **Live search** — press `/` to focus, type to filter the tool list; `Esc` clears.
* **Light / Dark theme toggle** using the AA_ deep-navy & cyan palette as the base.
* **Syntax-highlighted code** (Python / shell) via Prism.js.
* **Responsive** — collapsible drawer nav on mobile.

---

## Local preview

No build step or server required:

```bash
git clone https://github.com/MrGezz/MrGezz.github.io.git
cd MrGezz.github.io
# open index.html in any browser
```

Or serve it locally if you prefer:

```bash
python -m http.server 8080
# visit http://localhost:8080
```

---

## Tech stack

* **HTML5 / CSS3** — custom properties for theming, CSS grid layout, no framework.
* **Prism.js** — Python / Bash code highlighting.
* **FontAwesome 6** — iconography and ribbon-style glyphs.

---

## Maintainer

**@MrGezz** — BIM Coordinator & Revit API developer.
GitHub: [github.com/MrGezz](https://github.com/MrGezz)

&copy; 2026 IcZ. Hosted on GitHub Pages.
