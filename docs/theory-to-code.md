# From chapter 2 to the code

How to use this document: for each model, derive the result on paper first, then open `index.html` and find the function named in the "In the code" box, then predict what a slider will do before you drag it. Each section ends with the one sentence you should be able to say without notes and two teach-back questions. If you can answer those cold, you own the model.

Page numbers refer to Cartea, Jaimungal and Penalva (2015), chapter 2.

---

## 0. The toolkit the whole chapter runs on

1. **Expectation and conditioning.** E[X] is the average outcome; E[X | info] is the best guess given what you know. The "efficient price" everywhere in the chapter is E[final value | everything public so far].
2. **Variance scaling.** Holding q units of something with variance σ² gives wealth variance q²σ².
3. **The exponential-utility trick.** For U(X) = -e^(-γX) and X normal with mean m and variance s²: E[U(X)] = -exp(-γ(m - ½γs²)). That is increasing in m - ½γs², so maximising expected utility is the same as maximising **mean minus half gamma times variance**. Every optimisation in section 2.1 is this.
4. **First-order condition.** Hill-shaped objective: set the derivative to zero.
5. **Market clearing.** Shares don't vanish. The equilibrium price is the one at which people willingly hold exactly the shares that exist.
6. **Conditional expectation of jointly normal variables is linear regression.** E[v | y] = E[v] + [Cov(v,y)/Var(y)]·(y - E[y]). For an engineer: the LMMSE estimator, with Cov/Var as the gain. Kyle's λ is this gain.
7. **Bayes' rule.** P(state | evidence) = P(evidence | state)·P(state)/P(evidence).
8. **Martingale.** Expected next value equals the current value. The efficient price is one; observed prices deviate from it by liquidity effects.

---

## 1. Grossman-Miller: the price of immediacy (2.1.1, pp. 21-24)

**Story.** LT1 must sell i shares at t = 1. The natural buyer LT2 only arrives at t = 2. Between the two dates a news shock ε₂ ~ N(0, σ²) hits the value; between t = 2 and t = 3 another shock ε₃ does. Final value S₃ = μ + ε₂ + ε₃. n identical risk-averse market makers, and LT1 himself, all have U(X) = -e^(-γX).

**Step 1: the demand rule.** Consider the t = 2 decision. Hold q from t = 2 to t = 3. Expected wealth rises by q·(E[S₃|ε₂] - S₂) and variance is q²σ². By tool 3 maximise q·(E[S₃|ε₂] - S₂) - ½γσ²q². Derivative to zero:

> q* = (E[S₃|ε₂] - S₂) / (γσ²)

Position size equals expected edge per share divided by risk aversion times variance. Everyone uses this rule because everyone has the same utility.

**Step 2: clear the market at t = 2.** LT1 brought +i, LT2 brought -i (he wants to buy i), market makers brought nothing: net zero shares. All n + 2 agents want the same q*, so (n+2)q* = 0, hence q* = 0 and S₂ = E[S₃|ε₂] = μ + ε₂. The imbalance is gone, nobody needs paying to hold risk, everyone goes flat at fair value.

**Step 3: clear the market at t = 1.** Everyone knows they will be flat at t = 2, so only S₂ matters, and from t = 1 it is N(μ, σ²). Same rule: q₁* = (μ - S₁)/(γσ²). Only the n market makers and LT1 are present and i shares exist, so (n+1)(μ - S₁)/(γσ²) = i:

> **S₁ = μ - γσ² · i/(n+1)**

Each agent holds i/(n+1). LT1 sells n·i/(n+1) now and keeps i/(n+1) for tomorrow.

**Reading it.** The concession γσ²·i/(n+1) is γσ² times the inventory each risk-bearer ends up holding: the marginal risk cost of the last share. It grows with i, γ, σ² and shrinks with n. LT1 does not dump everything because he is one of the n + 1 risk-bearers. His total cost is concession times shares sold, γσ²·n·i²/(n+1)², so it is quadratic in size: doubling the order roughly quadruples the cost.

**In the code.** `gmCalc(p, n)`:
- `risk = p.gamma * p.sigma * p.sigma * p.i / (n + 1)` is γσ²·i/(n+1).
- `S1 = p.mu - risk - fee`, `hold = p.i / (n + 1)`, `sold = n * p.i / (n + 1)`.
- `gmRender` evaluates `gmCalc` for n = 1..30 to draw the concession curve; the marker is the current n.
- The sentence under the numbers is built from the same variables, so the words and the maths cannot drift apart.

