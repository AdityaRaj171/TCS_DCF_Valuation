# TCS DCF Valuation Model

A Discounted Cash Flow (DCF) model built from scratch in Python to estimate the intrinsic value of Tata Consultancy Services (TCS) and compare it against the current market price.

## Result

|Metric|Value|
|-|-|
|WACC|8.20%|
|Enterprise Value|₹9,69,152 Cr|
|Equity Value|₹9,98,949 Cr|
|**DCF Implied Price per Share**|**₹2,760.99**|
|Current Market Price|₹2,094.70|
|**Implied Upside**|**+31.81%**|

> Note: the model implies TCS is undervalued under these assumptions — see \[Methodology](docs/METHODOLOGY.md#limitations) for why this conclusion is sensitive to WACC and growth assumptions, and what a sensitivity table shows.

## What this project does

1. Pulls TCS's real financial data (income statement, market data) live via `yfinance`
2. Analyzes historical revenue growth and EBIT margin trends (FY2023–FY2026)
3. Projects 5 years of Free Cash Flow using a tapering revenue growth assumption
4. Calculates WACC from first principles — CAPM for cost of equity, blended with cost of debt
5. Estimates Terminal Value using the Gordon Growth Model
6. Discounts all cash flows to present value → Enterprise Value → Equity Value → Implied Price per Share
7. Builds a sensitivity table (WACC × Terminal Growth) showing how the valuation swings
8. Visualizes projected Revenue/FCF and DCF price vs market price

## Repository structure

```
tcs-dcf-valuation/
├── notebooks/
│   └── TCS\_DCF\_Model.ipynb    Full model, run top to bottom in Colab
├── docs/
│   └── METHODOLOGY.md          Detailed walkthrough of every formula and assumption
├── images/
│   └── ...                     Chart exports
├── requirements.txt
└── README.md
```

## How to run

**Option A — Google Colab (recommended, zero setup)**
Open `notebooks/TCS\_DCF\_Model.ipynb` directly in Colab and select Runtime → Run all. No API key needed; `yfinance` pulls public data live.

**Option B — Locally**

```bash
git clone https://github.com/AdityaRaj171/tcs-dcf-valuation.git
cd tcs-dcf-valuation
pip install -r requirements.txt
jupyter notebook notebooks/TCS\_DCF\_Model.ipynb
```

## Key assumptions

|Assumption|Value|Rationale|
|-|-|-|
|Revenue growth (Yr 1–5)|5.5% → 3.5% (tapering)|Reflects TCS's slowing historical growth (6.8% → 4.6% over FY24–26)|
|EBIT margin|\~25% (held flat)|Stable historically, no clear directional trend|
|Tax rate|25%|Recent historical average|
|Capex / D\&A / ΔWC|4% / 2% / 1% of revenue|Typical for an asset-light IT services business|
|Risk-free rate|7%|India 10-Year G-Sec yield|
|Equity risk premium|5.5%|Standard assumption for Indian equities|
|Beta|0.227|TCS's actual live beta — reflects low volatility vs. market|
|Terminal growth|3.5%|Matches tail-end of revenue taper, avoids discontinuity|

Full derivations and formulas are in [docs/METHODOLOGY.md](docs/METHODOLOGY.md).

## Tools used

Python · pandas · numpy · yfinance · matplotlib · Google Colab

## Author

Aditya Raj

&#x20;

