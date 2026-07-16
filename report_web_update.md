# Web Update Report — MrGezz.github.io

**Date:** 2026-07-06 · **Last pass:** 2026-07-16
**Scope:** Refresh the SuiteTools documentation site to reflect the current `IcZScripts.extension`, add presentation/interactivity ("wow factor"), expand the shared-library documentation, strip legacy branding, and avoid exposing proprietary code. README aligned to match.

---

## Update pass — 2026-07-16 · Per-tool "How you use it" operations

Added a **usage / operations block to every tool section** (10 tools), presenting each tool's real operations the way the in-app dashboards do (icon + operation name + concise "what it does / how you'd use it" blurb) but elevated beyond the flat in-app cards.

**Content — pulled from source, not invented.** FIT and PSU match their in-app dashboards verbatim (the two reference screenshots); every other tool's operations were read from its `script.py` / `ui.xaml` (nav pages, dashboard cards, `PHASES`/`AUDITS` tables, action buttons):

- **AA** — grouped: QC phase pipeline (Cleanup 0–2, CMMN 3, SPEC 4a–4f, Flags 5–6, IFC Classification 7, Workset Orchestration 8) + the 5 standing audits (Tag, LOI, Model Health, View Filters, Colorize by Value).
- **FIT** — the six engines (Fittings & Accessories Audit, Coordinate Stamping, Flex Conversion, Parameter Synchronization, Prefabrication Auto-Slicer, Spatial Routing Engine).
- **PSU** — Link Worksets, Project Info, Levels (+ the `psu_config.json` note).
- **CD** — Ghost Purge, Connection Fix, Nested Fix, Scan & Commit, Configuration.
- **IssueOne** — Transmit Center, Pick Drawings, Pick 3D & Schedules, Formats, Issue History, PDF Preview, Settings & Profiles.
- **AutoDoc** — Generate Sheets, Layout Profiles, Element Staging, Templates & Auto-Tag, Auto-Dimensioning.
- **RO1** — Scan Model, Run BOQ Lookup, Results, BOQ Data Browser, Data Editor.
- **Guideline** — Browse Standards, Multi-Format Preview, Edit & Save, Notes / Redlines, Folders & Settings.
- **WOT** — Configure & Run, Review Results, Place Openings, Parameter Settings.
- **Family QC** — Scan Family, Health Scorecard, Export Report, Profile Settings.

**Styling — new `.usage` / `.op-grid` / `.op-card` component** (single CSS block, uses the existing design tokens; no restructure). Responsive auto-fill grid of operation cards; each card has an accent-gradient rounded icon badge, bold title and concise blurb, a panel→bg gradient surface, and on hover lifts with an accent border, deepened shadow and a wipe-in 3&nbsp;px accent bar down the left edge. An uppercase "How you use it" header rule (with the step flow, e.g. "Scan → review → Apply") tops each block; AA additionally uses full-width `.op-group` sub-headers ("QC phase pipeline" / "Standing audits"). Matches the site's cyan-accent glass aesthetic and re-tints with the Light/Dark toggle.

**Verification:** re-read post-edit — 10 usage blocks (one per tool section), section tags balanced, one block spot-checked in context for clean nesting. (No headless-browser pass this cycle.)

---

## Update pass — 2026-07-16 · Suite state refresh

Content brought current with the suite as of 2026-07-16 (no restructure — numbers, cards, one new tool, and descriptions updated in place). `README.md` aligned to match.

### 1. New tool documented — Family QC (FQC)

**Family QC** (`QualityCheck.panel`) added as the suite's 10th shipping tool: a modeless health-check for the family open in the family editor, scoring its definition against a firm profile (bundled default + `%APPDATA%` overlay via a schema-generated settings form) and reporting a per-check pass/warn/fail table + overall health number, with a themed HTML export. Added its nav-link, architecture-diagram node (`data-tool="familyqc"`), and a full tool section; wired to `famqa`, `settingsform`, `reporthtml`, `configstore`, `serdes`, `host`.

### 2. Shared-library documentation caught up

- **Canonical JSON path** — `serdes` re-cast from "staged for a future config rewire" to **adopted suite-wide**: with `configstore` it is the one JSON persistence path (deep-clone + single atomic `.tmp→.bak→rename` writer + `read_json` best-effort/fail-loud seed reads). Every tool's config load/save routes through the pair.
- **New module cards + diagram nodes** — `host` (host-application seam + `IcZ.Core.dll` resolver), `availability` (launch-time document guards), `tristate` (hierarchical tri-state selection tree), plus `settingsform` / `reporthtml` nodes.
- **`params`** — now notes `describe()` (per-parameter storage/read-only/modifiable metadata for edit-time validation); consumer tag `WOT` added.
- **`famqa`** re-tagged from "shared" to its real consumer, **FQC**.

### 3. WOT first-consumer features + engine status

- WOT gains a **tri-state element scope** feature (parent toggles ducts/pipes/conduit/tray) and a note that opening dimensions are written only into a genuinely-writable parameter, flagging names that don't resolve (`params.describe` gate).
- **ipy3 rollout** reflected: the "ipy3 pilot tool" line updated to state the suite now runs on the IronPython 3 engine (ipy2.7 retained for Revit ≤2023); WOT was the pilot.

### 4. Metrics

`SHIPPING TOOLS` 9 → **10**, `SHARED MODULES` 32 → **37** (count-up counters + architecture-diagram nodes updated to match). Panels unchanged at 5.