**Try this, predicting first.** Double i: the concession doubles and the total cost quadruples. Double σ: the concession quadruples. Push n from 5 to 29: the concession falls by a factor of 5 (6/30). Set n = 1: the seller and one market maker split the risk half and half.

**Say it in one sentence.** A market maker's discount is the risk premium for holding an imbalance until the other side arrives, equal to γσ² times the inventory she ends up carrying, shared across everyone willing to carry it.

**Teach-back.** (1) Why does LT1 keep i/(n+1) shares rather than selling them all? (2) What happens to S₁ if the seller wanted to buy instead (i < 0)?

---

## 2. Two kinds of cost (2.1.2, pp. 24-26)

Participation cost c (technology, connectivity, opportunity cost of being present) is fixed and decides how many market makers show up at all: it works through n. A trading fee η per share is paid on each trade. LT1 pays η whether he sells now or later, so η drops out of his demand: q₁^LT1 = (μ - S₁)/(γσ²). A market maker buys now (paying η) and sells later (paying η again), so her demand is q₁^MM = (μ - 2η - S₁)/(γσ²). Clearing:

> i = n(μ - S₁ - 2η)/(γσ²) + (μ - S₁)/(γσ²)   =>   **S₁ = μ - γσ²·i/(n+1) - 2η·n/(n+1)**

Almost the whole round-trip fee lands on the liquidity taker as a lower price, and he sells less (less immediacy). Participation costs hit liquidity through competition; fees hit it directly.

**In the code.** `fee = 2 * p.eta * n / (n + 1)` in `gmCalc`; the sentence adds the "passed through in the price" clause only when η > 0.

**Teach-back.** (1) Why does η cancel out of LT1's demand but not the market maker's? (2) With n = 9, what fraction of the market maker's 2η reaches the seller?

---

## 3. Measuring liquidity (2.1.3, pp. 26-28)

Reinterpret in a limit order book: LT1 sends market orders that walk the bid side; his average fill S₁ is below the starting mid (the usual proxy for μ). Write the concession as linear in quantity:

> S₁ = μ + λ·q, with λ = -γσ²/n and q = n·i/(n+1) shares sold

λ is price impact per share, the first liquidity measure. Sign convention: q here counts shares sold, so λ is negative; in Kyle positive flow means buying and λ > 0. Same idea.

Second measure: add a t = 0 and a public news shock ε₁. Efficient price μ_t is a martingale; observed S₀ = μ₀, S₁ = μ₁ + λq, S₂ = μ₂. Price changes Δ₁ = ε₁ + λq and Δ₂ = ε₂ - λq, so

> Cov(Δ₁, Δ₂) = -λ²·Var(q) < 0

Liquidity demand pushes the price away and it snaps back. Negative autocovariance of price changes is the fingerprint of illiquidity (bid-ask bounce); Roll's (1984) spread estimator is built on exactly this. The chapter also flags the later distinction between temporary impact (λ, reverts) and permanent impact (information, does not revert).

**In the code.** `lambda = -p.gamma * p.sigma * p.sigma / n` in `gmCalc`, shown as "Price impact λ per share". The autocovariance result is not coded; it is a two-line consequence of the same λ and a natural first extension.

**Book wording to watch.** Page 27 says λ falls with "lower risk tolerance (γ)"; γ is risk aversion, so read lower risk aversion.

**Teach-back.** (1) Why is the autocovariance negative rather than positive? (2) Which of the two measures can you estimate from prices alone, without seeing order flow?

---

## 4. Ho-Stoll, static version: where to rest a quote (2.1.4, pp. 28-30)

Risk-neutral market maker, everyone agrees the mid is S_t. She posts a sell order δ⁺ above the mid. A buy market order arrives with probability p⁺; given arrival it walks at least δ deep with probability e^(-κδ). Expected profit per side is fill chance times margin:

> p⁺ · e^(-κδ) · δ   (equation 2.3)

Differentiate δe^(-κδ): e^(-κδ)(1 - κδ) = 0 gives **δ* = 1/κ**, which is the mean of an Exponential(κ) depth distribution: post where the average aggressive order reaches. At the optimum the fill probability is e^(-1) ≈ 36.8% whatever κ is.

**In the code.** `hRender`: `pi = x => pp * x * Math.exp(-kappa * x)`, `dstar = 1 / kappa`, and the curve is `pi` sampled on 0..xmax with markers at δ* and at your δ. The "% below the optimum" line is 1 - pi(d)/pi(dstar).

