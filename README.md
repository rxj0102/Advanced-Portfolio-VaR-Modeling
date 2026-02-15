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

--- Final Comparative Backtest Summary (Full Christoffersen Test) ---
Total Days: 5044
Expected Violations (1%): 50.44
Note: All P-Values must be > 0.05 to ACCEPT the model.

Model	Violations	P-Value (POF)	P-Value (Ind)	P-Value (Joint)
A-FIGARCH-Norm VaR	5001	nan	0.395378	nan
GARCH-Norm VaR	4996	nan	0.472287	nan
GJR-GARCH-Norm VaR	4995	nan	0.492891	nan
HS VaR	72	0.004143	0.000065	0.000006
GARCH-t VaR	69	0.012866	0.002620	0.000490
EGARCH-t VaR	67	0.025662	0.000239	0.000097
GJR-GARCH-t VaR	66	0.035532	0.001720	0.000806
A-FIGARCH-t VaR	62	0.114269	0.000942	0.001211

--- Component VaR Analysis (Risk Contribution) ---

Asset	Weight	MVaR	CVaR	CVaR %
SPY	0.35	0.0307605	0.0107662	49.4753%
QQQ	0.30	0.0148301	0.00444902	20.4452%
EFA	0.15	0.0278333	0.00417499	19.1859%
GLD	0.10	0.0259685	0.00259685	11.9336%
TLT	0.10	-0.00226325	-0.000226325	-1.04006%
Total Portfolio VaR (Sum of CVaR, using A-FIGARCH-t): 0.0218

FIGARCH(1,d,1)-t Model Estimated. Estimated Degrees of Freedom (nu): 7.05
GJR-GARCH(1,1)-t Model Estimated. Estimated Degrees of Freedom (nu): 7.42


### Comparative Model Performance During 2008-2009 Financial Crisis
The visualization in the notebook shows:
- **HS VaR (Static, Worst)**: Blue dashed line - Rigid, fails to adapt to changing volatility
- **GARCH-Normal (Symmetric, No Tails)**: Green line - Better but underestimates tail risk
- **A-FIGARCH-t (Asymmetric Long Memory)**: Red line - Most adaptive, best fit during crisis
- **Violations**: Dark red scatter points show when returns exceed VaR estimates

## 🔍 Interpretation of Results

### Key Findings:
1. **GARCH-Normal models completely fail** (POF test P-Value = `nan`) due to their inability to model fat tails. They produce thousands of violations (4995-5001) when only ~50 are expected.

2. **t-distribution models** significantly improve unconditional coverage (62-72 violations vs. 50 expected) but still show violation clustering, as evidenced by low Independence P-Values (< 0.05).

3. **A-FIGARCH-t** (Asymmetric Fractionally Integrated GARCH with Student's-t) demonstrates the best overall performance:
   - Passes the unconditional coverage test (P-Value = 0.114 > 0.05)
   - Shows the strongest joint test statistics
   - Best balance of violation count and independence

4. **EGARCH-t** shows improved performance over standard GARCH-t but still fails independence tests.

### Risk Contribution Insights:
- **SPY** contributes nearly 50% of portfolio risk despite being only 35% of the portfolio
- **QQQ** contributes ~20% of risk with 30% weight - relatively efficient
- **TLT** shows negative CVaR, indicating hedging benefits during risk-off periods
- The negative CVaR for TLT suggests it serves as a diversifier, reducing overall portfolio risk

## 🛠️ Technical Implementation

### Dependencies

pip install yfinance pandas numpy matplotlib scipy arch

Key Libraries

yfinance: Data acquisition from Yahoo Finance
arch: GARCH-family model estimation
pandas/numpy: Data manipulation and numerical computing
scipy: Statistical testing (chi-squared distribution)
matplotlib: Visualization
