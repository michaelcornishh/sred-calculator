# Cursor Prompts — SR&ED Calculator Feature Extensions

Use these prompts in Cursor (Cmd+K or chat panel) with `index.html` open.
Run them in order — each builds on the previous.

---

## PROMPT 1 — Better PDF export (formatted print report)

Paste this into Cursor chat with index.html open:

```
I have a SR&ED ITC calculator in index.html. I want to improve the PDF export so that when the user clicks "Export PDF" it produces a clean, professional report instead of just printing the screen.

Requirements:
- Add a hidden <div id="print-report"> that is display:none normally but display:block during print
- The report should include: title "SR&ED ITC Analysis", date generated, all input parameters (corp type, expenditure amounts, phase-out metric), the full breakdown table with all buckets, provincial estimates, and a disclaimer footer
- Use @media print CSS to hide the interactive controls and show only the report div
- The report should use a clean white background, black text, Times New Roman or Georgia serif font for a formal feel
- Add a subtitle line that says "Prepared using Canada SR&ED FES 2024 / Budget 2025 rules"
- The disclaimer should read: "This calculator provides estimates only and does not constitute tax advice. Consult a qualified SR&ED tax advisor for filing purposes."
- When the user clicks Export PDF, call window.print() — the browser print dialog handles PDF save
- Make sure all the current computed values (from the JS state) are written into the print-report div before calling window.print()

Keep all existing functionality intact. Only modify the exportPDF() function and add the print-report div + print CSS.
```

---

## PROMPT 2 — Provincial credits full second layer

Paste this into Cursor chat with index.html open:

```
In my SR&ED calculator (index.html), the provincial section shows a static grid of all provinces. I want to replace this with a proper interactive province selector that shows detailed provincial credit calculations.

Requirements:
- Add a dropdown or toggle button group to select the user's province (Ontario, Quebec, BC, Alberta, Manitoba, Saskatchewan, New Brunswick, Nova Scotia, PEI, Newfoundland)
- When a province is selected, show:
  (a) The provincial ITC rate and what it applies to
  (b) The calculated provincial credit amount
  (c) Whether it is refundable or non-refundable
  (d) Any phase-out thresholds specific to that province
  (e) The combined federal + provincial total cash refund
- Ontario OITC: 8% on current expenditures up to $3M, refundable, phases out at taxable capital $25M-$50M and taxable income $500K-$800K
- Quebec: 30% on wages only (not materials/contracts/capital), fully refundable for CCPCs, phases out based on assets
- BC: 10% on all qualified expenditures, non-refundable
- Alberta AITC: 8%, refundable
- Manitoba MRITC: 15%, refundable up to $2M expenditure limit
- All others: use 10% as a conservative placeholder rate
- Add a "Combined total" metric card that shows federal cash refund + provincial cash refund
- Keep the existing provincial grid as a fallback if no province is selected

Update the existing provincial section only. Keep all other functionality intact.
```

---

## PROMPT 3 — Scenario comparison chart

Paste this into Cursor chat with index.html open:

```
In my SR&ED calculator (index.html), there is a scenario toggle that shows old rules ($3M limit) vs new rules ($6M limit) in the breakdown table. I want to add a visual bar chart that makes this comparison clearer.

Requirements:
- When the scenario toggle is ON, show a horizontal bar chart below the breakdown table
- The chart should show side-by-side bars for: ITC earned (old vs new), Cash refund (old vs new), Effective cash rate % (old vs new)
- Use pure CSS for the bars — no Chart.js or external libraries
- Bar colours: new rules bars use --green (#3ecf8e), old rules bars use --text3 (#555)
- Show the dollar value at the end of each bar
- Show a "You gain X more in cash refunds under the new rules" summary line above the chart
- The chart should animate in smoothly using CSS transitions when the scenario toggle is switched on
- If the scenario toggle is OFF, hide the chart entirely
- Keep all existing table comparison rows as well — the chart is additive, not a replacement

Only add the chart section. Do not change any existing HTML structure or JS logic except to call a renderChart() function from within calc().
```

---

## PROMPT 4 — Shareable links (already implemented — enhancement)

Paste this into Cursor chat with index.html open:

