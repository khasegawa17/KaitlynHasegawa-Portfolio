# FX Hedging Specification – U.S. Pharmaceutical Exporter – Technical Specification

**Created by:** Kaitlyn Hasegawa  
**Updated by:** Kaitlyn Hasegawa  
**Date Created:** 2026-04-01  
**Date Updated:** 2026-04-24  
**Version:** 1.0  
**LLM Used:** None (Stage 1 used Copilot for Decision Memo)

**Role:** Treasury Analyst / Financial Risk Manager  
**Audience:** Director of Treasury / CFO

**Purpose:** Provide a quantitative technical specification outlining the analytical framework for evaluating FX hedging alternatives for a material EUR-denominated receivable with a one-year settlement horizon.

---

## 1. Problem Statement

Our company, a U.S.-based pharmaceutical exporter, faces a significant foreign exchange exposure: an **€8,000,000 receivable due in 12 months** from our international pharmaceutical sales contract. At current spot rates (approximately 1.09 EURUSD), this represents roughly **$8.72 million in USD cash flow**. This receivable exposes us to **EURUSD exchange rate risk**: should the EUR depreciate 5–10% against the USD over the next year, the USD value of our receivable will decline materially, reducing reported revenue and adversely affecting cash flow planning for operations, R&D investment, and shareholder returns.

**Objective:** Evaluate and model four hedging strategies (forward contract, money market hedge, EUR put option, and EUR call option) to determine the optimal risk-return profile that protects minimum USD proceeds while preserving operational flexibility and upside potential where appropriate. This specification documents the analytical framework for comparing hedge outcomes under various spot rate scenarios at the one-year settlement date, directly supporting the Director of Treasury's hedge authorization decision and our Stage 2 Excel model build.

---

## 2. Inputs (Known Variables)

| Named Range | Description | Unit | Value | Source |
|---|---|---|---|---|
| `FC_AMT` | EUR receivable amount | EUR millions | 8.000 | Company revenue contract / Stage 1 Decision Memo |
| `S0_in` | EURUSD spot rate at inception | USD per EUR | 1.0900 | Market data as of 2026-04-01 (per Decision Memo) |
| `F0_in` | 1-year EURUSD forward rate | USD per EUR | 1.0890 | Dealer quote per Stage 1 memo |
| `R_USD` | USD 1-year interest rate | Annual % | [Market determined] | Market data (1-year SOFR or Treasury); to be confirmed |
| `R_FC` | EUR 1-year interest rate | Annual % | [Market determined] | Market data (EURIBOR 12M); to be confirmed |
| `K_PUT` | EUR put strike (at-the-money) | USD per EUR | 1.0900 | Set at current spot per Stage 1 Strategy 2 |
| `K_CALL` | EUR call strike (at-the-money) | USD per EUR | 1.0900 | Set at current spot per Stage 1 Strategy 3 |
| `PREM_PUT` | Put premium per EUR | USD | 0.021 | Dealer quote per Stage 1 Decision Memo |
| `PREM_CALL` | Call premium per EUR | USD | 0.026 | Dealer quote per Stage 1 Decision Memo (short call in collar) |
| `T_DAYS` | Days to settlement | Days | 365 | Contract timing (1 year = 365 days) |
| `COLLAR_CALL_STRIKE` | Call strike in collar (optional) | USD per EUR | 1.10 | Strike level per Decision Memo Stage 1 Strategy 3 |

---

## 3. Assumptions & Constraints

- **Rate basis:** All interest rates expressed on a simple annual basis (not compounded); ACT/360 day count convention.
- **Forward parity:** Forward rate 1.0890 reflects interest rate differential via covered interest parity; no arbitrage between forward hedge and money market hedge.
- **Option premiums:** Paid upfront in USD at inception; premiums ($0.021 for put, $0.026 for call) deducted from USD proceeds at settlement.
- **Strike prices:** Set at or near the current spot rate (1.0900 EURUSD) using at-the-money convention; no out-of-the-money variations modeled in Stage 2.
- **Transaction and credit costs:** Excluded from analysis; real-world models should include bid-ask spreads and counterparty credit spreads per Stage 1 assumptions.
- **Settlement:** All cash flows occur at exactly T = 365 days; no early termination, dynamic rehedging, or mid-year adjustments modeled.
- **Borrowing/investment access:** Assumes our company has ready access to EUR borrowing at `R_FC` and USD investment at `R_USD` for the money market hedge.
- **Collar structure (Strategy 3):** Buy put at 1.09 (cost: $0.021); sell call at 1.10 (receive: $0.026); net credit = $0.005 per EUR (~$40,000 total for 8M EUR).
- **Scope:** Stage 2 models forward, MM hedge, put option, and call option separately. Collar and collar variants deferred to Stage 3+ refinements.

