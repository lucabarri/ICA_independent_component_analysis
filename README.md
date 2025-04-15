# Independent Component Analysis (ICA) on French Market Data

**Authors:** Luca Barriviera, Carl-Emil Krarup Jensen

## Overview

This project applies Independent Component Analysis (ICA), specifically using the JADE algorithm, to analyze French stock market data (CAC40 constituents) from the period 2001–2010. The methodology is inspired by the paper "A First Application of Independent Component Analysis to Extracting Structure from Stock Returns" by Back et al. (1997).

The primary goals are:
1.  Preprocess the stock price data, including handling missing values via regression imputation.
2.  Verify necessary assumptions for ICA/PCA by calculating log returns and checking for stationarity (ADF, KPSS tests) and non-Gaussianity (QQ-plots).
3.  Decompose the log returns into underlying components using ICA (JADE) and Principal Component Analysis (PCA).
4.  Analyze the dominant independent components and their relation to major financial events.
5.  Reconstruct the CAC40 index price using a subset of the dominant ICA and PCA components (both with and without thresholding) and compare the reconstructions against the actual index price.

## Methodology

1.  **Data Loading & Preparation:**
    *   Loaded daily stock prices for CAC40 constituents (2001-2010) and the CAC40 index itself.
    *   Identified stocks with missing data (`Credit Agricole SA`, `Electricite de France`, `Gaz de France SA`, `Suez SA`).

2.  **Missing Data Imputation:**
    *   Implemented regression-based imputation: For each column with missing values, a linear regression model was trained on the non-missing portion using other stocks (mean-imputed) as predictors. This model was then used to predict and fill the missing values.
    *   Visualized the imputed data segments alongside the observed data for affected stocks.

3.  **Log Returns Calculation:**
    *   Calculated daily log returns for each stock to work with additive components and approximate stationarity: `log_return = log(price_t) - log(price_{t-1})`.

4.  **Stationarity & Non-Gaussianity Checks:**
    *   Performed Augmented Dickey-Fuller (ADF) and Kwiatkowski-Phillips-Schmidt-Shin (KPSS) tests on the log return series of each stock to check for stationarity. (Result: Most series deemed stationary).
    *   Generated QQ-plots for each log return series to visually assess non-Gaussianity (Result: Heavy tails observed, indicating non-Gaussianity).

5.  **Component Analysis:**
    *   **ICA:** Applied the JADE algorithm (`jadeR` implementation) to the matrix of log returns to extract independent components (ICs).
    *   **PCA:** Applied Principal Component Analysis (PCA) to the log returns for comparison.

6.  **Analysis of Components:**
    *   Identified the top 5 dominant ICs based on the L∞ norm (maximum absolute value).
    *   Plotted the centered dominant ICs over time, overlaying markers for significant financial events (e.g., 9/11, Lehman Bankruptcy).

7.  **CAC40 Index Reconstruction:**
    *   Focused on data post-July 23, 2008, to avoid periods affected by imputation.
    *   Used the inverse ICA mixing matrix (A = W⁻¹) and the PCA loadings/scores.
    *   Reconstructed stock returns using the top *n* dominant components (ICs or PCs). Tested with *n*=1 and *n*=5.
    *   Applied an element-wise threshold to the components (ICs and PCA scores) to retain only significant fluctuations (e.g., |value| >= 0.4). Reconstructed using the top 5 thresholded components.
    *   Calculated the reconstructed CAC40 log returns using the official index weights.
    *   Converted reconstructed log returns back to price levels using the initial price and `exp(cumsum(log_returns))`.
    *   Compared the reconstructed prices (ICA vs. PCA, with/without thresholding) against the actual CAC40 price trajectory.

## Key Results & Visualizations

-   Successful imputation of missing stock data using linear regression.
-   Confirmation that log returns are largely stationary and non-Gaussian, validating the use of ICA/PCA.
-   Visualization of dominant ICs shows distinct patterns and responses to major financial crises.
-   Comparison plots show that both ICA and PCA can approximate the CAC40 index, but ICA (especially with thresholding) may capture large shocks differently than PCA. The quality of reconstruction depends on the number of components used and the threshold applied.

![039d3195-62c6-4138-9dff-37051a5eacd6](https://github.com/user-attachments/assets/dde41db8-6b87-419d-bebc-17e24ac074e4)
![0557902b-afca-49e5-9331-c439f5fe66b2](https://github.com/user-attachments/assets/704690b2-29bf-41a0-8dc2-f213a87b83ee)

## Discussion Points

-   The choice of imputation method (linear regression) is simple but potentially less accurate than more complex models.
-   While most stocks passed stationarity tests, minor deviations (2 stocks failing KPSS) exist. More robust normalization could be explored.
-   Non-Gaussianity checks were primarily visual (QQ-plots); formal statistical tests could be added.
-   Comparing different ICA algorithms (e.g., FastICA) could assess the robustness of the extracted components.
-   ICA appears potentially better suited than PCA for capturing sharp, non-Gaussian market movements (shocks) when reconstructing the index from thresholded components.


## Reference

Back, K., & Weigend, A. S. (1997). *A first application of independent component analysis to extracting structure from stock returns*. International Journal of Neural Systems, 8(04), 473-484.

This project is part of the course Allocation d'actifs et arbitrage multi-asset from Professor Jean-Gabriel Attali. 
