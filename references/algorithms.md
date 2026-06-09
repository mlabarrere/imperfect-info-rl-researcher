# Algorithms for imperfect-information games

A decision-oriented taxonomy. For each family: **what it computes**, **when to use
it**, **guarantees**, **scalability / compute profile**, and **failure modes**.
Default to OpenSpiel implementations (see `tooling.md`) unless you have a reason
not to.

The dominant paradigm for two-player zero-sum (2p0s) imperfect-information games is
**iterative self-play that provably converges to a Nash equilibrium** — chiefly the
CFR family. General-sum and many-player settings have weaker guarantees and lean on
population methods and empirical game theory.

---

## 1. Regret minimization — the CFR family

**Core idea.** Treat each information set as an independent regret-minimizing
learner. Minimizing **counterfactual regret** at every information set bounds
overall regret; by the folk theorem on regret, the **average** strategy (not the
current one) converges to a Nash equilibrium in 2p0s games. Convergence rate of
average overall regret is O(1/√T).

| Variant | One-line | Use when | Notes / failure modes |
|---|---|---|---|
| **Vanilla CFR** (Zinkevich 2007) | Full tree traversal each iteration | Small/medium games where you can enumerate the tree | Baseline; superseded in practice by CFR+ |
| **CFR+** (Tammelin 2014) | Regret-matching+ (floor regrets at 0) + linear averaging; no alternating-update guarantee proof but excellent empirically | Default tabular solver; solved heads-up limit hold'em (Cepheus) | Much faster constants than CFR; still full traversal |
| **Linear CFR / Discounted CFR (DCFR)** (Brown & Sandholm 2019) | Discount early (bad) iterations | Faster practical convergence than CFR+ on many games | DCFR(α,β,γ) hyperparameters; α=1.5,β=0,γ=2 is the common default |
| **Monte Carlo CFR (MCCFR)** (Lanctot 2009) | Sample part of the tree per iteration | Game too large to fully traverse each iteration | Higher variance; **outcome-sampling** (one trajectory, high variance, model-free-ish) vs **external-sampling** (sample only opponent+chance, lower variance, usually preferred) |
| **Deep CFR** (Brown 2019) | Neural nets regress to cumulative regrets; reservoir-buffer training | Large games where tabular tables don't fit; you have a GPU | Approximation + sampling error; needs careful buffers; reproduce on Leduc first |
| **Single Deep CFR (SD-CFR)** (Steinberger 2019) | Removes the separate average-strategy network | Same as Deep CFR, cleaner | — |
| **DREAM** (Steinberger 2020) | Model-free, single-sample, variance-reduced (baseline) deep CFR | Model-free large games | — |
| **ESCHER** (McAleer 2023) | Removes importance sampling; scales better than Deep CFR/DREAM | Large games where IS variance hurts | Newer; fewer reference runs |

**Key correctness traps for the whole family:**
- Convergence is for the **average strategy**, not the current iterate. Evaluate the
  average.
- 2p0s only for the Nash guarantee. In general-sum / >2 players CFR may not converge
  to Nash (can converge to a coarse-correlated equilibrium in self-play; no
  per-player optimality guarantee).
- "NashConv went to ~0" on a neural variant after few iterations is almost always an
  artifact, not a solved game (see `metrics-and-evaluation.md`).

---

## 2. Fictitious play and its neural form

| Variant | One-line | Use when | Notes |
|---|---|---|---|
| **Fictitious Play (FP)** | Best-respond to opponent's average strategy | Theoretical baseline; normal-form | Slow; converges in 2p0s and some classes |
| **Fictitious Self-Play (FSP)** (Heinrich 2015) | FP in extensive form via approximate best response | Bridge from FP to scalable methods | — |
| **NFSP** (Heinrich & Silver 2016) | Each agent mixes a DQN best-response net and a supervised average-policy net | First end-to-end deep RL method to reach approximate Nash in poker without abstraction | Sensitive to anticipatory parameter and reservoir sizing; slower than modern CFR; good when you want a pure-RL, no-tree-traversal approach |

---

## 3. Double oracle / population methods (PSRO family)

**Core idea.** Maintain a population of policies; repeatedly compute a meta-strategy
(a distribution over the population, e.g. via a meta-solver) and train a new
best-response policy against it; add it to the population.