---

## 4. Calculation Flow

### 4.1 Forward Hedge

1. Lock in the one-year forward rate from dealer: **`F0_in` = 1.0890 USD/EUR**
2. Compute USD proceeds: **`USD_Forward = FC_AMT × F0_in`** → **`8.000 × 1.0890 = 8.712 million USD`**
3. Result: Certain USD proceeds with zero upfront cost; no variability with spot at maturity.
4. Store as certainty benchmark for comparison against option strategies.

### 4.2 Money Market Hedge

1. **Step 1 – Borrow in EUR:** Calculate the present value of the EUR receivable to determine borrowing amount.
   - **`PV_EUR = FC_AMT / (1 + R_FC × T_DAYS / 360)`**
   - This is the EUR amount needed today that grows to €8.000M by maturity via EUR interest accrual.

2. **Step 2 – Convert at spot:** Exchange the borrowed EUR to USD at the current spot rate.
   - **`USD_Invested = PV_EUR × S0_in`** → **`PV_EUR × 1.0900`**
   - This locks in the effective rate via spot and cost of money.

3. **Step 3 – Invest in USD:** Place USD proceeds in a 1-year investment earning `R_USD`.
   - **`USD_Proceeds_MM = USD_Invested × (1 + R_USD × T_DAYS / 360)`**
   - At maturity: EUR receivable repays EUR loan (no net FX exposure); USD investment matures to net proceeds.

4. **Verify parity:** Compare `USD_Proceeds_MM` to `USD_Forward`. They should be nearly identical (within rounding error); any divergence signals market data inconsistency or calculation error.
   - **Expected:** Both should yield approximately **8.712 million USD** (or very close).

### 4.3 Put Option Hedge (EUR Put, Strike = 1.0900)

Provides floor protection; we retain upside if EUR appreciates. This strategy aligns with Decision Memo Stage 1 Strategy 2.

1. **Premium cost:** Total put cost = **`FC_AMT × PREM_PUT`** → **`8.000 × 0.021 = 0.168 million USD`** = **$168,000** (paid upfront in USD).

2. **For each scenario ending spot rate `S_T`:**
   - **If `S_T ≥ K_PUT` (i.e., S_T ≥ 1.0900):** Exercise is out-of-the-money. We sell EUR at spot: **`Gross_USD = FC_AMT × S_T`** = **`8.000 × S_T`**
   - **If `S_T < K_PUT` (i.e., S_T < 1.0900):** Exercise is in-the-money. We exercise and sell EUR at strike: **`Gross_USD = FC_AMT × K_PUT`** = **`8.000 × 1.0900 = 8.720 million USD`**
   - **Net USD proceeds:** **`USD_Put(S_T) = Gross_USD - (FC_AMT × PREM_PUT)`** = **`Gross_USD - 0.168`**

3. The put option payoff creates a hockey-stick profile: flat floor at **$8.552M** (= $8.720M – $0.168M premium) below the strike, then linearly increasing with spot rates above the strike.

4. **Break-even spot rate:** `S_T = K_PUT - PREM_PUT` = **1.0900 – 0.021 = 1.0690 EURUSD**. At this rate, put option net proceeds equal zero; below this, protection exceeds premium cost.

### 4.4 Call Option Hedge (EUR Call, Strike = 1.0900)

Provides upside participation for comparison; counterparty (option buyer) has the call. Less suitable for a receivable hedge but demonstrates trade-offs.

1. **Premium cost:** Total call cost = **`FC_AMT × PREM_CALL`** → **`8.000 × 0.026 = 0.208 million USD`** = **$208,000** (paid upfront in USD).

