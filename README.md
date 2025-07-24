# 📘 Slippage Impact Model

Python notebook for nonlinear market impact modeling and trade schedule optimization.

---

## Task 1: Modeling Temporary Market Impact gₜ(x)

We very often see the simple form:

gₜ(xₜ) = βₜ · xₜ

When one transacts a small amount, the price increment takes time; however, that is not the case in real markets.  
We checked 3 tickers and discovered three patterns:

- Big gaps in the order book indicate prices hardly own any grounds.  
- It is possible to purchase a large amount of stock at once and drive the value up.  
- When you enter a large order into the market, the slippage occurs quickly.

### A More Practical Way

Instead, we represent impact as:

gₜ(xₜ) = αₜ · xₜ + βₜ · xₜ²

- αₜ · xₜ: normal price movement  
- βₜ · xₜ²: cost increases in a nonlinear way with trade size

To support this, we used:

- Order book snapshots across 3 symbols  
- Trade size vs slippage graph  
- Curve fitting → figured out convex-shaped behaviors, i.e. cost goes up faster than trade size

This model gave us a much cleaner fit, captured what actually happens the first time you trade on a big scale, and was simple enough to be optimized efficiently.

---

## Task 2: Optimizing Trading Schedule with Nonlinear Impact

We wish to purchase S shares in 390 minutes. At each moment tᵢ, we determine how many pieces we can trade xᵢ, so that:

∑₍ᵢ₌₁₎⁽³⁹⁰⁾ xᵢ = S

The cost per trade is modeled as:

gᵢ(xᵢ) = αᵢ · xᵢ + βᵢ · xᵢ²

To minimize total cost, we use Lagrange multipliers:

minimize ∑₍ᵢ₌₁₎⁽³⁹⁰⁾ (αᵢ · xᵢ + βᵢ · xᵢ²)

Define the Lagrangian:

L(x₁, ..., x₃₉₀, λ) = ∑₍ᵢ₌₁₎⁽³⁹⁰⁾ (αᵢ · xᵢ + βᵢ · xᵢ²) + λ · (S − ∑ xᵢ)

Set gradient to zero:

xᵢ = (λ − αᵢ) / (2 · βᵢ)

Solve for λ, plug back in, and you get the optimal trade schedule.
