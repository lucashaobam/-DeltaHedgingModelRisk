# Discrete Time Delta Hedging: Replication Error, Transaction Costs, and Model Risk

A Monte Carlo research notebook on what it actually costs to replicate an
option, and where the textbook Black Scholes hedge breaks down.

## Motivation

The Black Scholes hedging argument is usually presented as a clean,
continuous time result: hedge continuously and the option is perfectly
replicated. In practice hedging happens at discrete intervals, incurs
transaction costs, and is run against a market that doesn't actually follow
geometric Brownian motion. This notebook quantifies each of those gaps
explicitly, rather than leaving them as footnotes.

## What's inside

1. **Theoretical foundation.** The replication argument derived from Ito's
   lemma, arriving at the Black Scholes PDE and closed form Greeks, plus an
   exact derivation of the **gamma theta P&L identity**, the single equation
   that governs everything else in the notebook: a delta hedged option is a
   pure bet on realized versus implied variance, paid through gamma.
2. **Pricing engine and validation.** Closed form Black Scholes and Greeks,
   cross checked against finite differences and put call parity.
3. **Discrete hedging and the 1/√N law.** Quantifying how replication error
   shrinks as rebalancing frequency N increases, and the quadratic variation
   view of why.
4. **Exact P&L attribution.** Splitting realized hedged P&L into convexity
   (gamma) and time decay (theta) streams that tie out to the cent against
   full repricing.
5. **Transaction costs and optimal hedging frequency.** The cost risk
   frontier under proportional transaction costs, move based (band) hedging
   as an alternative to fixed interval rebalancing, and a numerically
   verified Leland volatility adjustment.
6. **Model risk I, jump diffusion.** Why a continuous delta cannot hedge a
   jump, and the resulting P&L floor under Merton dynamics.
7. **Model risk II, stochastic volatility.** The "vega leak" that appears
   when the true dynamics are Heston but the hedge assumes constant vol, and
   a demonstration of drift invariance (risk neutral pricing made visible).
8. **Calibration to real market data.** Fitting the jump diffusion model to
   real Nifty 50 ETF (NIFTYBEES.NS) data via robust jump detection, then
   re running the hedging analysis in the calibrated world.
9. **The morning risk report.** Mean, volatility, VaR, and CVaR of hedged
   P&L across four market worlds (Black Scholes, stylized Merton jumps,
   Heston stochastic vol, and the real NIFTYBEES calibrated world), in the
   format of a desk risk report.
10. **Extensions and research directions.** Deep hedging, no transaction
    band networks, reinforcement learning for hedging, minimum variance
    deltas estimated from data, rough volatility, and variance swaps.

## Key result

Hedged P&L is governed exactly by ½Γ(δS² − σ²S²δt): realized variance minus
implied variance, weighted by gamma. Every subsequent section is that
identity meeting a piece of reality, whether that is finite rebalancing
(discretization error), trading costs (the cost risk trade off), or
dynamics the model didn't anticipate (jumps, stochastic vol).

Calibrating to real NIFTYBEES.NS data (Aug 2023 to Aug 2026) is a useful
sanity check on the stylized stress tests above: over this particular
window, real calibrated jump risk turned out milder than the stylized
Merton and Heston scenarios, with the hedge's tail loss (CVaR₉₅) coming in
close to, and marginally below, the plain Black Scholes case, versus
roughly double under the stylized crash and vol scenarios. That is a
property of this specific sample window, not a general claim about the
Nifty index.

## Tech stack

`numpy`, `pandas`, `scipy` (stats), `matplotlib`, `yfinance` (for live data,
optional)

## Repository structure

```
.
├── delta_hedging_pnl.ipynb   Full notebook: theory, derivation, and code
└── README.md
```

## Running it

```bash
pip install numpy pandas scipy matplotlib yfinance
jupyter notebook delta_hedging_pnl.ipynb
```

Sections 1 through 9 run entirely on simulated paths with fixed random
seeds, fully reproducible offline. Section 10 (calibration to real market
data) tries to fetch live NIFTYBEES.NS price history via `yfinance`, and
falls back to an embedded real price snapshot bundled in the notebook if
that fetch fails, so the notebook runs end to end even without internet
access.

## References

- Black, F. and Scholes, M. (1973). *The Pricing of Options and Corporate
  Liabilities.* Journal of Political Economy.
- Merton, R. (1976). *Option Pricing When Underlying Stock Returns Are
  Discontinuous.* Journal of Financial Economics.
- Heston, S. (1993). *A Closed Form Solution for Options with Stochastic
  Volatility.* Review of Financial Studies.
- Leland, H. (1985). *Option Pricing and Replication with Transaction
  Costs.* Journal of Finance.