2. **For each scenario ending spot rate `S_T`:**
   - **If `S_T ≤ K_CALL` (i.e., S_T ≤ 1.0900):** Option is out-of-the-money; buyer does not exercise. We sell EUR at spot: **`Gross_USD = FC_AMT × S_T`** = **`8.000 × S_T`**
   - **If `S_T > K_CALL` (i.e., S_T > 1.0900):** Option is in-the-money; buyer exercises. We must sell EUR at strike: **`Gross_USD = FC_AMT × K_CALL`** = **`8.000 × 1.0900 = 8.720 million USD`**
   - **Net USD proceeds:** **`USD_Call(S_T) = Gross_USD - (FC_AMT × PREM_CALL)`** = **`Gross_USD - 0.208`**

3. The call option payoff creates an inverted hockey-stick: decreases as spot falls (receiving less), then capped at **$8.512M** (= $8.720M – $0.208M premium) above the strike.

### 4.5 Unhedged (Baseline)

- **Simple spot exposure:** **`USD_Unhedged(S_T) = FC_AMT × S_T`** = **`8.000 × S_T`**
- No premium cost; full exposure to FX volatility.
- At current spot (1.09), unhedged value = **$8.72M**; at 1.05, drops to **$8.40M**; at 0.95, drops to **$7.60M**.

---

## 5. Outputs

| Output Name | Description | Format | Purpose | Notes |
|---|---|---|---|---|
| **Input Summary Table** | All scenario variables (FC_AMT=8M, S₀=1.09, F₀=1.0890, premiums, strikes) | Structured table with named ranges | Audit trail and AI prompt foundation | Directly from Decision Memo |
| **Forward Hedge Result** | Locked USD proceeds: 8.000 × 1.0890 = 8.712M USD | Single numeric value | Certainty benchmark | Zero premium cost |
| **Money Market Hedge Result** | USD proceeds from borrow/convert/invest logic; should equal ~8.712M | Single numeric value | Parity verification vs. forward | Validates forward rate consistency |
| **Put Option Summary** | Premium cost: $0.168M; floor: $8.552M; breakeven: S_T = 1.0690 | Summary metrics | Downside protection quantification | Strategy 2 from Decision Memo |
| **Sensitivity Table** | USD proceeds by hedge strategy across ±5% spot scenarios (S_T: 1.035–1.145) | 12-row table: S_T, USD_Forward, USD_MM, USD_Put, USD_Call, USD_Unhedged | Visualization of strategy trade-offs | 1% increments = 11 scenarios |
| **Payoff Chart** | Line plot comparing net USD proceeds across strategies vs. ending spot rate | XY scatter/line chart; S_T on x-axis, USD on y-axis; 4 strategy lines | Executive-ready visual comparison | Shows hockey-stick (put), cap (call), flat (forward), linear (unhedged) |
| **Summary Metrics** | Minimum, maximum, mean, and median USD proceeds by strategy across scenarios | 4 × 6 summary table | Quick decision support | Highlights downside (put) vs. upside (unhedged) |
| **Break-Even Analysis** | Spot rates where hedge net proceeds equal forward or unhedged | 3-row table | Clarifies indifference points | Put breakeven = 1.0690; forward = 1.0890 always |
| **Model Review & Notes** | Candid reflection on Stage 2 build and refinements for Stage 3+ | Narrative text (Section 6 below) | Foundation for iterative enhancement | Captures design decisions and improvement opportunities |

---

## 6. Model Review — What Worked & What to Improve

### 6.1 What Worked Correctly

- **Forward hedge logic:** Straightforward multiplication of FC amount by forward rate (8.000 × 1.0890 = 8.712); no ambiguity in implementation.
- **Spot rate anchor:** Using current spot (1.0900 EURUSD) as strike and sensitivity baseline aligns with Decision Memo Stage 1 intent.
- **Premium cost calculations:** Direct multiplication (8.000 × 0.021 = $0.168M; 8.000 × 0.026 = $0.208M) correctly quantifies upfront outlay.
- **Put option payoff structure:** Hockey-stick profile correctly implemented; floor at $8.552M (post-premium) provides intuitive protection visualization.
- **Parity check between forward and MM hedges:** Framework established to verify interest rate differential captured correctly; expected result ~8.712M USD.
- **Sensitivity grid setup:** ±5% spot range (1.035–1.145) with 1% increments yields 11 scenarios, sufficient for decision-making without overwhelming complexity.
- **Color-coded worksheet design:** Yellow inputs (1.09 spot, 1.0890 forward, premiums), blue assumptions (ACT/360, 365 days), green formulas (payoff calculations), gray outputs (summary).

