# Metrics and evaluation

Choosing the wrong metric is how serious-looking projects fool themselves. In
imperfect-information games the **progress metric and the strength metric are
different things**, and conflating them is the canonical mistake.

- **Progress toward equilibrium** → exploitability / NashConv (or LBR when exact is
  infeasible).
- **Strength against specific opponents** → head-to-head win rate with confidence
  intervals; Elo/Glicko; alpha-rank for populations.

A low exploitability does **not** imply high win rate vs a weak bot, and a high win
rate vs a weak bot does **not** imply low exploitability. State which question you
are answering.

---

## 1. Exploitability / NashConv (the gold standard)

For a strategy profile σ, **NashConv(σ) = Σ_i [ u_i(BR_i, σ_{-i}) − u_i(σ) ]**: the
total amount each player could gain by switching to a best response. **Exploitability**
is usually NashConv/2 (the per-player average) in 2p0s. At a Nash equilibrium it is 0.

- **What it tells you:** how far from equilibrium you are — the true, opponent-
  independent quality signal in 2p0s games.
- **Computable exactly** only when you can traverse the full game tree to compute
  best responses. Feasible for Kuhn, Leduc, and small/shrunk variants; infeasible for
  full poker, Bridge, full Chkobba, etc.
- **Units matter:** report in the game's payoff units (e.g. mbb/g in poker) and
  always against a **baseline** (uniform-random profile's NashConv) so the reduction
  is interpretable, e.g. "NashConv 5.09 → 0.08, a 62× reduction; certified < 0.1× baseline".

### Anti-artifact guard (read this twice)
An **untrained or degenerate neural policy can report NashConv ≈ 0** — e.g. if it
outputs a near-deterministic or numerically degenerate distribution that the
best-response computation mishandles, or if the policy and the evaluator share a bug.
**Near-zero NashConv after very little training is almost always an artifact, not a
solved game.** Defenses:
- Validate the policy is a proper probability distribution over legal actions at
  every queried information set (sums to 1, no negatives, support ⊆ legal actions).
- Sanity-check that the *uniform-random* baseline reports a clearly positive NashConv
  and that your number sits sensibly below it, not implausibly far below.
- Cross-check with an independent strength measurement (does the "solved" agent
  actually beat a random/greedy bot with a tight CI?).
- Reproduce the whole pipeline on Kuhn/Leduc where the equilibrium value is known.

---

## 2. Local Best Response (LBR) — when exact is impossible

**LBR** (Lisý & Bowling 2017) approximates a lower bound on exploitability by having
a simple, shallow best-responder probe the agent. A high LBR value is a *proof of
exploitability*; a low LBR value is **not** a proof of low exploitability (it only
means *that* limited responder couldn't exploit it). Use LBR to catch egregiously
exploitable agents in large games where true exploitability is uncomputable.

---

## 3. Head-to-head win rate + confidence intervals

A win rate is an estimate of a Bernoulli (or multinomial) parameter from n games. A
point estimate without an interval is not a result.

- **Wilson score interval** for a proportion (preferred over normal approximation,
  especially near 0/1 and for small n). Report `winrate, CI[lo, hi], n`.
- **Match the seats / alternate positions.** In asymmetric games, play each pairing
  in both seats (duplicate / mirrored deals where possible) to remove positional and
  card-luck bias.
- **Significance:** to claim A > B, the intervals/test must support it at your stated
  n; otherwise report "no significant difference at n=…".

### Variance reduction — AIVAT
High-variance games (poker especially) need enormous n for tight CIs. **AIVAT**
(Burch 2018) is an unbiased, low-variance estimator that subtracts a control variate
based on chance events and value baselines, often cutting required sample sizes by an
order of magnitude. Use it (or at least mirrored/duplicate deals) before concluding
you need millions of hands.

---

## 4. Ratings: Elo, Glicko-2, alpha-rank

- **Elo / Glicko-2:** convert tournament results into a single rating per agent.
  Glicko-2 adds a rating-deviation (uncertainty) and is preferable with few games.
  Good for tracking a ladder of agents over time. Requires enough games per pairing
  (rule of thumb: ≥ ~100 games/pair for stable estimates in noisy games).
- **Alpha-rank** (Omidshafiei 2019): an evolutionary-dynamics ranking over a
  population/meta-game. Unlike Elo it does not assume transitivity and is well-suited
  to **general-sum and intransitive** (rock-paper-scissors-like) populations, and is
  the principled meta-solver for PSRO. Use it when "who's best" is not a total order.

Elo is a **relative, transitive** strength measure; it is not a distance to
equilibrium. Never report Elo as evidence of optimality.

---

## 5. Which metric for which question

| Question | Metric |
|---|---|
| How close to Nash am I? (small/tractable game) | Exact NashConv / exploitability vs uniform baseline |
| Am I at least not horribly exploitable? (large game) | LBR (lower bound only) |
| Does A beat B? | Win rate + Wilson CI, mirrored deals, n stated |
| Poker-scale variance killing me? | AIVAT or duplicate deals |
| Rank a ladder of agents | Elo / Glicko-2 (≥100 games/pair) |
| Rank an intransitive / general-sum population | Alpha-rank |

## Reporting rules

- Every number carries its **n, seed(s), and compute** (iterations/samples/wall-clock/hardware).
- Compare methods only at **matched compute**.
- Distinguish the **average** strategy (what CFR converges with) from the current iterate.
- State the metric's **limitation** alongside the number (e.g. "LBR is a lower bound").
