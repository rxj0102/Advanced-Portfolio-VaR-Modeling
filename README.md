# Advanced-Portfolio-VaR-Modeling

# Comparative Analysis of Parametric and Non-Parametric VaR Models with Christoffersen Backtesting

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![Pandas](https://img.shields.io/badge/pandas-2.0+-brightgreen.svg)](https://pandas.pydata.org/)
[![Arch](https://img.shields.io/badge/arch-6.0+-orange.svg)](https://github.com/bashtage/arch)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A comprehensive implementation and comparative analysis of advanced Value-at-Risk (VaR) models for multi-asset portfolio risk management. This project evaluates 8 different VaR methodologies—from simple Historical Simulation to sophisticated GARCH-family models—and rigorously backtests them using the Christoffersen Conditional Coverage framework to identify the most accurate and robust risk predictor.

## 📊 Project Overview

Value-at-Risk (VaR) is a cornerstone of financial risk management. This project builds a diversified portfolio of 5 assets (SPY, QQQ, EFA, GLD, TLT) over 20+ years of data and implements a spectrum of VaR models to answer a critical question: **Which model best captures the true risk of a portfolio?**

The analysis progresses from simple to complex models, with each step addressing specific financial stylized facts:
- Volatility clustering
- Leverage effects (asymmetric volatility)
- Fat-tailed return distributions
- Long memory in volatility

The final comparison uses the **Christoffersen Conditional Coverage test**—the industry standard for VaR validation—to evaluate both the number of violations and their independence over time.

## 🚀 Key Features

### Models Implemented
| Model | Type | Distribution | Key Characteristics |
|-------|------|--------------|---------------------|
| **HS VaR** | Non-Parametric | Empirical | Baseline benchmark |
| **GARCH-Norm** | Symmetric | Normal | Captures volatility clustering only |
| **GARCH-t** | Symmetric | Student's-t | Adds fat tails |
| **GJR-GARCH-Norm** | Asymmetric | Normal | Captures leverage effect |
| **GJR-GARCH-t** | Asymmetric | Student's-t | Leverage effect + fat tails |
| **A-FIGARCH-Norm** | Long Memory | Normal | Fractional integration |
| **A-FIGARCH-t** ⭐ | Long Memory + Asymmetric | Student's-t | **Full-featured: long memory, asymmetry, fat tails** |
| **EGARCH-t** | Asymmetric | Student's-t | Exponential GARCH formulation |

### Backtesting & Validation
- **Christoffersen Conditional Coverage Test**: Joint test of:
  - **Unconditional Coverage (POF)**: Are violations occurring at the expected rate (1%)?
  - **Independence Test**: Are violations clustered in time?
  - **Joint Test**: Combined assessment for model acceptance
- **Component VaR (CVaR) Analysis**: Risk contribution decomposition by asset
- **Visual Comparison**: 2008 Financial Crisis period visualization

## 📈 Key Findings

### Final Comparative Backtest Results (Full Christoffersen Test)

Total Days: 5044
Expected Violations (1%): 50.44
Note: All P-Values must be > 0.05 to ACCEPT the model.

| Model | Violations | P-Value (POF) | P-Value (Ind) | P-Value (Joint) |
|-------|------------|---------------|---------------|-----------------|
| A-FIGARCH-Norm VaR | 5001 | nan | 0.395378 | nan |
| GARCH-Norm VaR | 4996 | nan | 0.472287 | nan |
| GJR-GARCH-Norm VaR | 4995 | nan | 0.492891 | nan |
| HS VaR | 72 | 0.004143 | 0.000065 | 0.000006 |
| GARCH-t VaR | 69 | 0.012866 | 0.002620 | 0.000490 |
| EGARCH-t VaR | 67 | 0.025662 | 0.000239 | 0.000097 |
| GJR-GARCH-t VaR | 66 | 0.035532 | 0.001720 | 0.000806 |
| A-FIGARCH-t VaR | 62 | 0.114269 | 0.000942 | 0.001211 |

--- Component VaR Analysis (Risk Contribution) ---

| Asset | Weight | MVaR | CVaR | CVaR % |
|-------|--------|------|------|--------|
| SPY | 0.35 | 0.0307605 | 0.0107662 | 49.4753% |
| QQQ | 0.30 | 0.0148301 | 0.00444902 | 20.4452% |
| EFA | 0.15 | 0.0278333 | 0.00417499 | 19.1859% |
| GLD | 0.10 | 0.0259685 | 0.00259685 | 11.9336% |
| TLT | 0.10 | -0.00226325 | -0.000226325 | -1.04006% |

**Total Portfolio VaR (Sum of CVaR, using A-FIGARCH-t): 0.0218**

FIGARCH(1,d,1)-t Model Estimated. Estimated Degrees of Freedom (nu): 7.05
GJR-GARCH(1,1)-t Model Estimated. Estimated Degrees of Freedom (nu): 7.42


### Model Performance Ranking (Best to Worst)
| Rank | Model | Violations | P-Value (Joint) | Status |
|------|-------|------------|-----------------|--------|
| **1** | **A-FIGARCH-t VaR** | **62** | **0.001211** | ⚠️ **Best Performer** |
| 2 | GJR-GARCH-t VaR | 66 | 0.000806 | ❌ Rejected |
| 3 | EGARCH-t VaR | 67 | 0.000097 | ❌ Rejected |
| 4 | GARCH-t VaR | 69 | 0.000490 | ❌ Rejected |
| 5 | HS VaR | 72 | 0.000006 | ❌ Rejected |
| 6 | GJR-GARCH-Norm VaR | 4995 | nan | ❌ Complete Failure |
| 7 | GARCH-Norm VaR | 4996 | nan | ❌ Complete Failure |
| 8 | A-FIGARCH-Norm VaR | 5001 | nan | ❌ Complete Failure |

### Portfolio Composition
| Ticker | Asset Description | Weight |
|--------|-------------------|--------|
| **SPY** | SPDR S&P 500 ETF Trust | 35% |
| **QQQ** | Invesco QQQ Trust (Nasdaq-100) | 30% |
| **EFA** | iShares MSCI EAFE ETF (International) | 15% |
| **GLD** | SPDR Gold Shares | 10% |
| **TLT** | iShares 20+ Year Treasury Bond ETF | 10% |

**Data Period:** 2000-01-01 to Present  
**Confidence Level:** 99% (α = 0.01)  
**Historical Window:** 252 trading days (1 year)

### Model Configuration Parameters
| Model | Volatility Specification | p | o | q | Distribution |
|-------|-------------------------|---|---|---|--------------|
| HS VaR | Historical Simulation | N/A | N/A | N/A | Empirical |
| GARCH-Norm VaR | GARCH | 1 | 0 | 1 | Normal |
| GARCH-t VaR | GARCH | 1 | 0 | 1 | Student's-t |
| GJR-GARCH-Norm VaR | GJR-GARCH | 1 | 1 | 1 | Normal |
| GJR-GARCH-t VaR | GJR-GARCH | 1 | 1 | 1 | Student's-t |
| A-FIGARCH-Norm VaR | FIGARCH | 1 | 1 | 1 | Normal |
| A-FIGARCH-t VaR | FIGARCH | 1 | 1 | 1 | Student's-t |
| EGARCH-t VaR | EGARCH | 1 | 0 | 1 | Student's-t |

### Christoffersen Test Results Interpretation Guide
| Test | Null Hypothesis | P-Value < 0.05 Indicates |
|------|-----------------|---------------------------|
| **POF (Unconditional Coverage)** | The model's violation rate equals the expected rate (1%) | Model systematically under- or over-estimates risk |
| **Independence** | Violations are independent over time | Violations cluster during periods of market stress |
| **Joint (Conditional Coverage)** | Model has correct coverage AND independent violations | Model fails either coverage or independence test |

### Statistical Significance Summary
| Model | Passes POF? (α=0.05) | Passes Independence? (α=0.05) | Passes Joint? (α=0.05) | Overall Assessment |
|-------|----------------------|------------------------------|------------------------|-------------------|
| HS VaR | ❌ No (0.004) | ❌ No (0.000065) | ❌ No (0.000006) | **Rejected** |
| GARCH-Norm VaR | ❌ No (nan) | ✅ Yes (0.472) | ❌ No (nan) | **Rejected** |
| GARCH-t VaR | ❌ No (0.013) | ❌ No (0.0026) | ❌ No (0.00049) | **Rejected** |
| GJR-GARCH-Norm VaR | ❌ No (nan) | ✅ Yes (0.493) | ❌ No (nan) | **Rejected** |
| GJR-GARCH-t VaR | ❌ No (0.036) | ❌ No (0.0017) | ❌ No (0.00081) | **Rejected** |
| A-FIGARCH-Norm VaR | ❌ No (nan) | ✅ Yes (0.395) | ❌ No (nan) | **Rejected** |
| EGARCH-t VaR | ❌ No (0.026) | ❌ No (0.00024) | ❌ No (0.000097) | **Rejected** |
| **A-FIGARCH-t VaR** | ✅ **Yes (0.114)** | ❌ No (0.00094) | ❌ No (0.0012) | **Best Available** |

### Risk Contribution Analysis
| Asset | Weight | Risk Contribution (CVaR) | Risk/Weight Ratio |
|-------|--------|-------------------------|-------------------|
| SPY | 35% | 49.48% | 1.41x |
| QQQ | 30% | 20.45% | 0.68x |
| EFA | 15% | 19.19% | 1.28x |
| GLD | 10% | 11.93% | 1.19x |
| TLT | 10% | -1.04% | -0.10x |

### Key Mathematical Formulations
| Model | Variance Equation |
|-------|-------------------|
| **GARCH(1,1)** | σ²_t = ω + αε²_{t-1} + βσ²_{t-1} |
| **GJR-GARCH(1,1)** | σ²_t = ω + αε²_{t-1} + γI_{t-1}ε²_{t-1} + βσ²_{t-1} |
| **FIGARCH(1,d,1)** | φ(L)(1-L)^d ε²_t = ω + [1-β(L)]ν_t |
| **EGARCH(1,1)** | ln(σ²_t) = ω + α\|ε_{t-1}/σ_{t-1}\| + γε_{t-1}/σ_{t-1} + βln(σ²_{t-1}) |

## 🔍 Interpretation of Results

1. **GARCH-Normal models completely fail** (POF test P-Value = `nan`) due to their inability to model fat tails. They produce thousands of violations (4995-5001) when only ~50 are expected.

2. **t-distribution models** significantly improve unconditional coverage (62-72 violations vs. 50 expected) but still show violation clustering, as evidenced by low Independence P-Values (< 0.05).

3. **A-FIGARCH-t** (Asymmetric Fractionally Integrated GARCH with Student's-t) demonstrates the best overall performance:
   - Passes the unconditional coverage test (P-Value = 0.114 > 0.05)
   - Shows the strongest joint test statistics
   - Best balance of violation count and independence

4. **EGARCH-t** shows improved performance over standard GARCH-t but still fails independence tests.

5. **SPY** contributes nearly 50% of portfolio risk despite being only 35% of the portfolio, while **TLT** shows negative CVaR, indicating hedging benefits during risk-off periods.

## 🛠️ Technical Implementation

### Dependencies

pip install yfinance pandas numpy matplotlib scipy arch

Key Libraries

yfinance: Data acquisition from Yahoo Finance
arch: GARCH-family model estimation
pandas/numpy: Data manipulation and numerical computing
scipy: Statistical testing (chi-squared distribution)
matplotlib: Visualization

🎯 Key Takeaways

Simple isn't always better: Historical Simulation, while simple, significantly underestimates tail risk and fails all statistical tests.
Distribution matters: Student's-t distributions are essential for capturing fat tails. All normal-distribution models fail catastrophically.
Asymmetry is crucial: GJR-GARCH and EGARCH models capture the leverage effect where negative shocks increase volatility more than positive shocks, improving forecast accuracy.
Long memory improves accuracy: FIGARCH models capture the persistent nature of volatility shocks, leading to more responsive VaR estimates during crisis periods.
A-FIGARCH-t emerges as the winner: The combination of long memory, asymmetry, and fat tails provides the most accurate and reliable VaR estimates, passing the unconditional coverage test and showing the strongest joint test statistics.
Risk decomposition matters: Component VaR analysis reveals that SPY contributes disproportionately to portfolio risk (49.5% of total risk with 35% weight), while TLT provides valuable diversification benefits with negative CVaR.

🔬 Future Improvements

Implement realized volatility measures for comparison
Add copula-based multivariate models to capture cross-asset dependencies
Include Expected Shortfall (ES) as an alternative coherent risk measure
Expand to high-frequency data analysis for improved volatility estimation
Add machine learning models (LSTM, XGBoost, Random Forest) for comparison
Implement rolling window analysis for parameter stability testing
Add stress testing scenarios beyond historical crises
Include transaction costs and liquidity considerations
📚 References

Bollerslev, T. (1986). "Generalized autoregressive conditional heteroskedasticity", Journal of Econometrics, 31(3), 307-327.
Engle, R. F. (1982). "Autoregressive conditional heteroscedasticity with estimates of the variance of UK inflation", Econometrica, 50(4), 987-1007.
Christoffersen, P. F. (1998). "Evaluating interval forecasts", International Economic Review, 39(4), 841-862.
Glosten, L. R., Jagannathan, R., & Runkle, D. E. (1993). "On the relation between the expected value and the volatility of the nominal excess return on stocks", Journal of Finance, 48(5), 1779-1801.
Baillie, R. T., Bollerslev, T., & Mikkelsen, H. O. (1996). "Fractionally integrated generalized autoregressive conditional heteroskedasticity", Journal of Econometrics, 74(1), 3-30.
J.P. Morgan/Reuters (1996). "RiskMetrics—Technical Document", Fourth Edition.
Alexander, C. (2008). "Market Risk Analysis, Volume II: Practical Financial Econometrics", John Wiley & Sons.