### 6.2 Simplifications to Replace in Refined Version

- **Strike price assumption (ATM only):** Current model assumes strikes set at current spot (1.0900). A more robust version should explore **out-of-the-money variations** (e.g., K_PUT = 0.95×S₀ = 1.0355, K_PUT = 1.0900, K_PUT = 1.05×S₀ = 1.1445) to compare cost vs. protection trade-offs and quantify "insurance" value.

- **Constant interest rates across the year:** Real markets have term structure; 1-year rates differ from 3M/6M rates. Decision Memo notes R_USD and R_FC as "[Market determined]"—next version should incorporate observed forward rate curve or assume realistic SOFR/EURIBOR benchmarks (e.g., 5.0% USD, 3.5% EUR as of 2026-04-01).

- **Zero bid-ask spread:** Dealers quote bid-ask spreads on forwards and option premiums. Decision Memo assumes premiums are "fair" (mid-market); refined model should apply realistic spreads (e.g., ±1–2 bps on forward, ±$0.0005 on premiums) to reveal hidden transaction costs (~$10K–30K range).

- **No dynamic rehedging or rolling:** Current model assumes buy-and-hold hedge for the full 365 days. Treasury might adjust if spot drifts significantly (e.g., EUR rallies 10% by month 6, reducing downside risk). Future version could model rolling or partial unwinding scenarios.

- **Collar strategy deferred:** Decision Memo Stage 1 Strategy 3 describes a collar (long put at 1.09 + short call at 1.10, net credit = $0.005/EUR = $40K). Stage 2 Excel should incorporate this for completeness; adds 1–2 rows to sensitivity table.

### 6.3 Naming, Layout, and Auditability Improvements

- **Intermediate calculations section:** Add explicit rows for:
  - `PV_EUR` (present value of EUR receivable)
  - `USD_INVESTED_MM` (USD amount invested in money market hedge)
  - `PREM_PUT_TOTAL` (total put premium in USD)
  - `PREM_CALL_TOTAL` (total call premium in USD)
  - `BREAKEVEN_PUT_SPOT` (spot rate where put net proceeds = 0)
  Rather than nesting all three MM steps in a single formula. Easier to audit and debug.

- **Named ranges for key outputs:** Beyond inputs, add named ranges for:
  - `FORWARD_PROCEEDS` (8.712M)
  - `MM_PROCEEDS` (expected ~8.712M)
  - `PUT_FLOOR` ($8.552M)
  - `PUT_COST` ($0.168M)
  - `CALL_CAP` ($8.512M)
  - `CALL_COST` ($0.208M)

- **Assumptions tab:** Create a second sheet listing all assumptions (rate basis, day count, transaction costs excluded, borrowing access assumed, etc.) with version history. Makes it easy for Treasury to adjust assumptions for "what-if" analysis without touching formulas.

- **Comments and cell validation:** Embed data validation rules (e.g., spot rates must be > 0, premiums must be > 0) and cell comments explaining key assumptions for new users or future model maintainers.

- **Decision Memo traceability:** Add cell references linking to Decision Memo values (e.g., "Forward = 1.0890 per Decision Memo section Methods, Strategy 1").

### 6.4 Scenarios & Metrics Worth Including

- **Collar strategy (Decision Memo Strategy 3):** Buy put at 1.09 (cost $0.021), sell call at 1.10 (receive $0.026), net credit $0.005/EUR. Add a 5th column `USD_Collar(S_T)` to sensitivity table. This is the hybrid Treasury mentioned and should be analyzed in Stage 2.

