# Tooling — OpenSpiel first

**OpenSpiel** (DeepMind) is the de-facto research standard for imperfect-information
and multi-agent games: a large library of games and the canonical implementations of
CFR, MCCFR, Deep CFR, NFSP, PSRO, IS-MCTS, exploitability/NashConv, and ranking
(Elo, alpha-rank). Default to it. Reproduce a known result on a built-in game
(Kuhn/Leduc) before trusting your harness on a custom game.

> The snippets below are **illustrative idioms**, not verified runs. APIs drift
> between OpenSpiel versions — confirm signatures against the installed version
> (`help(...)`, the source, or the official examples) before relying on them, and
> never report a result you have not actually executed.

## Why OpenSpiel
- Canonical, peer-reviewed implementations → you compare against the field, not a
  homebrew solver with subtle bugs.
- Built-in **exact exploitability/NashConv** → the certification metric for free.
- A custom game implemented against the OpenSpiel `Game`/`State` API immediately gets
  *every* algorithm and metric — and is potentially upstreamable.

## Install (CPU is fine for tabular methods)
```bash
pip install open_spiel        # prebuilt wheels exist for common platforms
# Deep methods (Deep CFR, NFSP, PSRO, ESCHER) additionally need torch (or TF/JAX).
```

## Load a game and inspect it
```python
import pyspiel
game = pyspiel.load_game("leduc_poker")      # or "kuhn_poker", "liars_dice", ...
print(game.num_players(), game.num_distinct_actions())
state = game.new_initial_state()
while not state.is_terminal():
    if state.is_chance_node():
        outcomes, probs = zip(*state.chance_outcomes())
        state.apply_action(np.random.choice(outcomes, p=probs))
    else:
        state.apply_action(state.legal_actions()[0])
```

## Tabular CFR / CFR+ and exact exploitability
```python
from open_spiel.python.algorithms import cfr, exploitability

solver = cfr.CFRPlusSolver(game)             # or cfr.CFRSolver(game)
for it in range(10_000):
    solver.evaluate_and_update_policy()
avg_policy = solver.average_policy()         # converge with the AVERAGE policy
conv = exploitability.nash_conv(game, avg_policy)   # exact NashConv (small games only)
print(f"iter={it} NashConv={conv:.6f}")
```
`exploitability.exploitability(game, policy)` gives the per-player average;
`nash_conv` gives the summed version — be explicit about which you report.

## Monte Carlo CFR (when the tree is too big to traverse fully)
```python
from open_spiel.python.algorithms import external_sampling_mccfr as ext_mccfr
solver = ext_mccfr.ExternalSamplingSolver(game, ext_mccfr.AverageType.SIMPLE)
for _ in range(1_000_000):
    solver.iteration()
avg_policy = solver.average_policy()
# outcome_sampling_mccfr is the higher-variance, single-trajectory alternative.
```

## Deep CFR / NFSP / PSRO (neural; benefit from a GPU but run on CPU)
```python
from open_spiel.python.pytorch import deep_cfr        # DeepCFRSolver
from open_spiel.python.pytorch import nfsp            # NFSP agents
from open_spiel.python.algorithms import psro_v2      # PSRO meta-loop
```
Make device selection portable so the same code runs on a laptop or a GPU box:
```python
import torch
device = "cuda" if torch.cuda.is_available() else "cpu"
```
Save `state_dict()` checkpoints (device-agnostic; resume with `map_location`).

## IS-MCTS (strong online baseline, no training)
```python
from open_spiel.python.algorithms import ismcts
evaluator = ismcts.RandomRolloutEvaluator(n_rollouts=2, seed=0)
bot = ismcts.ISMCTSBot(game=game, evaluator=evaluator,
                       uct_c=2.0, max_simulations=1000, ...)
action = bot.step(state)
```

## Ranking a population
```python
from open_spiel.python.algorithms import elo          # Elo from match results
from open_spiel.python.egt import alpharank            # alpha-rank over a meta-game
```

## Other ecosystems (know they exist)
- **RLCard** — many card games (Limit/No-Limit Hold'em, DouDizhu, Mahjong, UNO,
  Leduc); easy environments and rule-based agents; good for quick baselines.
- **PokerRL** — research framework behind Deep CFR / SD-CFR poker experiments.
- **PettingZoo** — general multi-agent API (AEC/parallel); broad but not
  equilibrium-focused; fine for RL plumbing, not for exploitability work.

## Implementing your own game in OpenSpiel
Subclass the `Game` / `State` API (Python is fine to start; port hot paths to C++
only if CFR throughput on millions of information sets demands it). Critical
discipline:
- **Delegate the rules to a single validated engine**; do not reimplement scoring or
  legality inside the OpenSpiel layer.
- Verify the wrapper **node-by-node against that engine** (oracle-parity test) before
  running any algorithm — a rules bug silently corrupts every downstream metric.
- Make the **observation/information-state tensor leak-free** (perfect recall, no
  hidden information). Audit it explicitly; leakage is the most common silent bug.
- Keep state cheap to clone (immutable data) — CFR/MCCFR clone states constantly.