| Variant | One-line | Use when |
|---|---|---|
| **Double Oracle** | Classic; exact best responses over a growing strategy set | Small normal-form / when BR is computable |
| **PSRO** (Lanctot 2017) | Deep RL best responses + meta-game solved by a meta-solver (Nash, alpha-rank, uniform) | General-sum, many-player, or large games where you want modular RL |
| **Pipeline PSRO (P2SRO)** (McAleer 2020) | Parallelizes the BR training pipeline | Scaling PSRO with more compute |
| **XDO / Anytime Double Oracle** (McAleer 2021) | Extensive-form double oracle with better convergence in sequential games | Sequential games where PSRO's normal-form meta-game blows up |

**Failure modes:** meta-solver choice matters a lot; PSRO can be sample-inefficient;
the empirical meta-game can mislead if best responses are weak. Pairs with
**alpha-rank** (see metrics) for ranking populations in general-sum settings.

---

## 4. Test-time search (and RL + search)

Search at decision time is what pushed poker to superhuman. The hard part in
imperfect information is that you **cannot search a subgame in isolation** — its
values depend on beliefs/reach probabilities from the rest of the game.

| Method | One-line | Use when | Notes |
|---|---|---|---|
| **IS-MCTS** (Cowling 2012) | MCTS over information sets, sampling determinizations | Strong, simple online baseline with no training | Exploitable; not an equilibrium; but a serious bar to beat |
| **Continual resolving / DeepStack** (Moravčík 2017) | Re-solve subgames online with learned counterfactual value networks | Large 2p0s with reach-dependent subgames | Needs CFV networks; theoretically grounded |
| **Safe & nested subgame solving** (Brown & Sandholm 2017, Libratus) | Re-solve subgames without becoming more exploitable | Endgame solving in large games | The "safe" qualifier is the whole point |
| **ReBeL** (Brown 2020) | Unifies RL + search by treating belief-state public-belief-states as the MDP state; provably converges in 2p0s | When you want a single principled RL+search recipe | Superhuman HUNL with far less domain knowledge |
| **Player of Games** (Schmid 2021) | CFR + growing-tree search; one algorithm for perfect and imperfect info | General game-playing across both regimes | Generalist; heavy |

**Failure mode:** naive determinization ("assume the hidden state, solve a perfect-
info game, average") is **strategy fusion** and can be badly exploitable. It is a
baseline, not a solution.

---

## 5. Policy-gradient / regularized RL in games

| Method | One-line | Use when | Notes |
|---|---|---|---|
| **NeuRD** (Hennes 2020) | Neural Replicator Dynamics; policy-gradient that follows replicator dynamics | RL-style training with game-theoretic convergence properties | — |
| **Magnetic Mirror Descent (MMD)** (Sokota 2023) | Regularized mirror descent; last-iterate convergence to QRE/Nash in 2p0s | When you want **last-iterate** (not average) convergence and a single RL-like loop | Elegant; ties RL and equilibrium computation |
| **(F)FoReL / regularization** | Follow-the-Regularized-Leader variants | Theoretical framing of the above | — |
| **R-NaD / DeepNash** (Perolat 2022) | Regularized Nash Dynamics; reached human-expert Stratego | Very large imperfect-info games, model-free | Large-scale; the Stratego result |

Plain self-play policy gradient (PPO et al.) has **no equilibrium guarantee** in
imperfect-information / general-sum games and can cycle. Use it only with eyes open,
and measure exploitability, not just self-play win rate.

---

## Quick selector

- **Small game, want a certified equilibrium / ground truth** → CFR+ or DCFR
  (tabular), measure exact NashConv.
- **Medium game, tabular tables still fit** → MCCFR (external sampling).
- **Large game, tables don't fit, have a GPU** → Deep CFR / SD-CFR / ESCHER, or
  ReBeL if you want search.
- **Want pure deep-RL self-play, no tree traversal** → NFSP, or MMD for last-iterate.
- **General-sum or many players** → PSRO (+ alpha-rank meta-solver); abandon the
  single-Nash framing.
- **Strong online baseline with zero training** → IS-MCTS.
- **Superhuman in a large 2p0s game** → search at test time is almost always
  required (DeepStack/Libratus/ReBeL lineage); pure offline solving rarely suffices.

Always reproduce the method on Kuhn/Leduc (or your shrunk game) and confirm NashConv
decreases before scaling to the target game.