- **Downside protection scenario explicitly labeled:** Add a summary row showing:
  - **Worst case:** Put option floor ($8.552M)
  - **Best case:** Unhedged if EUR rallies (unlimited to ~1.15–1.20)
  - **Locked-in:** Forward (8.712M always)
  - **Balanced:** Collar (protected 1.09–1.10, costs ~$40K)

- **Effective cost-of-hedge metric:** Calculate implicit cost of each strategy vs. forward:
  - Put: Net proceeds vs. forward = difference in USD; express as % of receivable or bps.
  - Collar: Net credit vs. put = $40K saved; express as % of receivable.

- **Parity deviation analysis:** If MM proceeds ≠ forward proceeds by >$10K, flag the source (interest rate data mismatch, spot quote inconsistency) and quantify arbitrage opportunity.

- **Accounting P&L impact notes:** Stage 2 excludes fair value and hedge accounting. Add a notation row: "Fair value of put option varies with implied volatility and spot; mark-to-market per ASC 815. Hedge accounting qualification deferred to Stage 4 accounting review." This prepares for Stage 4.

---

## 7. Sensitivity Plan

### 7.1 Scenario Range & Step

We vary the ending spot rate (`S_T`) from **0.95 × S₀ to 1.05 × S₀**, where S₀ = 1.0900:

- **Lower bound:** 0.95 × 1.0900 = **1.0355 EURUSD** (5% EUR depreciation)
- **Upper bound:** 1.05 × 1.0900 = **1.1445 EURUSD** (5% EUR appreciation)
- **Step:** 1% increments = **0.0109 EURUSD per step**
- **Total scenarios:** 11 (1.0355, 1.0464, 1.0573, 1.0682, 1.0791, 1.0900, 1.1009, 1.1118, 1.1227, 1.1336, 1.1445)

| Scenario # | S_T (EURUSD) | S_T as % of S₀ | USD Proceeds (Unhedged) | Business Interpretation |
|---|---|---|---|---|
| 1 | 1.0355 | 95% | $8.284M | EUR significant depreciation; Treasury concern |
| 2 | 1.0464 | 96% | $8.371M | EUR mild depreciation |
| 3 | 1.0573 | 97% | $8.458M | EUR mild depreciation |
| 4 | 1.0682 | 98% | $8.546M | EUR slight depreciation |
| 5 | 1.0791 | 99% | $8.633M | EUR near-flat |
| 6 | 1.0900 | 100% | $8.720M | Spot unchanged (no FX movement) |
| 7 | 1.1009 | 101% | $8.807M | EUR slight appreciation |
| 8 | 1.1118 | 102% | $8.894M | EUR mild appreciation |
| 9 | 1.1227 | 103% | $8.981M | EUR mild appreciation |
| 10 | 1.1336 | 104% | $9.069M | EUR moderate appreciation |
| 11 | 1.1445 | 105% | $9.156M | EUR significant appreciation; business upside |

### 7.2 What This Communicates

- **Downside protection via put option:** Scenarios 1–5 show unhedged proceeds falling from $8.284M to $8.633M; put option floor at $8.552M captures this risk, costing only $0.168M in premium.

- **Upside optionality:** Scenarios 7–11 show unhedged proceeds rising from $8.807M to $9.156M; put option captures ~$0.807M to ~$0.988M of upside (minus $0.168M premium paid = ~$0.64M–0.82M net upside).

- **Forward rigidity:** Forward is flat at $8.712M across all 11 scenarios; no participation in upside (scenarios 7–11), but also no downside below $8.712M.

- **Collar sweet spot:** Decision Memo Strategy 3 (buy 1.09 put, sell 1.10 call) yields:
  - Floor protection at 1.09 (like put, but cheaper)
  - Upside capped at 1.10 (costs $0.026 call premium but receives $0.026)
  - Net cost ~$0 ($40K one-time credit)
  - Suitable for balanced risk appetite and Treasury that expects EUR to trade 1.09–1.10.

- **Premium cost trade-offs:**
  - Put costs $0.168M; breakeven spot = 1.0690 (below forward).
  - Call costs $0.208M; effective cap on upside at 1.0692 (post-premium).
  - Collar nets $0.040M credit; balanced protection 1.09–1.10.

### 7.3 Most Useful Comparisons for Treasury Decision

