# Fund Analysis Engine
### Multi-Criteria Ranking of Emerging Market Equity Funds using TOPSIS and Yuan–Yuan (2023)

**Qaweem M Ahmad** · MSc Finance · University of Nottingham · 2025

---

## Overview

This project implements a quantitative fund evaluation framework developed as part of the Capital Markets Analysis coursework (BUSI4519). It evaluates four actively managed emerging market equity funds against the MSCI Emerging Markets Index across a five-year sample period (January 2020 – October 2025).

The core contribution is a from-scratch Python implementation of two independent multi-criteria decision analysis (MCDA) ranking algorithms, TOPSIS (Hwang & Yoon, 1981) and the Yuan & Yuan (2023) paired competition eigenvector method, applied to a 19-metric evaluation framework spanning: return, risk, drawdown, cost, and ESG dimensions.

---

## Funds Analysed

| Fund | Strategy | ETF Proxy Used |
|---|---|---|
| BlackRock Emerging Markets | GARP, broad diversification | IEMG |
| Fidelity EM ESG | ESG integration, concentrated | EMXF |
| JPMorgan EM Income | Income & quality, defensive | JPEM |
| Schroder Global EM | High-growth, market amplification | GEM |

**Benchmark:** MSCI Emerging Markets Index (EEM proxy)

> The original funds are UK-domiciled OEICs not available on Yahoo Finance. US-listed ETF proxies are used to operationalise the methodology. A production version would substitute Morningstar Direct NAV exports.

---

## Methodology

### Metrics Computed From Scratch

All metrics are derived directly from monthly log returns - no third-party analytics libraries.

**Performance metrics:**
- Effective annualised return: $(e^{\bar{r} \times 12} - 1) \times 100$
- Annualised volatility
- CAPM alpha and beta — estimated by regressing *excess* fund returns on *excess* benchmark returns (correct specification)
- R² (systematic variance share)
- Sharpe ratio, Treynor ratio, Sortino ratio, Information ratio

**Downside metrics:**
- Upside and downside capture ratios
- Maximum drawdown — derived from the log-return wealth index $W_t = e^{\sum r_t}$
- Maximum drawdown duration
- Calmar ratio

### TOPSIS (Hwang & Yoon, 1981)

Five-step methodology implemented as a Python class:

1. Min-max normalisation of each metric to a [0, 1] scale
2. Pillar-weighted matrix
3. Positive Ideal Solution (PIS) and Negative Ideal Solution (NIS)
4. Euclidean distances from PIS and NIS
5. Relative closeness score: $S_i = \frac{D_i^-}{D_i^+ + D_i^-}$

### Yuan & Yuan (2023) Paired Competition

1. **Competition matrix** — for each fund pair $(i, j)$, the weighted proportion of metrics on which fund $i$ outperforms fund $j$
2. **Eigenvector ranking** — principal eigenvector of the competition matrix via power iteration (converges in ~20 steps)

### Pillar Weights

| Pillar | Weight |
|---|---|
| Returns | 40% |
| Risk-Adjusted Performance | 25% |
| Risk & Drawdown | 20% |
| Costs | 10% |
| ESG | 5% |

### Sensitivity Analysis

Rankings re-run under four alternative weighting schemes to test stability:

| Scheme | Returns | Risk-Adj | Risk/DD | Costs | ESG |
|---|---|---|---|---|---|
| Baseline | 40% | 25% | 20% | 10% | 5% |
| Return-Heavy | 60% | 20% | 10% | 5% | 5% |
| Risk-Heavy | 25% | 35% | 30% | 5% | 5% |
| Equal Weights | 20% | 20% | 20% | 20% | 20% |

---

## Key Results

| Fund | TOPSIS Rank | Yuan Rank | Key Driver |
|---|---|---|---|
| JPMorgan EM | 1st (risk-heavy) | 1st–2nd | Lowest downside capture, highest Sharpe & Sortino |
| BlackRock EM | 1st (return-heavy) | 1st–2nd | Stronger short-term ETF proxy returns |
| Schroder EM | 3rd (all schemes) | 3rd (all schemes) | High absolute return, limited alpha |
| Fidelity ESG EM | 4th (all schemes) | 4th (all schemes) | ESG constraints, elevated downside capture |

Fidelity and Schroder rankings are stable across all four weighting schemes. The JPMorgan–BlackRock ordering at the top is sensitive to the return vs risk weight split, consistent with the ETF proxy limitation.

---

## Visualisations

| Chart | Description |
|---|---|
| Cumulative Returns | All funds vs benchmark, 2020–2025 |
| Drawdown Chart | Peak-to-trough from wealth index — JPMorgan notably shallower |
| Rolling Sharpe (12m) | Risk-adjusted return consistency over time |
| TOPSIS Heatmap | Min-max normalised 19-metric input matrix |
| Yuan C-Matrix | Pairwise competition scores across all fund pairs |
| Radar Chart | Average normalised score per pillar |
| Sensitivity Heatmap | Yuan scores and ranks across four weighting schemes |

---

## Project Structure

```
fund-analysis-engine/
│
├── fund_analysis_revised.ipynb   # Main notebook
├── charts/                       # Saved interactive HTML charts
│   ├── chart1_cumulative_returns.html
│   ├── chart2_drawdown.html
│   ├── chart3_rolling_sharpe.html
│   ├── chart4_topsis_heatmap.html
│   ├── chart6_radar.html
│   ├── chart_sensitivity_heatmap.html
│   └── yuan_cmatrix_heatmap.html
├── requirements.txt
├── .gitignore
└── README.md
```

---

## How to Run

**1. Clone the repository**
```bash
git clone https://github.com/YOUR-USERNAME/fund-analysis-engine.git
cd fund-analysis-engine
```

**2. Install dependencies**
```bash
pip install -r requirements.txt
```

**3. Launch the notebook**
```bash
jupyter notebook fund_analysis_revised.ipynb
```

**4. Run all cells**

Data is fetched live from Yahoo Finance on first run. An internet connection is required.

---

## Dependencies

```
yfinance
pandas
numpy
scipy
seaborn
plotly
matplotlib
openpyxl
jupyterlab
```

---

## Academic Context

This project extends the following sources:

- Hwang, C-L. and Yoon, K. (1981) *Multiple Attribute Decision Making*. Springer-Verlag.
- Yuan, J. and Yuan, X. (2023) 'A Comprehensive Method for Ranking Mutual Fund Performance', *SAGE Open*, 13(2).
- Sharpe, W.F. (1964) 'Capital Asset Prices', *Journal of Finance*, 19(3).
- Treynor, J.L. (1965) 'How to Rate Management of Investment Funds', *Harvard Business Review*, 43(1).
- Sortino, F.A. and Satchell, S. (2001) *Managing Downside Risk in Financial Markets*. Butterworth-Heinemann.
- Cremers, M. and Petajisto, A. (2009) 'How Active is Your Fund Manager?', *Review of Financial Studies*, 22(9).

---

## Limitations

- ETF proxies are used in place of actual fund NAV data
- Sample window (2020–2025) does not represent a full EM market cycle
- With n=4 funds, results are illustrative rather than statistically conclusive

---

## Possible Extensions

- Phase 2: Streamlit web app with live data fetching, interactive weight sliders, and PDF report generation
- Phase 3: Package the `TOPSIS` and `YuanYuan` classes as a pip-installable Python library

---

*Capital Markets Analysis · BUSI4519 · University of Nottingham Business School*
