# FX Hedging Recommendation – U.S. Pharmaceutical Exporter

**Created by:** Kaitlyn Hasegawa  
**Updated by:** Kaitlyn Hasegawa  
**Date Created:** 2026-04-01  
**Date Updated:** 2026-04-01  
**Version:** 1.0  
**LLM Used:** Copilot

---

## Executive Summary

Our firm faces a significant foreign exchange exposure: an €8,000,000 receivable due in 12 months from our international pharmaceutical sales contract. Without hedging, a 5–10% depreciation in EUR/USD could reduce our USD proceeds by $400,000–$800,000. This memo outlines the nature of our exposure and presents three hedging families—forward contracts, put options, and synthetic strategies—to mitigate downside risk. We recommend proceeding to Stage 2 (Excel modeling) to quantify payoff profiles and compare hedge costs relative to our firm's risk tolerance.

---

## Background & Objectives

### The Exposure
Our international business segment has secured a €8,000,000 receivable, payable in one year. At current spot rates (approximately 1.09 EURUSD), this represents roughly $8.72 million in USD cash flow. However, exchange rate volatility presents material downside: a 10% EUR depreciation to 0.98 EURUSD would reduce proceeds to $7.84 million—a $880,000 loss.

### Why Hedging Matters
Manufacturing and export cycles often misalign with revenue timing. We incur expenses (R&D, production) in USD while earning revenues in EUR. Without hedging, we face:
- **Translation risk:** Monthly P&L swings based on currency moves unrelated to operational performance
- **Economic risk:** Competitive disadvantage if competitors hedge and we don't
- **Earnings volatility:** Harder to forecast cash and earnings guidance to investors

### Objective
Evaluate three hedging strategies to determine which best balances cost, protection, and operational flexibility for Stage 2 modeling.

---

## Methods

We will evaluate three hedge families against our €8M exposure:

### 1. **Forward Contract Hedge**
- **Mechanics:** Lock in a fixed 1.0890 EURUSD exchange rate now; settle in 1 year.
- **Pros:** Zero upfront cost; eliminates all FX uncertainty; simple and transparent.
- **Cons:** No upside participation if EUR appreciates; rigid (no flexibility to adjust).

### 2. **Put Option Hedge**
- **Mechanics:** Buy a EUR put with strike ~1.09 EURUSD (current spot); premium = $0.021 per EUR (~$168,000 total for 8M EUR).
- **Pros:** Downside protection; unlimited upside if EUR rallies; retains operational flexibility.
- **Cons:** Upfront premium cost reduces net proceeds; complexity requires monitoring.

### 3. **Synthetic Collar (Put + Short Call)**
- **Mechanics:** Buy a put at 1.09 (cost: $0.021); sell a call at 1.10 (receive: $0.026); net credit = $0.005 per EUR (~$40,000 for 8M).
- **Pros:** Low or zero net cost; protection within a band; suitable for balanced risk appetite.
- **Cons:** Caps upside gains above 1.10; more complex accounting and documentation.

---

## Limitations & Next Steps

**Assumptions:**
- Current spot = ~1.09 EURUSD; rates and volatility as of market open 2026-04-01.
- Counterparty credit risk and transaction costs assumed negligible.
- No material shifts in firm's operating strategy or revenue timing expected.

**Next Steps:**

1. **Stage 2 (Excel Model Build):** Construct a sensitivity table comparing all three strategies across a range of EUR/USD rates at maturity (0.98–1.20). Calculate net USD proceeds, hedge costs, and P&L under each scenario.

2. **Stage 3 (Technical Specification):** Document the model architecture, assumptions, and valuation logic in sufficient detail for replication or enhancement.

3. **Stage 4 (Final Recommendation):** Interpret modeling results relative to company risk tolerance and deliver a structured CFO recommendation with AI-assisted prompt engineering.

**Ownership:** [Your Name] will lead Excel build and analysis with input from Treasury and Controller.

---

## References

- Adam Stauffer, *FIN-321 Multi-Stage Project: FX Exposure and Hedging Strategies*, Shidler College of Business, 2026.
- Scenario 2 (U.S. Pharmaceutical Exporter), project-fx-hedging/scenarios.md.
- Current market data: EURUSD spot and forward quotes as of 2026-04-01 (Bloomberg Terminal or Yahoo Finance).
- Hull, J. C. (2018). *Options, Futures, and Other Derivatives* (10th ed.). Pearson.
