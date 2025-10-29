# fuzzycopulas
Fuzzy copulas
Repository Purpose: To provide replication materials for the paper

“Fuzzy Bootstrap Inference of Copula-Based Dependence between Gold and Oil Returns”
submitted to Symmetry (MDPI).
1. Overview

This repository contains minimal replication materials supporting the empirical results reported in Appendix B of the paper.
It allows readers to reproduce the fuzzy bootstrap estimation of copula parameters, Kendall’s τ, and Spearman’s ρ for Gold and Oil daily returns (2015–2025).

The provided code reproduces:

Fuzzy α-cut bands (68 % and 95 %) for copula parameters
2. Data Description

Data were obtained from Yahoo Finance using the quantmod R package.

Variable	Ticker	Description	Source	Frequency	Period
Gold	GC=F	Gold Futures (continuous contract)	Yahoo Finance	Daily	2015-01-01 – 2025-01-01
Oil	CL=F	Crude Oil Futures (continuous contract)	Yahoo Finance	Daily	2015-01-01 – 2025-01-01

Data were retrieved on October 29, 2025.

Daily log-returns were computed using:

gold <- dailyReturn(Cl(`GC=F`))
oil  <- dailyReturn(Cl(`CL=F`))
data <- na.omit(cbind(gold, oil))
3. Software Environment

R version: 4.4.1

Main packages:

quantmod (for data retrieval)

copula (for copula fitting and simulation)

ggplot2, dplyr (for visualization and summary)

Random seed for all bootstrap replications:

set.seed(123)
4. Key Functions (Code Excerpts)

Only the essential functions are provided below.
Full scripts can be shared upon request to ensure file size limits and focus on replicability.

# Kendall's τ fuzzy bootstrap
fuzzy_tau <- function(cop_fit, nboot = 100) {
  vals <- replicate(nboot, {
    simU <- rCopula(nrow(U), cop_fit@copula)
    fit_b <- fitCopula(cop_fit@copula, simU, method = "ml")
    tau(fit_b@copula)
  })
  quantile(vals, c(0.025, 0.16, 0.84, 0.975))
}

# Spearman's ρ fuzzy bootstrap
fuzzy_rho <- function(cop_fit, nboot = 100) {
  vals <- replicate(nboot, {
    simU <- rCopula(nrow(U), cop_fit@copula)
    fit_b <- fitCopula(cop_fit@copula, simU, method = "ml")
    rho(fit_b@copula)
  })
  quantile(vals, c(0.025, 0.16, 0.84, 0.975))
}
5. Rolling and Estimation Rules

No rolling window was applied; analysis is performed on the full 2015–2025 sample.

Copula parameters were estimated via maximum likelihood (ML).

Bootstrap replications: 100 for parameters and 150 for dependence measures.

α-cut levels: 68 % (core) and 95 % (support), corresponding to ±1σ and ±2σ equivalents.
