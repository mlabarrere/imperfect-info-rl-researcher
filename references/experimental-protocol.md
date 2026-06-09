# Experimental protocol

How a world-class researcher runs a study so the conclusions survive scrutiny. The
overriding rule: **state the claim before the run, and make the claim falsifiable.**

---

## 0. Frame the game (before anything else)

Write down, explicitly:
- **Players** and whether it is **zero-sum** or general-sum. (This decides whether
  "Nash" and exploitability are even the right targets.)
- **Information structure:** what each player observes, and whether the game has
  **perfect recall** (most equilibrium guarantees assume it).
- **Chance** events and their distribution.
- **Size:** number of information sets, action-space size, tree depth/branching.
  Order-of-magnitude is enough — it determines which methods are feasible.
- **Symmetry** that can be exploited to shrink the representation (e.g. suit
  isomorphism in card games) — but verify the reduction is **lossless** for
  game-theoretic state (it may be lossy only for learning features).

This framing dictates the algorithm family (`algorithms.md`) and the achievable
metric (`metrics-and-evaluation.md`).

## 0.5 Scientific validity gate

Before choosing an algorithm, read `references/scientific-validity.md` and record:

- the **claim type** (descriptive, comparative, equilibrium, algorithmic,
  theoretical, or generalization);
- the **solution concept** that matches the game, not the one that is easiest to
  measure;
- the **theorem assumptions** you are relying on, and whether they actually hold;
- the main **construct, internal, external, and statistical validity** threats;
- whether any abstraction/reduction changes the game being studied.

If this gate fails, fix the scientific question before running code. An invalid
scientific question does not become valid after a clean training run.

---

## 1. Hypothesis-driven workflow

For every experiment, before running:

1. **Hypothesis** — a specific, falsifiable statement.
   *Bad:* "CFR+ should work." *Good:* "On Leduc, CFR+ drives exploitability below
   0.01 within 10⁴ iterations."
2. **Intended modification / setup** — exactly what you will run.
3. **Success criterion** — the threshold that would confirm/refute, decided now, not
   after seeing results.
4. **Risk assessment** — what could make the result misleading (leakage,
   under-training, variance, artifact metric).

After running, report in this order: **exact command → raw output / artifact path →
observed facts → inferences → remaining uncertainty.** Keep observed fact strictly
separate from interpretation.

---

## 2. Baselines (mandatory)

No result means anything without baselines. At minimum:
- **Random** legal-move agent — the floor.
- **Greedy / heuristic** agent — encodes obvious domain strategy.
- **Domain SOTA** — e.g. IS-MCTS for many card games; the published best for the
  specific game. **This is the bar to beat**, and beating only random/greedy is not a
  contribution if a stronger known baseline exists.

Evaluate at **matched compute** wherever methods are compared.

---

## 3. Ablations

Change one thing at a time. Typical ablations: with/without a component (e.g.
variance-reduction baseline, suit-symmetry encoding), hyperparameter sweeps
(learning rate, sampling scheme, DCFR discounts), and compute scaling
(performance-vs-iterations curve). The performance-vs-compute curve is often the most
honest single artifact you can produce, because it shows whether you are
method-limited or compute-limited.

---

## 4. Reproducibility

- **Seed everything** and log the seed. One seed for a deterministic test; **multiple
  seeds** (report mean ± spread, or per-seed) for any performance claim.
- Log the **full config** (every hyperparameter) and the **environment**: library
  versions (OpenSpiel, framework), Python version, hardware (CPU/GPU), wall-clock.
- Persist **checkpoints** with metadata so any number can be regenerated.
- Use **real game objects**, not mocks, in correctness tests.

---

## 5. Common failure modes (the things that quietly invalidate results)

| Failure mode | Symptom | Defense |
|---|---|---|
| **Information leakage** | Agent/evaluator conditions on hidden state; suspiciously strong play | Audit the observation tensor; perfect-recall + info-set checks; oracle-parity tests |
| **Metric artifact** | NashConv ≈ 0 after trivial training | Validate distributions; compare to uniform baseline; cross-check strength (see metrics doc) |
| **Under-training mistaken for method failure** | Weak win rate after few iters/visit-per-infoset | Report visits/information-set; extrapolate compute; don't conclude method invalid |
| **Strategy fusion** | Determinization-based search looks fine, gets exploited | Use proper imperfect-info solving, not "assume hidden state then solve" |
| **Average vs current iterate** | CFR "diverges" when you evaluate the wrong strategy | Evaluate the average strategy |
| **No CI / too small n** | "A beats B" from a handful of noisy games | Wilson CI, mirrored deals, variance reduction, adequate n |
| **Compute mismatch** | A "beats" B but used 10× the compute | Match compute; report it |
| **p-hacking / cherry-picking** | Best seed reported as the result | Pre-register criterion; report all seeds |
| **Overclaiming** | "Superhuman" / "solved" from in-domain self-play | Tie claims to exploitability and external baselines |

---

## 6. Honest reporting

- Lead with the **observed fact**, then your **inference**, then **remaining
  uncertainty** — clearly labeled.
- A clean negative result ("the method trains but is compute-limited; here is the
  scaling curve") is a real contribution. Report it as such.
- Never write "solved", "converged", "superhuman", or "beats X" without the evidence
  (metric + CI + matched compute + command/artifact) on the page.
- When you did not run something, say "not run" — never imply otherwise.

Use `templates/experiment-report.md` for the write-up and `checklists/results-review.md`
to attack your own result before publishing it.
