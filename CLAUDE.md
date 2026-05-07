# Investment Property Scenario Builder — Claude Instructions

## What This Is

A single self-contained HTML file (`scenario_builder.html`) — no framework, no build step, no dependencies. All logic, styles, and markup live in one file. Deployed to Vercel as a static site via `vercel.json` rewrite.

## Owner Context

- **Lou** earns $400K/yr W-2 at Salesforce (37% federal marginal rate)
- **Cynthia** is the property manager (potential REPS candidate)
- Combined state+city marginal rate: ~10.7% (NYC residents)
- Combined marginal rate: ~47–48%
- They own a NYC duplex: live in one half, LTR the other at ~$3,300/mo
- Target: Long Island Airbnb property using the STR loophole strategy

## File Structure

```
scenario_builder.html   — entire app (HTML + CSS + JS in one file)
vercel.json             — rewrites / → scenario_builder.html
README.md               — public-facing docs
CLAUDE.md               — this file
.gitignore
```

## Architecture

### Layout
- **Sidebar** (350px fixed): all inputs, grouped into collapsible sections
- **Main panel**: tabs → Summary | Cash Flow | Tax Analysis | 5-Year Projection | Wealth Building

### Key Patterns

**Tooltip system** (click-based):
```html
<span class="tip-wrap">
  <span class="tip-icon">?</span>
  <div class="tip-box">Explanation text here</div>
</span>
```
Never use `title=` attributes for tooltips — they don't work with the custom click handler.

**Input helpers:**
```javascript
const $ = id => document.getElementById(id);
const v = id => parseFloat($(id).value) || 0;   // numeric
const ck = id => $(id).checked;                  // checkbox
const sl = id => $(id).value;                    // select / string
```

**Table builder:**
```javascript
setTbody('table-id', [
  ['Label', fm(value)],          // normal row
  ['Label', fm(value), true],    // bold/total row
  ['Label', fm(value), false, 'c-pos'],  // with CSS class on value cell
  null,                           // separator row
  ['GRP', 'Section Header'],     // group header row
]);
```

**Money formatter:** `fm(n, decimals=0)` — returns `$1,234` or `-$1,234`

**Color class:** `cc(n)` → `'c-pos'` if n ≥ 0, `'c-neg'` if n < 0

### CSS Variables
```css
--primary: #1e40af    --primary-lt: #dbeafe
--green: #15803d      --green-lt: #dcfce7
--red: #b91c1c        --red-lt: #fee2e2
--amber: #b45309      --amber-lt: #fef3c7
--muted: #5a6a82      --border: #dde3ec    --r: 8px
```

### Tax Logic — Critical Details

Two separate offset flags:
```javascript
const canOffSTR = strActive || useREPS;  // STR loss offsets W-2
const canOffLTR = useREPS;               // LTR loss ONLY via REPS (not STR Loophole)
```

Baseline tax = W-2 + LTR passive income (state before buying STR). Tax "savings" = baseline − new tax. If REPS also unlocks trapped LTR losses, that benefit is captured automatically.

At $400K+ AGI: $25K passive loss allowance is 100% phased out. Without STR Loophole or REPS, LTR losses are completely trapped.

### Key Results Object (`r`) Properties

| Property | Description |
|----------|-------------|
| `r.pp` | Purchase price |
| `r.loan` / `r.down` / `r.closing` | Financing |
| `r.totCash` | Total cash to close (down + closing + cost seg fee) |
| `r.bldgBasis` | Depreciable building basis |
| `r.dep1Total` / `r.dep2` | Depreciation Year 1 / Year 2+ |
| `r.bonusPct` | Bonus depreciation rate (0–1) |
| `r.t1.savings` / `r.t2.savings` | Tax savings Year 1 / Year 2+ |
| `r.cfPreTax` | Pre-tax annual cash flow |
| `r.w2` / `r.stateR` / `r.filing` | Tax parameters |
| `r.ltrTaxInc` / `r.ltrBasis` | LTR taxable income & basis |
| `r.ratePct` / `r.termYrs` | Loan parameters |
| `r.years` | Array of 5-year projection data |

Note: `landPct` is NOT in `r` — use `v('landPct')/100` directly.

### Scenario Manager

Saves to `localStorage` key `str_scenarios_v1`. `getAllInputs()` captures every `input`/`select`/`checkbox` with an ID — new inputs added to the HTML are automatically included in save/load.

### Wealth Building Tab

- Config inputs: `wbApp`, `wbStock`, `wbReinv`, `wb1031`, `wbYears` (in static HTML, always in DOM)
- `loanBalance(principal, ratePct, termYrs, yearOfLoan)` — amortization helper
- `calculateWealth(r)` — runs the 30-year RE vs. stock simulation
- `renderWealth(r)` — writes to `#wealth-content`
- 1031 exchange: equity / downPct = new property value; fresh `curBldgBasis`; scale factor drives larger depreciation/savings
- Deferred tax: `totalDepClaimed × 25%` + `appreciation × 20%` + state; erased at death via §1014

## Deployment

Static site on Vercel. No build step.

```bash
vercel --prod   # deploy from project root
```

The `vercel.json` rewrite serves `scenario_builder.html` at `/`.

## Adding New Features

1. **New sidebar input**: add inside the appropriate `<div class="sec-bd">`, give it an ID, call `oninput="calc()"`. It's automatically saved to scenarios.
2. **New tab**: add a `<div class="tab" onclick="showTab('name',this)">` button, a `<div id="pane-name" class="tab-pane">` pane, and a `renderMyTab(r)` call inside `calc()`.
3. **New tax strategy**: add the checkbox to the Tax Strategy sidebar section, read it in `calculateResults()`, apply it to the `withTax()` inner function.

## DO NOT

- Add a build system, bundler, or npm packages
- Split into multiple files
- Use frameworks (React, Vue, etc.)
- Add `title=` attributes to `.tip-icon` spans — they don't work as tooltips here
