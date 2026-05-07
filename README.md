# STR Investment Property Scenario Builder

A self-contained, single-file web app for modeling the full financial picture of buying a short-term rental (Airbnb) property — cash flow, tax strategy, and long-term wealth building.

Built for evaluating whether the **STR Loophole + Cost Segregation + 1031 Exchange** strategy makes financial sense vs. keeping capital in index funds.

**Live:** [str-investment.vercel.app](https://str-investment.vercel.app) *(update with actual URL)*

---

## Features

### Tabs

| Tab | What it shows |
|-----|---------------|
| **Summary** | Cards + acquisition table + annual income/expense breakdown |
| **Cash Flow** | Year 1 (bonus depreciation) vs. Year 2+ side-by-side |
| **Tax Analysis** | Strategy status, comparison table, full tax calculations |
| **5-Year Projection** | Revenue/expense growth, after-tax cash flow by year |
| **Wealth Building** | 30-year RE vs. index fund comparison + 1031 exchange + generational transfer |

### Tax Strategies Modeled

- **STR Loophole (IRC §469)** — avg stay ≤7 days + material participation → paper losses offset W-2 income directly
- **Real Estate Professional Status (REPS)** — 750+ hrs/yr across all properties → ALL rental losses (STR + LTR) non-passive
- **Bonus Depreciation** — 2025 rate is 40% under TCJA; configurable up to 100% if legislation restores it
- **Cost Segregation** — reclassifies building components into 5-yr / 15-yr / 27.5-yr buckets; bonus dep applies to 5-yr and 15-yr
- **QBI Deduction (§199A)** — 20% of net rental income; at high incomes limited to 2.5% of combined building basis
- **Existing LTR** — models a second long-term rental property; losses only unlock via REPS (not STR Loophole)

### Wealth Building Tab

- Side-by-side 30-year projection: real estate equity + reinvested tax savings vs. same cash in index funds
- **1031 Exchange modeling** — at a configurable year, equity is rolled into a larger property; depreciation and tax savings scale up; deferred tax carries forward
- **Step-up in basis at death (IRC §1014)** — shows exactly how much deferred recapture/cap gains tax your heirs never pay, and their new depreciation basis

### Scenario Manager

Save, load, export (JSON), and import scenarios using the sidebar panel. Scenarios are stored in `localStorage`.

---

## Usage

No build step. Open `scenario_builder.html` directly in a browser, or visit the Vercel URL.

```bash
open scenario_builder.html
```

All inputs are in the left sidebar. Results update live on every change.

---

## Tax Assumptions

- **Federal brackets:** 2025 MFJ / Single rates
- **Standard deduction:** $30,000 MFJ / $15,000 Single
- **Depreciation recapture:** 25% (§1250)
- **Long-term capital gains:** 20%
- **State tax:** configurable; NYC combined is ~10.7% (6.85% NY State + 3.876% NYC city)
- **Passive loss phase-out:** $25K allowance fully phased out above $150K MAGI (modeled correctly for high earners)

---

## Not Financial or Legal Advice

This tool is for planning and exploration purposes only. Consult a CPA and/or real estate attorney before making investment decisions.
