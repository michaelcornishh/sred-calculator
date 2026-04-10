# SR&ED ITC Calculator

Canadian SR&ED federal investment tax credit calculator — 2024 Fall Economic Statement + Budget 2025 rules.

## Quick start

Open `index.html` in any browser. No install, no server, no dependencies.

## Files

| File | Purpose |
|---|---|
| `index.html` | Complete working calculator |
| `PRD.md` | Full product requirements and tax logic reference |
| `PROMPTS.md` | Cursor prompts to add PDF export, provincial credits, chart, React migration, and deployment |

## Tax rules implemented

- CCPC / ECPC / Other corporation types
- $6M expenditure limit (new) vs $3M (old) scenario comparison
- 35% enhanced ITC (fully refundable for current exp, 40% refundable for capital exp)
- 15% basic ITC (40% refundable for CCPC above limit)
- Phase-out: $15M–$75M taxable capital or gross revenue (elected)
- Capital expenditure eligibility restored (post Dec 15, 2024)
- Provincial credit estimates: ON, QC, BC, AB, MB, NS

## Features

- Shareable URLs (state encoded in query params)
- PDF export via browser print
- Scenario comparison (old vs new rules)
- Dark mode, mobile responsive

## Extending with Cursor

See `PROMPTS.md` for step-by-step prompts to add features or migrate to React.
