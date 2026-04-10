# SR&ED ITC Calculator — Product Requirements Document

## Overview

A client-side web calculator for modelling Canadian SR&ED (Scientific Research & Experimental Development) federal investment tax credits under the 2024 Fall Economic Statement and Budget 2025 rules. No backend. No auth. Pure HTML/CSS/JS that works offline.

## Current state (index.html — already built)

The base file (`index.html`) is complete and working. It includes:

- Corporation type selector (CCPC / ECPC / Other)
- Sliders for current expenditures (wages, materials) and capital expenditures (equipment)
- Phase-out calculator with taxable capital or gross revenue toggle
- Scenario comparison toggle (old $3M limit vs new $6M limit)
- Federal ITC breakdown table with per-bucket math
- Provincial credit estimates panel
- PDF export via window.print()
- Shareable URL encoding via URLSearchParams
- Reset button
- Dark theme, DM Sans typography

## Tax logic (do not change without review)

### Expenditure limit formula
```
expLimit = maxLimit × (1 − (phaseMetric − 15,000,000) / 60,000,000)
clamped to [0, maxLimit]
```
- New rules maxLimit: $6,000,000
- Old rules maxLimit: $3,000,000
- Phase-out range: $15M → $75M (both taxable capital and gross revenue methods)

### ITC rate + refundability matrix

| Bucket | ITC rate | Refundable |
|---|---|---|
| Current exp, within limit (CCPC/ECPC) | 35% | 100% |
| Capital exp, within limit (CCPC/ECPC) | 35% | 40% |
| Any exp, above limit (CCPC) | 15% | 40% |
| Any exp, above limit (ECPC) | 15% | 0% |
| Any exp (other corp) | 15% | 0% |

Capital expenditures consume remaining limit AFTER current expenditures.

### Provincial rates (on current expenditures, approximate)
- Ontario: 8% (OITC)
- Quebec: 30% (wages only)
- British Columbia: 10%
- Alberta: 8% (AITC)
- Manitoba: 15% (MRITC)
- Nova Scotia: 15% (NSRITC)

---

## Planned feature extensions (implement with Cursor prompts below)

### Feature 1: PDF export — formatted report
### Feature 2: Provincial credits — full second layer with province selector
### Feature 3: Scenario comparison — visual chart showing old vs new delta
### Feature 4: Shareable links — URL state encoding (already done in base)
### Feature 5: React component — migrate to React + Vite

---

## Design tokens

```css
--bg: #0f0f0f
--surface: #1a1a1a
--surface2: #222
--border: rgba(255,255,255,0.08)
--border2: rgba(255,255,255,0.14)
--text: #f0ede8
--text2: #888
--text3: #555
--green: #3ecf8e
--amber: #f5a623
--blue: #4f9cf9
--red: #e05252
--radius: 10px
--radius-lg: 14px
```

Font: DM Sans (Google Fonts), DM Mono for numbers.

---

## File structure

```
sred-calculator/
├── index.html          ← base calculator (complete)
├── PRD.md              ← this file
├── PROMPTS.md          ← Cursor prompts for each feature
└── react/              ← created when migrating to React
    ├── package.json
    ├── vite.config.js
    └── src/
        ├── main.jsx
        ├── App.jsx
        └── components/
            └── SREDCalculator.jsx
```

---

## Constraints

- No external API calls
- No user data stored anywhere
- Numbers must be formatted with toLocaleString / toFixed — no raw floats displayed
- All tax logic lives in one pure function `computeITC(current, capital, phaseVal, corpType, maxLimit)` — keep it pure
- Print styles must produce a clean white-background report
