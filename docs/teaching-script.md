# Presenting the lab: a script for a seven-minute video

Format: screen recording of the live page, you talking over it. One tab per segment, about 90 seconds each, plus a 40-second opening and a 40-second close. Every segment has the same shape: say the question, drag one slider, read the sentence that changes, state the rule. Do not read formulas aloud; point at them.

Before recording, do a dry run where you answer every teach-back question in `theory-to-code.md` without notes. If one stalls you, that is the segment to rehearse.

## Opening (40 seconds)

"Chapter 2 of Cartea, Jaimungal and Penalva asks one question four ways: why does a market maker get paid, and how much? I turned the four models into sliders so I could see the equations move. Two things pay a market maker: carrying inventory she doesn't want, and trading against people who know more than she does. Let me show you both."

## Segment 1: Inventory risk, Grossman-Miller (90 seconds)

Say: "A seller has to unload 100 shares today. The buyer arrives tomorrow. Someone has to hold those shares overnight, and nobody does that for free."

Drag: number of market makers from 5 up to 25. Point at the curve falling and the concession number shrinking.

Say: "The discount is γσ² times the inventory each risk-bearer ends up holding. More market makers, smaller share each, smaller discount. Watch volatility instead." Drag σ from 2 to 4. "Four times the discount, because it's σ squared."

Rule to state: "Inventory risk premium: risk aversion times variance times the position each person is stuck with."

## Segment 2: Price impact, Kyle (90 seconds)

Say: "Now one trader knows the true value. Market makers see only total order flow, signal plus noise, and price it like a regression: price equals prior plus λ times flow."

Click: Run 100 auctions. Point at the dots.

Say: "The insider sends half of what he'd trade if prices didn't move, because his own order moves them. The price ends up halfway to the truth on average. His profit is exactly what the noise traders lose; the market makers break even."

Drag: noise trading σᵤ up. "More noise, more cover, so λ falls and the insider trades bigger. This λ is the standard measure of adverse selection cost."

Rule to state: "Kyle's lambda is the market's regression coefficient of price on flow."

## Segment 3: Adverse selection, Glosten-Milgrom (2 minutes)

Say: "From the market maker's chair: every buy order might be from someone who knows the value is high. She can't tell. So she charges every buyer a half-spread equal to the probability the buyer is informed times what she loses when he is."

Drag: α from 0.2 to 0.5. Point at the ladder: ask up, bid down, spread 4 to 10. Then to 1: "Nobody uninformed left to pay for it. Spread is the whole gap, no trades. That's a market shutting."

Reset α to 0.2. Click New market, then Run 25 trades, then Reveal the true value.

Say: "Now watch the book learn. Each order updates her belief by Bayes' rule; she re-quotes; each trade prints at her new best guess. The quotes walk to the truth and the spread narrows because there's less left to know. Trade prices are a martingale."

Toggle: Liquidity traders can walk away; drag the ceiling down until it jumps.

Say: "If uninformed traders leave when the spread is wide, the flow gets more toxic, the spread widens, more leave. Past a threshold there's no interior solution. That's a liquidity death spiral in one equation."

Rule to state: "Half-spread equals probability informed times loss when informed; every execution is a posterior mean."

## Segment 4: Posting depth, Ho-Stoll (60 seconds)

Say: "Last one is simple: how far from the mid do you rest a quote? Too tight, you fill often for a sliver; too deep, fat margin, no fills."

Drag: κ from 0.5 to 2. Point at the peak moving toward the mid.

Say: "Optimal depth is 1/κ, the average distance an aggressive order walks into the book. And at the optimum you fill 36.8% of the time, always, whatever κ is. The arrival rate slider scales the curve but never moves the peak."

Rule to state: "Post where the average aggressive order reaches."

## Close (40 seconds)

"So a spread is two premiums stacked: inventory risk from Grossman-Miller, adverse selection from Kyle and Glosten-Milgrom, plus whatever fee the exchange takes, which the chapter shows is passed almost entirely to the liquidity taker. Chapter 10 of the book puts all of it into one dynamic quoting problem, which is where I'm heading next. The page and the derivations are on GitHub, link below. If you spot a mistake, tell me; I'd rather know."

## Recording notes

- Record at 1280x720 or wider so the readout numbers stay legible on a phone.
- Reset every tab to defaults before each segment (reload the page).
- Keep the cursor still while talking; move it only to point.
- The Glosten-Milgrom simulator is random. Do two or three takes and keep the one where convergence is clean but not instant.

## A caption, in plain words

Turned chapter 2 of Cartea, Jaimungal and Penalva into a page of sliders so I could see the equations move instead of just reading them. Four models: Grossman-Miller, Kyle, Glosten-Milgrom, Ho-Stoll. Each one is about ten lines of code once the derivation is done on paper, and the derivations are in the repo. Two typos in the book that I hit along the way are noted too. Feedback welcome, especially from people who quote for a living.
