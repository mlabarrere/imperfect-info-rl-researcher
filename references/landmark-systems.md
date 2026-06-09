# Landmark systems — what they did and what to steal

Calibrate ambition against what actually worked, and against what it cost. The
recurring lesson: **for large two-player zero-sum imperfect-information games,
test-time search (re-solving subgames) plus learned values is what reached
superhuman — not offline solving alone — and the compute is large.**

Compute figures below are order-of-magnitude, from the papers/reporting; treat them
as calibration, not exact quotes.

---

## Poker — the proving ground

- **Cepheus** (Bowling, Burch, Johanson, Tammelin, 2015) — *Heads-up limit hold'em is
  essentially solved.* Method: **CFR+** run to tiny exploitability. Scale: ~900
  core-years of computation, ~262 TB of storage for the average strategy.
  **Steal:** CFR+ is the workhorse exact solver; exploitability is the certificate;
  "essentially solved" was *defined* as exploitability below a threshold a lifetime
  of play couldn't statistically detect.

- **DeepStack** (Moravčík et al., 2017) — first to beat professionals at **heads-up
  no-limit** hold'em. Method: **continual re-solving** of subgames online with
  **deep counterfactual value networks** as the leaf evaluator. **Steal:** you can
  avoid solving the whole game by re-solving subgames at decision time with learned
  values — the imperfect-info analogue of a value network + search.

- **Libratus** (Brown & Sandholm, 2017) — beat top HUNL pros. Method: blueprint
  strategy via MCCFR on an abstraction + **safe/nested subgame solving** for endgames
  + self-improvement that patched holes opponents found. **Steal:** *safe* subgame
  solving (re-solve without becoming more exploitable); abstraction + endgame
  refinement; fix your own most-exploited spots.

- **Pluribus** (Brown & Sandholm, 2019) — superhuman **6-player** no-limit hold'em,
  for a few thousand dollars of compute. Method: blueprint via MCCFR self-play +
  depth-limited search at test time. **Steal:** many-player superhuman play is
  possible *cheaply* with self-play + limited search; you do **not** always need
  industrial compute when the search is good and the abstraction is right. Note: no
  equilibrium guarantee in 6-player; it worked empirically.

- **ReBeL** (Brown, Bakhtin, Lerer, Gong, 2020) — superhuman HUNL with far less
  domain knowledge, **unifying RL and search**. Treats the **public belief state** as
  the MDP state and runs RL+search over it, with proven convergence to Nash in 2p0s.
  **Steal:** the cleanest principled recipe to combine learning and search in
  imperfect info; a strong default mental model.

---

## Beyond poker

- **Player of Games** (Schmid et al., 2021) — one algorithm (CFR-style growing-tree
  search + learning) competitive across **both perfect and imperfect** information
  games (chess, Go, poker, Scotland Yard). **Steal:** generality is achievable;
  search + CFR is a unifying frame.

- **DeepNash / R-NaD** (Perolat et al., 2022) — human-expert **Stratego** (a huge
  imperfect-info game) via **model-free** Regularized Nash Dynamics, no search, no
  tree traversal. **Steal:** regularized policy-gradient dynamics can reach
  approximate Nash at enormous scale without search.

- **DouZero** (Zha et al., 2021) — superhuman **DouDizhu** (3-player, imperfect info,
  cooperation+competition) via plain Deep Monte-Carlo (actor-critic with action
  encoding), trained on modest hardware (days on a single server). **Steal:**
  sometimes a simple, well-engineered self-play RL method beats elaborate machinery —
  and it can be cheap.

- **Suphx** (Li et al., 2020, Microsoft) — first AI above most human players on
  **Mahjong** (4-player, huge hidden info). Deep RL + global reward prediction +
  oracle-guided distillation + run-time policy adaptation. **Steal:** domain-specific
  reward shaping and run-time adaptation matter in messy multi-player games.

- **Hanabi** (the "new frontier", Bard et al., 2019) — fully **cooperative**
  imperfect info; the challenge is theory-of-mind / convention formation, not
  adversarial equilibrium. **Steal:** cooperative imperfect-info needs different tools
  (e.g. other-play, public belief reasoning), not exploitability minimization.

---

## Compute calibration (so you don't over-promise)

| System | Game | Rough compute |
|---|---|---|
| Cepheus | HU limit hold'em | ~900 core-years; ~262 TB strategy |
| Libratus | HUNL | millions of core-hours (supercomputer) |
| Pluribus | 6-max NL | ~$150 of cloud compute for the blueprint (notably cheap) |
| ReBeL | HUNL | large-scale GPU training + search |
| DeepNash | Stratego | large-scale (many GPU-weeks) |
| DouZero | DouDizhu | days on a single 48-core / few-GPU server |
| Deep CFR (research scale) | Leduc/medium poker | tens of GPU-hours |

**Implications for a CPU-only or modest-hardware project:**
- Exact NashConv certification on a **shrunk** game (Kuhn/Leduc scale) is achievable
  on a laptop and is a legitimate, publishable contribution.
- Full-game superhuman play in a large game is typically **compute-bound**: the right
  honest framing is "the method trains; here is the scaling curve; the blocker is
  compute, which is provisioning, not methodology." Pluribus and DouZero show the
  bound is not always industrial — good search/engineering can collapse it.
- Always position your contribution against the **strongest existing baseline** for
  the specific game (often IS-MCTS for card games lacking prior equilibrium work).