**Verification:** all edits applied via file tools and confirmed by re-read; the diagram/explorer JS reads `data-uses` / `data-mod` / `data-domain` generically, so the new tool and module nodes wire in without JS changes. (No headless-browser pass run this cycle.)

---

## Update pass — 2026-07-11 · Theme & navigation

Three UI changes to `index.html`; README feature list aligned. No content, tool, or module documentation changed.

### 1. CyanogenMod-inspired theme (both modes)

The palette was re-tuned from Deep Navy/GitHub colors to a CyanogenMod/Material identity — the layout was already close, so this was a variable-level restyle, not a rebuild:

- **Dark:** charcoal blue-grey surfaces (`--bg #0B1013`, `--panel #152026`, lines `#283B45`) with the signature CM cyan `#00BCD4` / `#4DD0E1` accents; secondary moved to Material deep purple `#7C4DFF`; status colors to Material (`#4CAF50` / `#FFB300` / `#FF5252`).
- **Light:** Material blue-grey — `#ECEFF1` background, `#263238` text, `#CFD8DC` lines — with cyan-700 `#0097A7` accent (kept dark enough for WCAG contrast on white).
- Hardcoded `#005CC5` in the brand-mark gradient replaced with `#00838F` so nothing off-palette remains.
- Theme choice now **persists in `localStorage`** (`icz-theme`).

### 2. Thin themed scrollbars

10 px grey WebKit scrollbars replaced with 6 px accent-tinted ones (`--sb-thumb`, cyan-mixed, transparent track) plus Firefox `scrollbar-width: thin` / `scrollbar-color`. The thumb re-tints automatically with the theme toggle.

### 3. Collapsible sidebar (desktop icon rail)

- New toggle in the brand row collapses the 288 px sidebar to a **68 px icon rail** — mirroring the suite's own `ShellWindow` collapsible nav-rail pattern.
- Collapsed state: icon-only links with native tooltips (label read from the link's text node, excluding the `tcode` badge), centered active pill with a 3 px cyan inset bar, group dividers instead of labels; search and text hidden.
- State **persists in `localStorage`** (`icz-nav`); `aria-expanded` kept in sync; the `/` search shortcut auto-expands the rail first so search stays reachable.
- Scoped to `@media (min-width: 961px)` — the mobile off-canvas drawer is untouched.

**Verification:** headless Chromium pass — no JS errors; collapse width, tooltips, reload persistence (rail + theme), and `/`-focus behavior all confirmed. Screenshots taken in dark, light and collapsed states.

---

## 1. What changed at a glance

| Area | Before | After |
|---|---|---|
| Tools documented | 7 | **9** |
| Ribbon panels | 4 | **5** |
| Library modules shown | ~13 (6 detailed) | **20** (all, with a filterable explorer) |
| Real code snippets | 31 blocks (internal logic) | **4** curated snippets (patterns/usage, not internals) |
| Interactive elements | scrollspy, search, theme toggle | + architecture diagram, module explorer, count-up metrics |
| Branding | neutral, but sourced from `GmBH`/`DX`/`DEAXO` metadata | fully scrubbed to **IcZ** |

---

## 2. Tools added / updated

**Newly documented**

- **GUIDE — Guideline Library** (`QualityCheck.panel`). One-click access to company standards (PDF/Word) scanned from shared folders. The tool's original tooltip ("A LIST OF STANDARD BY DEAXO") was rewritten to neutral "company standards" language on the public site.
- **DOC — Auto-Doc Tools** (`Tools.panel`). Auto-generates plans, sections, elevations, isometric 3D, dimensions, tags and sheet placement, and enforces naming conventions. Documented its top ≥ cut ≥ bottom view-range hierarchy and its port onto the shared theme + version-safe element Ids.

**Refreshed**

- **IssueOne (EXP)** re-cast from a COM-centric exporter to its current form: six formats (DWG/PDF/NWC/IFC/images/Excel), reusable **profiles v2**, token-based **filename builder**, split output folders, mid-batch **warning suppression**, and a **pure-Python XLSX** path (COM now opt-in only). Tied to the shared `exportcfg`, `xlsxlite`, `perftracker`, `failures`, `lastused` modules.
- **AA, FIT, PSU, CD, RO1, WOT** descriptions tightened to match the current source (e.g. FIT's four pages and per-page gear/Settings view; WOT's review grid + filters + XLS report). **CD corrected to its actual three engines** — Ghost Purge, Connection Fix, Nested Fix (the earlier draft wrongly split the nested engine into "Align" + "Resolve").

### On-site legacy provenance

Per request, the site now documents each tool's origins the way good tool docs credit their legacy scripts. Collapsible **"from script to suite"** cards were added to **AA** (from the ~2,600-line RevitPythonShell macro) and **FIT** (from the WinForms flex converter + the coordinate-filler macro), each with a before → after comparison. A small number of **curated, section-specific code snippets** were also added (AA commit convention, FIT config-over-hardcoding, CD isolate/verify/rollback pattern) — each captioned "illustrative", showing the *shape or public surface* only, never the proprietary algorithm.

---

## 3. "Wow factor" & informativeness

- **Interactive architecture diagram** — a layered view (ribbon → `icz` library → Revit API). Hovering any tool lights up exactly the modules it consumes; hovering a module reverse-highlights its consuming tools. Keyboard-focusable.
- **Module explorer** — all 20 modules as cards grouped by domain (Core, UI/Shell, MEP, Data, Worksharing, Export/IO), each tagged with its consuming tools, 