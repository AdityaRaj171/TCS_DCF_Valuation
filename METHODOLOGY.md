# Methodology

This document explains every formula, assumption, and decision behind the TCS DCF model — written so each choice can be defended, not just reproduced.

## 1. Data source

All financial data is pulled live from Yahoo Finance via `yfinance`, using ticker `TCS.NS` (NSE listing). Four fiscal years of income statement data (FY2023–FY2026) were available and used; the fifth year returned by the API (FY2022) was mostly null and excluded.

## 2. Historical analysis

| Fiscal Year | Revenue (₹) | EBIT Margin | Revenue Growth |
|---|---|---|---|
| FY2023 | 2,254,580,000,000 | 25.59% | — |
| FY2024 | 2,408,930,000,000 | 26.06% | 6.85% |
| FY2025 | 2,553,240,000,000 | 25.90% | 5.99% |
| FY2026 | 2,670,210,000,000 | 24.98% | 4.58% |

**Observation**: revenue growth is decelerating (6.85% → 4.58%) while EBIT margin is roughly stable (~25–26%). This shaped the forecast assumptions below.

## 3. Forecast assumptions

### Revenue growth (tapering)
Rather than flatlining growth at a historical average, growth is tapered from near the latest actual rate down toward the terminal growth rate, reflecting the realistic pattern of a large, maturing company:

| Year | Growth |
|---|---|
| 1 | 5.5% |
| 2 | 5.0% |
| 3 | 4.5% |
| 4 | 4.0% |
| 5 | 3.5% |

### EBIT margin
Held flat at the most recent value (~25%) since historical margins show no clear directional trend to extrapolate.

### Capex, depreciation, working capital
Estimated as a flat % of revenue (4%, 2%, 1% respectively) — typical for an asset-light IT services business with no heavy machinery or inventory needs. **This is the model's weakest assumption** — a refined version would pull these directly from TCS's cash flow statement instead of estimating them.

## 4. Free Cash Flow formula

```
Revenue
  × EBIT margin            = EBIT
  × (1 - tax rate)         = NOPAT
  + Depreciation
  - Capex
  - Change in Working Capital
  = Free Cash Flow (FCF)
```

## 5. WACC (Weighted Average Cost of Capital)

WACC blends the cost of equity and cost of debt, weighted by each one's share of total capital:

```
WACC = (E/V × Re) + (D/V × Rd × (1 - Tax))
```

### Cost of equity — CAPM
```
Re = Risk-free rate + Beta × Equity Risk Premium
   = 7.0% + 0.227 × 5.5%
   = 8.25%
```

- **Risk-free rate (7.0%)**: India's 10-year G-Sec yield at time of analysis
- **Beta (0.227)**: TCS's actual live beta, pulled from Yahoo Finance — notably low, reflecting TCS's stability relative to the broader market (large, contracted-revenue IT services business)
- **Equity risk premium (5.5%)**: standard assumption for Indian equities

### Cost of debt
Estimated at 6.5% (slightly above the risk-free rate, typical for a low-risk corporate borrower).

### Capital weights
TCS's market value of equity (~₹75.8 lakh Cr) vastly outweighs its total debt (~₹1,128 Cr) — debt weight is just ~1.5%. This means WACC sits very close to the cost of equity alone (8.20% vs. 8.25%).

## 6. Terminal value — Gordon Growth Model

```
TV = FCF_Year5 × (1 + g) / (WACC - g)
```

Terminal growth (`g`) is set to 3.5%, matching the tail end of the explicit forecast period to avoid an artificial jump in growth assumptions between Year 5 and "forever." WACC must exceed `g` for the formula to be valid — confirmed here (8.20% > 3.5%).

## 7. Discounting and valuation bridge

Each year's FCF, and the terminal value, is discounted back to present value using:

```
PV = Future Value / (1 + WACC)^n
```

```
Enterprise Value = Σ PV(FCF, Years 1-5) + PV(Terminal Value)
Equity Value = Enterprise Value - Total Debt + Total Cash
Implied Price per Share = Equity Value / Shares Outstanding
```

## Limitations

- **Capex/D&A/working capital** are estimated as flat percentages of revenue, not pulled from actual cash flow statements — the single biggest simplification in this model.
- **Single-stage growth taper**: a more rigorous model might use a two- or three-stage growth structure to better capture the transition to maturity beyond Year 5.
- **WACC sensitivity**: the valuation is highly sensitive to the WACC and terminal growth inputs. The sensitivity table in the notebook shows implied price ranging from roughly ₹2,000 to ₹7,900 across a realistic range of WACC (7–10%) and terminal growth (2.5–4.5%) combinations — this is the model's single largest source of uncertainty, larger than any other assumption.
- **No qualitative risk adjustment**: client concentration, currency exposure, and disruption risk (e.g. from AI-driven shifts in IT services demand) are not explicitly modeled beyond what's captured in Beta.

## Sensitivity table (WACC × Terminal Growth)

| WACC ↓ / Growth → | 2.5% | 3.0% | 3.5% | 4.0% | 4.5% |
|---|---|---|---|---|---|
| 7.0% | 3,680 | 4,206 | 4,942 | 6,046 | 7,887 |
| 7.5% | 3,230 | 3,617 | 4,133 | 4,856 | 5,939 |
| 8.2% | 2,760 | 3,028 | 3,368 | 3,814 | 4,425 |
| 9.0% | 2,370 | 2,557 | 2,784 | 3,069 | 3,435 |
| 10.0% | 2,017 | 2,144 | 2,294 | 2,474 | 2,693 |

*(All values in ₹ per share. Read each row at the matching WACC; the 8.2% row, 3.5% column reproduces the base case of ₹2,761.)*