1. **Forward vs. Put:** "What is the value of optionality?" Answer: Put costs $168K to preserve upside; forward is free but loses upside. This drives the hedge choice.

2. **Forward vs. MM:** "Is the market efficiently priced?" Answer: If MM ≠ forward by >$50K, investigate spot/rate quotes.

3. **Put vs. Unhedged:** "What is the 'insurance premium'?" Answer: $168K (or ~190 bps of receivable) to eliminate 5% downside risk.

4. **Collar vs. Put:** "Can we reduce hedge cost?" Answer: Collar costs $40K less than put (net credit); suitable if Treasury comfortable capping upside at 1.10.

5. **All strategies vs. Scenario #6 (no move):** At spot = 1.0900:
   - Forward = $8.712M (loses $0.008M vs. unhedged 8.720)
   - Put = $8.552M (loses $0.168M premium)
   - Unhedged = $8.720M (baseline)
   - Collar = $8.680M (gains $0.040M credit)

---

## 8. Limitations & Next Steps

### 8.1 What This Specification Excludes

- **Dynamic hedging or rolling:** Assumes static, full-year (365-day) hold. Real treasuries may roll hedges quarterly, adjust notional if receivables change, or unwind early if spot moves favorably.

- **Volatility and option valuation:** Uses dealer-quoted premiums ($0.021 put, $0.026 call) directly from Decision Memo; no Black-Scholes, implied volatility, or premium derivation.

- **Credit risk and counterparty exposure:** Assumes zero counterparty default. Real agreements include CSA (Credit Support Annex) collateral posting, mark-to-market valuation, and credit spread adjustments.

- **Accounting treatment (ASC 815):** Does not address hedge accounting designation, fair value vs. cash flow hedges, or realized/unrealized gains. Stage 4 will add accounting P&L discussion.

- **Correlation with other exposures:** Assumes isolated EUR receivable; ignores interaction with other EUR assets, liabilities, or contracts in company's FX book (e.g., EUR payables for supplies that could offset).

- **Tax treatment:** No distinction between ordinary income, hedging gains/losses, or foreign exchange gains/losses for tax purposes; assumes no material tax impact.

- **Interest rate term structure:** Uses flat rates (R_USD, R_EUR assumed constant for 1 year). Real markets have forward curves; this impacts MM hedge precision and option valuation.

- **Transaction costs and bid-ask spreads:** Assumed negligible per Decision Memo assumptions. Refined model should include dealer spreads (~1–2 bps forward, ~$0.0005 option premium spread).

### 8.2 How This Feeds Stage 4

This technical specification provides the **machine-readable blueprint** for the final analysis and AI prompt:

| Stage 3 Output | Stage 4 Input | Purpose |
|---|---|---|
| Problem statement + inputs (S₀=1.0900, F₀=1.0890, premiums $0.021/$0.026) | AI uses exact scenario variables (no guessing) | Ensures precision in generated model |
| Calculation flow (forward, MM, put, call logic) | AI generates precise, auditable formulas in Excel | Output is reproducible and auditable |
| Model review & improvements (collar, multi-strike, rates, bid-ask) | AI builds refined version, not just a replica | Elevates model rigor; adds professional polish |
| Outputs & sensitivity plan (11 scenarios, payoff chart, break-even analysis) | AI produces exact tables, charts, breakeven for CFO brief | Executive dashboard ready for presentation |
| Limitations section | AI-generated prompt includes caveats and scope | Maintains transparency for CFO decision |

The refined hedge recommendation in Stage 4 will rely directly on this specification to ensure consistency, transparency, and reproducibility—demonstrating how professional treasury workflows translate domain expertise into actionable AI-driven analysis.

---

**Specification Metadata**

- **Stage:** 3 (Technical Specification)
- **Project:** FX Hedging – U.S. Pharmaceutical Exporter
- **Scenario:** Scenario 2 from project-fx-hedging/scenarios.md
- **Input Source:** Decision-Memo.md (Stage 1) dated 2026-04-01
- **Current Date:** 2026-04-24
- **Next Deliverable:** Stage 4 (Final Analysis, Prompt Engineering & Recommendation)
- **Spec Status:** Ready for Excel model build (Stage 2) and AI prompt engineering (Stage 4)