**Try this.** Drag κ from 0.5 to 2: δ* drops from 2 to 0.5 ticks and the whole curve compresses toward the mid. Drag p⁺: the curve scales but the maximum does not move. That is the point of the p⁺ slider.

**Teach-back.** (1) Why does the optimal depth not depend on the arrival probability? (2) What in a real book would make κ large?

---

## 5. Kyle: trading an edge when your own order moves the price (2.2, pp. 30-33)

**Setup.** v ~ N(μ, σ²). One insider knows v. Noise traders send u ~ N(0, σᵤ²), independent of v. Risk-neutral competitive market makers see only y = x + u. Zero expected profit forces S = E[v | y]. Guess a linear rule S = μ + λy.

**Insider.** Maximise E[x(v - μ - λ(x+u))] = x(v - μ) - λx². Derivative to zero:

> x* = (v - μ)/(2λ) = β(v - μ), β = 1/(2λ)

Trade in proportion to the mispricing, scaled down by impact: half of what you would trade if the price stood still. A monopolist facing a linear demand curve.

**Market makers.** With x = β(v - μ), the flow y = β(v - μ) + u is a scaled signal plus noise. Tool 6, with E[y] = 0:

> λ = Cov(v,y)/Var(y) = βσ² / (β²σ² + σᵤ²)

**Fixed point.** β depends on λ and λ on β. Substitute β = 1/(2λ): λ(σ²/(4λ²) + σᵤ²) = σ²/(2λ), so σ²/(4λ) + λσᵤ² = σ²/(2λ), so λσᵤ² = σ²/(4λ), so 4λ²σᵤ² = σ²:

> **λ = σ/(2σᵤ), β = σᵤ/σ**

Direction check: more noise (σᵤ up) is more cover for the insider, so market makers trust flow less and λ falls; more uncertainty (σ up) makes flow more informative and λ rises.

**Two consequences.** E[S | v] = μ + λβ(v - μ) = μ + ½(v - μ): one round of trading moves the price halfway to the truth. Insider expected profit E[x(v - S)] = β(1 - λβ)σ² = ½βσ² = σσᵤ/2, and the noise traders' expected loss E[u(S - v)] = λσᵤ² = σσᵤ/2: market makers break even, the informed eat the uninformed.

**In the code.** `kCalc(p)`: `lambda = p.sigma / (2 * p.sigu)`, `beta = p.sigu / p.sigma`, `x = beta * (p.v - p.mu)`, `ES = p.mu + 0.5 * (p.v - p.mu)`, `profit = p.sigma * p.sigu / 2`. `kSample(n)` draws `u = p.sigu * randn()`, forms `y = r.x + u`, prices `S = p.mu + r.lambda * y` and records the insider's realised `x*(v - S)`. Running 100 auctions shows the dots' average height converging on `ES` and average profit on `profit`.

**Book errata to know about.** Page 33 prints λ = 2σᵤ/σ; the derivation gives σ/(2σᵤ) and the direction check confirms it. The same page says x + u has mean μ(1+β); it is zero. Neither error propagates to anything else in the chapter.

**Try this.** Double σᵤ: λ halves, β doubles, x* doubles, expected profit doubles. Set v = 100: no edge, x* = 0, but λ is unchanged because the market makers cannot see that today's flow is pure noise.

**Say it in one sentence.** Price impact is the gain of a linear estimator of value from order flow, and an informed trader trades exactly half as aggressively as he would if that gain were zero.

**Teach-back.** (1) Why is the insider's order linear in (v - μ) rather than "everything he can"? (2) Where does the insider's profit come from, given market makers break even?

---

## 6. Glosten-Milgrom: the spread as insurance against informed traders (2.3, pp. 34-37)

**Setup.** v ∈ {V_H, V_L}, prior P(V_H) = p, so μ = pV_H + (1-p)V_L. A fraction α of traders are informed (buy at the ask if v = V_H, sell at the bid if v = V_L); a fraction 1 - α are uninformed and buy or sell 50/50. One unit per order. Competitive risk-neutral market makers post a = μ + Δₐ and b = μ - Δ_b with zero expected profit on each side.

**Ask side.** A buy arrives. Informed buy: probability αp. Uninformed buy: (1-α)/2. Bayes: P(informed | buy) = αp/(αp + (1-α)/2). Against an uninformed buyer the market maker earns Δₐ; against an informed one the asset is worth V_H and she loses (V_H - μ) - Δₐ. Zero expected profit:

> [(1-α)/2]·Δₐ + αp·[Δₐ - (V_H - μ)] = 0   =>   **Δₐ = P(informed | buy) × (V_H - μ)**