```
In my SR&ED calculator (index.html), I have basic URL state encoding that saves params to the URL query string. I want to improve the share link feature.

Requirements:
- When "Copy share link" is clicked, also generate a human-readable summary in the clipboard alongside the URL. Format:
  "SR&ED ITC Calculator — [Corp type], $[current exp] current + $[capital exp] capital = $[cash refund] federal cash refund. View: [URL]"
- Add a second button "Copy summary text" that copies just the text summary without the URL
- If the URL is longer than 200 characters, use base64 encoding of the params JSON instead of raw query params (to keep URLs cleaner)
- Add a "Reset to defaults" link that goes to the base URL with no params
- When loading from a shared URL, show a small banner at the top: "Viewing a shared scenario — [summary of params] — Edit to customise"
- The banner should have an X to dismiss it
- Keep the existing toast notification for "Link copied"
```

---

## PROMPT 5 — Migrate to React (Vite project)

Run this in Cursor terminal first:
```bash
npm create vite@latest react -- --template react
cd react
npm install
```

Then paste this into Cursor chat with the new react/src/App.jsx open:

```
I have a SR&ED ITC calculator built as a single index.html file. I want to migrate it to a proper React component using Vite.

Here is the complete tax logic and design system from the original. Please:

1. Create src/components/SREDCalculator.jsx as the main component with all state managed via useState hooks:
   - corpType: 'ccpc' | 'ecpc' | 'other'
   - phaseMethod: 'capital' | 'revenue'  
   - current: number (default 4000000)
   - capital: number (default 1000000)
   - phaseVal: number (default 0)
   - showScenario: boolean

2. Extract the pure computeITC function into src/utils/sred.js:
   function computeITC(current, capital, phaseVal, corpType, maxLimit)
   Returns: { rows, totalITC, totalRefund, expLimit, notes }
   
   Tax logic:
   - expLimit = maxLimit × (1 − (phaseVal − 15000000) / 60000000), clamped [0, maxLimit]
   - New rules maxLimit = 6000000, old rules = 3000000
   - Current exp within limit: 35% ITC, 100% refundable
   - Capital exp within limit: 35% ITC, 40% refundable  
   - Any exp above limit (CCPC): 15% ITC, 40% refundable
   - Any exp above limit (ECPC/other): 15% ITC, 0% refundable
   - Capital consumes remaining limit after current

3. Create src/utils/urlState.js with:
   - encodeState(state) → URLSearchParams string
   - decodeState(searchString) → state object

4. Apply CSS variables matching the original dark theme. Put global styles in src/index.css.

5. Wire up URL state: use useEffect to sync state to URL on every change, and initialise from URL on mount.

6. Export SREDCalculator as default. Import and render it in App.jsx.

7. The component should be self-contained — no props required, works standalone.

Match the visual design of the original exactly: dark background #0f0f0f, DM Sans font, same card/slider/toggle structure. Import DM Sans via a <link> in index.html.
```

---

## PROMPT 6 — Deploy to GitHub Pages (after React build)

Run these in Cursor terminal:

```bash
# In the react/ directory:
npm install --save-dev gh-pages

# Add to package.json scripts:
# "predeploy": "npm run build"
# "deploy": "gh-pages -d dist"

# Add to vite.config.js:
# base: '/sred-calculator/'

npm run deploy
```

Then paste this in Cursor chat:

```
Update vite.config.js to set base: '/sred-calculator/' so the app works on GitHub Pages at username.github.io/sred-calculator.

Also add a 404.html to the public/ folder that redirects back to index.html so that direct URL sharing (with query params) works correctly on GitHub Pages.

The 404.html should use the standard GitHub Pages SPA redirect script.
```

---

## Tips for using Cursor effectively

1. **Always open the relevant file** before running a prompt — Cursor uses the active file as context
2. **Run one prompt at a time** — don't stack multiple feature prompts together
3. **After each prompt**, open the file in your browser and verify it works before moving to the next
4. **If Cursor breaks something**, use Cmd+Z or Git to revert, then re-run the prompt with more constraints
5. **The tax logic in computeITC() is the critical function** — tell Cursor explicitly not to touch it when adding UI features
6. **For the React migration**, do it in a fresh subfolder — don't try to convert index.html in place
