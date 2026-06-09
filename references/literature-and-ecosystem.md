# Finding, appraising, and the ecosystem (don't reinvent the wheel)

Three researcher skills that knowledge alone does not give you: **knowing where the
information lives**, **judging whether a method is actually sound from the academic
record**, and **knowing the tool/library/site ecosystem** so you build on what exists
instead of rebuilding it badly. Pairs with `references/the-research-loop.md` §3
(verify, don't hallucinate) and `references/tooling.md` (code specifics).

> Treat every resource and URL below as a **starting point to verify**, not a quotable
> fact. Projects get archived, sites move, results get overturned. Confirm currency and
> maintenance status before relying on anything.

---

## 1. Where the information lives — a sourcing map

Start with the closest practical prior art, not with moonshot theory. A strong
researcher checks whether a master thesis, student project, benchmark repo, course
report, or issue thread already solved the boring version of the problem before
proposing a new algorithm. These sources are not automatically authoritative, but they
often reveal implementation traps, baseline choices, game encodings, and negative
results that never make it into polished papers.

**Find papers (and their code/citations):**
- **arXiv** — preprints; the relevant categories are `cs.GT` (game theory),
  `cs.LG`/`stat.ML` (learning), `cs.MA` (multi-agent), `cs.AI`. Fast but **not
  peer-reviewed** — treat as preprint (see §2).
- **Google Scholar** — broadest index; use the **"Cited by"** graph to find follow-up
  work, especially papers that *qualify or refute* the one you're reading.
- **Semantic Scholar** — TLDRs, citation *context* (was it cited approvingly or as a
  counterexample?), and an open API (S2) for programmatic search.
- **OpenReview** — the actual **peer reviews + author rebuttals** for ICLR/NeurIPS
  etc. Reading the reviews tells you the community's real objections, not just the
  polished camera-ready.
- **Papers with Code** — links papers ↔ official/community code ↔ benchmark
  leaderboards; fastest way to find a reference implementation and see SOTA.
- **Connected Papers** — visual citation neighborhood; good for mapping a subfield
  quickly and finding the seminal node.
- **DBLP** — clean bibliographic records and venue listings (authoritative for
  author/venue/year when verifying a citation).

**Find practical and student-level prior art:**
- University repositories for **master's theses, PhD theses, capstone projects, and
  course reports**. Search for the exact game name plus "CFR", "OpenSpiel",
  "self-play", "bot", "thesis", "project", or "report".
- GitHub/GitLab repositories, forks, issues, pull requests, READMEs, and experiment
  logs. Treat code that actually runs on the target game as evidence of feasibility,
  not as proof of correctness.
- Competition write-ups, benchmark leaderboards, Kaggle/Colab notebooks, ACPC-style
  agent descriptions, and course project pages. They often identify the obvious
  baseline and the hidden engineering constraints.
- Blog posts and lab notes from students or practitioners. Use them to discover
  pitfalls and pointers; verify scientific claims through papers, code, or your own
  runs.

**The venues that matter in this field** (to gauge peer-review rigor and to browse):
NeurIPS, ICML, ICLR (learning); AAMAS, IJCAI, AAAI, UAI (agents/AI); EC (Economics &
Computation) and journals like *Games and Economic Behavior* (game theory); *Science*
/ *Nature* for the landmark poker/Stratego results.

**Domain-specific hubs:**
- **OpenSpiel** repository + its paper (Lanctot et al., 2019) — canonical
  implementations and a games list; the issues/discussions are a knowledge base.
- **University of Alberta Computer Poker Research Group (CPRG)** and the historical
  **Annual Computer Poker Competition (ACPC)** — origin of much of the CFR lineage and
  of standard benchmarks/agents.
- Research-lab outputs: **DeepMind**, **Meta AI / FAIR**, **Microsoft Research**
  publication pages and blogs (DeepStack, ReBeL, Player of Games, DeepNash, Suphx
  came from these).

**Staying current (against cutoff rot):** Papers with Code "trending", arXiv listings
for the categories above, OpenReview for the latest cycle, and reproducibility venues
(below). When recency matters, **search live** — do not answer "what's SOTA now" from
frozen memory.

---

## 2. Appraising a method from the academic record — is it actually sound?

A paper existing is not evidence the method works for *you*. Vet it deliberately:

1. **Provenance.** Peer-reviewed (which venue, how selective) vs **preprint** (arXiv,
   un-vetted) vs **blog/marketing**. A preprint can be excellent or wrong — it has not
   been refereed. Weight accordingly; never cite a blog as if it were a result.
2. **Read the assumptions, not the abstract.** What does the theorem *actually*
   assume — two-player zero-sum? perfect recall? bounded payoffs? a specific
   regularizer? **Do those assumptions hold in your setting?** Most misuse comes from
   importing a guarantee whose preconditions you silently violate (e.g. applying a 2p0s
   convergence result to a 4-player partnership game, or a perfect-recall result to an
   abstraction that induces imperfect recall).
3. **Separate the proven from the claimed from the spun.** Isolate (a) the theorem
   and its exact conditions, (b) the empirical claim and on which games, (c) the
   abstract's framing. Gains are often narrower than the title suggests.
4. **Check reproducibility.** Is there official code? Has anyone **independently
   reproduced** it? Look for reproduction reports, GitHub issues ("can't reproduce
   Table 2"), and the ML Reproducibility Challenge. A result no one has reproduced is a
   hypothesis.
5. **Follow the "Cited by" forward.** Later work frequently *qualifies* a method —
   shows it is more exploitable than claimed, fails to scale, or needs an unstated
   trick. The refutations are where the truth is. (Classic example: the
   counter-intuitive finding that *refining* an abstraction can *increase*
   exploitability — a method can look better and play worse.)
6. **Interrogate the comparison.** Fair baselines? **Matched compute?** Same game and
   metric? Or cherry-picked games and a weak opponent? Apply the same lens you apply to
   your own work (`references/experimental-protocol.md` failure modes).
7. **Ablations.** Did they isolate *which* component causes the gain, or could it be a
   confound (more compute, a better tuned baseline-of-one)?
8. **External validity.** "Works in their game" ≠ "works in general." Ask what about
   *their* game made it work, and whether your game shares it.
9. **Errata / retractions.** Quick check for corrections before you build on it.

Output of an appraisal should read like: *"Peer-reviewed at <venue>; proves <claim>
under <assumptions> — which hold/don't hold for us because …; independently reproduced
by <…> / not yet reproduced; later work <ref> qualifies it by …; verdict: sound to
adopt | adopt with caveat | not yet trustworthy."*

## 2.5 Practical-prior-art pass — avoid reinventing the wheel

Before proposing a novel method, do a short, explicit prior-art pass:

1. Search the **exact game / environment name** plus algorithm families and practical
   terms: `CFR`, `MCCFR`, `Deep CFR`, `NFSP`, `OpenSpiel`, `RLCard`, `bot`,
   `baseline`, `thesis`, `student project`, `course project`, `report`, `GitHub`.
2. Identify the **nearest working baseline**: even an imperfect student repo can be
   the right starting point if it encodes the game correctly and reports failures.
3. Extract practical facts: state/action encoding, observation leakage risks,
   baseline bots, evaluation scripts, compute required, and negative results.
4. Separate **usable artifact** from **scientific claim**. A thesis or repo can tell
   you what to try first; it does not establish SOTA unless evaluated rigorously.
5. Only then ask whether a new paper-level idea is needed. If existing baselines have
   not been run fairly, the next scientific act is reproduction, not invention.

Default posture: **read the nearby, concrete literature first**. Do not jump to a
new algorithm because the obvious baseline feels unglamorous.

---

## 3. The ecosystem — build on it, don't rebuild it

Reinventing a CFR solver, a game wrapper, or an exploitability calculator is wasted
effort and a bug surface. Reach for the ecosystem first.

**Game-theory / imperfect-info RL frameworks** (code detail in `references/tooling.md`):
- **OpenSpiel** — default: games + CFR/MCCFR/Deep CFR/NFSP/PSRO/IS-MCTS + exact
  exploitability + Elo/alpha-rank.
- **RLCard** — many card games (Hold'em, Leduc, DouDizhu, Mahjong, UNO) with easy envs
  and rule-based baselines.
- **PokerRL** — research framework behind Deep CFR / SD-CFR poker work.
- **PettingZoo** — general multi-agent API (broad, not equilibrium-focused).

**General RL / training infrastructure** (when you need the RL plumbing, not the solver):
- **Gymnasium** / **PettingZoo** (environment APIs), **Stable-Baselines3**,
  **RLlib (Ray)**, **CleanRL** (single-file, readable reference implementations),
  **Tianshou**. Use these instead of hand-rolling PPO/DQN.

**Experiment management & reproducibility:** **Weights & Biases**, **MLflow**,
**TensorBoard** (tracking); **Hydra** / OmegaConf (config); **DVC** (data/artifact
versioning); fixed-seed discipline (`references/experimental-protocol.md`).

**Compute & artifacts:** Hugging Face Hub (model/checkpoint hosting); cloud/cluster or
Colab/Kaggle for GPUs; object storage for large strategies.

**Optimization/solvers you should not rewrite:** `cvxpy` / LP solvers (PSRO
meta-games), `scipy`, `numpy`; for formal proof, **Lean/Mathlib** or a proof assistant
rather than asserting a bound is correct.

**Communities & news:** `r/MachineLearning`, lab blogs, OpenReview discussions,
field-specific Discord/Slack, and authors' own pages. Useful for "is anyone else
seeing this?" and for current practice — but **community consensus is not evidence**;
route claims back through §2.

**Rule of thumb:** before writing a solver, an evaluator, an env, or a metric, spend
five minutes checking whether OpenSpiel / RLCard / Papers with Code / GitHub / theses
and project reports already have it. The wheel is usually round already; your
contribution should be the part that isn't.