Half-spread equals the probability the counterparty knows something times what it costs you when they do. Equivalently a = E[v | a buy just arrived]. The bid mirrors it with (1-p) and (μ - V_L). Book vocabulary: α is the prevalence of toxicity, V_H - μ and μ - V_L its magnitude.

**Worked numbers.** V_H = 110, V_L = 90, p = ½, α = 0.2: P(informed | buy) = 0.1/(0.1+0.4) = 0.2, Δₐ = 2, ask 102, bid 98. α = 0.5: spread 10. α → 1: spread → 20, the full gap, and nobody trades.

**Dynamics (2.3.1).** Index by time. After each order update p_t = P(V_H | history) by Bayes and re-quote with the same formulas using μ_t = E[v | history]. Buy update: P(buy | V_H) = α + (1-α)/2, P(buy | V_L) = (1-α)/2. In the example one buy moves p from 0.5 to 0.6 and μ to 102, exactly the ask just paid. In general every execution price equals the posterior expectation, so the transaction price sequence is a martingale. The book learns v from the flow.

**Price-sensitive liquidity traders (2.3.2).** Give uninformed trader i an urgency cᵢ; he trades only if the half-spread is below cᵢ. With F the distribution of urgency, only 1 - F(Δₐ) of would-be uninformed buyers trade, so the informed share of flow rises, so the zero-profit spread widens, so more uninformed leave. The equation becomes implicit:

> Δₐ = αp / (αp + (1-α)(1 - F(Δₐ))/2) × (V_H - μ)

If urgency is low the only solution is Δₐ = V_H - μ: market collapse.

**In the code.** `solveHalf(alpha, pSide, magnitude, cmax, urgency)` computes one side. Without urgency it is the closed form: `wInf = alpha * pSide`, `wUn = (1 - alpha) / 2`, `pInf = wInf / (wInf + wUn)`, `delta = pInf * magnitude`. With urgency, F is uniform on [0, cmax], so `frac = 1 - Math.min(d / cmax, 1)` and `wUn = (1 - alpha) * frac / 2`; the loop iterates Δ ← g(Δ) from Δ = 0. Because g is increasing in Δ and g(0) is the closed-form spread, the iterates rise monotonically to the smallest fixed point; if `frac` hits zero the update becomes Δ = magnitude, which is the collapse case. `gQuotes(par, belief)` calls `solveHalf` for both sides with `pSide = belief` for the ask and `1 - belief` for the bid. `drawLadder` places V_L, bid, μ, ask, V_H on a price axis. `gStep()` draws the trader type, decides the side, applies the Bayes update (`m.belief = m.belief * pH / den`) and re-quotes; `gDynRender` plots ask, bid, μ_t and the executions.

**Try this.** With α = 0.2 and the urgency toggle on, drag the ceiling down from 10: the spread widens slowly, then jumps to the full gap somewhere around 6.4. That jump is the fixed-point equation losing its interior root (a quadratic whose discriminant turns negative). In the simulator, run 25 trades and reveal the value: the quotes converge on it, and the spread narrows as p_t approaches 0 or 1 because there is less left to be informed about.

**Say it in one sentence.** The bid-ask spread is the market maker's insurance premium against being picked off, equal on each side to the probability the counterparty is informed times the loss when they are, and every execution price is her updated best guess of value.

**Teach-back.** (1) Why does the ask equal E[v | buy] rather than E[v]? (2) Why does the spread shrink as the simulator runs, even though α has not changed?

---

## 7. How the four fit together

- Spread = **inventory risk premium** (Grossman-Miller: γ, σ², n, size) + **adverse selection premium** (Kyle's λ, Glosten-Milgrom's Δ: α, p, magnitude), plus any **fee** passed through (η).
- Two liquidity measures fall out of one λ: price impact, and negative autocovariance of price changes.
- Kyle gives the aggressor's rule (trade proportional to edge, scaled by 1/(2λ)); Glosten-Milgrom gives the quoter's rule (half-spread = P(informed) × loss if informed); Ho-Stoll gives the posting rule (depth = 1/κ).
- Everything is static and one-shot. Part II of the book supplies stochastic control, and chapter 10 puts inventory, time and adverse selection into one dynamic market-making problem whose spread formula contains recognisable pieces of all four models above.

For a market maker on a real venue, the practical reading of the whole chapter is one inequality: the half-spread you capture must exceed the maker fee plus the expected adverse-selection loss (your markout) plus the inventory risk cost γσ²q, or you are paying to provide liquidity.
