# Microstructure lab

Interactive versions of the four models in chapter 2 of Cartea, Jaimungal and Penalva, *Algorithmic and High-Frequency Trading* (Cambridge University Press, 2015): Grossman-Miller (inventory risk), Kyle (price impact), Glosten-Milgrom (adverse selection) and the static Ho-Stoll posting problem. Every parameter is on a slider; the figure, the key numbers and a plain-English sentence update together.

Live version: `https://dapshima.github.io/microstructure-lab/` once Pages is enabled (steps below).

The whole thing is one file, `index.html`, with no build step and no dependencies. It runs entirely in the visitor's browser, so a hosted copy is exactly as interactive as a local one.

## What each tab answers

| Tab | Model | The question it answers | Book section |
|---|---|---|---|
| Inventory risk | Grossman-Miller (1988) | What does a risk-averse market maker charge for holding your shares until the other side shows up? | 2.1.1 to 2.1.3, pp. 21-28 |
| Price impact | Kyle (1985) | How much does an informed trader trade when his own order moves the price, and how hard do market makers lean on order flow? | 2.2, pp. 30-33 |
| Adverse selection | Glosten-Milgrom (1985) | How wide must the spread be so that a market maker breaks even against traders who know more, and how do quotes learn from the flow? | 2.3 to 2.3.2, pp. 34-37 |
| Posting depth | Ho-Stoll (1981), static | How far from the mid should a limit order rest, trading fill probability against margin? | 2.1.4, pp. 28-30 |

## Slider to symbol

Inventory risk (`gmCalc` in the source)

| Slider | Symbol | Meaning | Where it enters |
|---|---|---|---|
| Market makers competing | n | risk-bearers who share the seller's inventory, plus the seller himself | S₁ = μ - γσ²·i/(n+1) |
| Order size | i | shares the seller must unload today | same |
| Overnight volatility | σ | standard deviation of tomorrow's news shock | same, squared |
| Risk aversion | γ | utility penalty per unit of variance, identical for everyone | same |
| Exchange fee per share | η | paid on every trade; the market maker pays it twice | extra term - 2η·n/(n+1) |

Price impact (`kCalc`, `kSample`)

| Slider | Symbol | Meaning | Where it enters |
|---|---|---|---|
| Uncertainty about value | σ | standard deviation of the true value around the prior | λ = σ/(2σᵤ) |
| Noise trading | σᵤ | standard deviation of the noise traders' net order | same, and β = σᵤ/σ |
| True value the insider sees | v | the insider's private information | x* = β(v - μ) |

Adverse selection (`gQuotes`, `solveHalf`, `gStep`)

| Slider | Symbol | Meaning | Where it enters |
|---|---|---|---|
| Share of traders who are informed | α | prevalence of toxic flow | P(informed \| buy) = αp / (αp + (1-α)/2) |
| Prior chance the value is high | p | market maker's belief before any order | same, and μ = pV_H + (1-p)V_L |
| Value gap | V_H - V_L | what an informed trader knows | Δₐ = P(informed \| buy)·(V_H - μ) |
| Liquidity traders can walk away | F(·) | uninformed traders trade only if the half-spread is below their urgency | implicit equation, section 2.3.2 |

Posting depth (`hRender`)

| Slider | Symbol | Meaning | Where it enters |
|---|---|---|---|
| Book decay | κ | chance an order walks at least δ deep is e^(-κδ) | δ* = 1/κ |
| Chance a market order arrives | p⁺ | arrival probability | scales profit only |
| Your posting depth | δ | the quote you would post, to compare with δ* | p⁺·e^(-κδ)·δ |

## Reading the source

Open `index.html`. The `<style>` block is the design; the script block at the bottom is the model code. Each model is a short block headed by a comment that quotes the book equation and page it implements:

- `gmCalc` (Grossman-Miller): about ten lines. The entire model is one algebraic formula once the demand rule and market clearing are done on paper.
- `kCalc` and `kSample` (Kyle): the closed-form λ and β, plus a Monte Carlo of the one-shot auction so the "halfway to the truth" and "profit equals noise traders' loss" results can be seen converging.
- `solveHalf`, `gQuotes`, `gStep` (Glosten-Milgrom): the half-spread formula, its price-sensitive extension solved by fixed-point iteration, and the Bayesian updating that drives the trade-by-trade simulator.
- `hRender` (Ho-Stoll): the profit curve and its maximum.
- `Chart`: a small canvas plotting helper so the page needs no libraries.

The derivations, step by step, with the code lines they correspond to, are in [`docs/theory-to-code.md`](docs/theory-to-code.md). A script for presenting the lab in a short video is in [`docs/teaching-script.md`](docs/teaching-script.md).

## Publishing on GitHub Pages

1. Create a public repository named `microstructure-lab` and push this folder to it (`index.html`, `README.md`, `docs/`, `LICENSE`).
2. In the repository, open Settings, then Pages. Under Build and deployment choose "Deploy from a branch", branch `main`, folder `/ (root)`. Save.
3. After a minute the site is live at `https://dapshima.github.io/microstructure-lab/`. Every push to `main` redeploys it.

To embed the lab in another page of your own site, use an iframe: `<iframe src="https://dapshima.github.io/microstructure-lab/" width="100%" height="1200" style="border:0"></iframe>`.

The page loads two typefaces from Google Fonts. Delete the two `<link>` lines in the `<head>` to make it fully offline; it falls back to system fonts.

## What this is not

These are the static, one-period versions of each model, exactly as the chapter presents them. There is no time, no inventory dynamics and no interaction between the four models; the book builds those in Part III (chapter 10 for market making). All numbers are illustrative and no parameter is calibrated to a real market. The point is to make the mechanics of each equation visible.

## Credits

Models: Grossman and Miller (1988), Kyle (1985), Glosten and Milgrom (1985), Ho and Stoll (1981), as presented in Cartea, Jaimungal and Penalva (2015), chapter 2. Code and documentation in this repository are released under the MIT licence (see `LICENSE`).
