# Scientific validity for imperfect-information RL

This file is the "pure science" gate. Use it before algorithm selection, before
claiming a result, and whenever a user asks whether a line of work is scientifically
sound. The goal is not to sound research-like; it is to make invalid claims impossible
to hide.

---

## 1. Start from the claim, not the code

Write the claim in one sentence, then classify it:

| Claim type | Minimum burden of evidence |
|---|---|
| **Descriptive**: "method A achieved metric X on game G" | Exact run, seed/config, metric definition, uncertainty |
| **Comparative**: "A beats B" | Matched compute, same evaluation protocol, CI/statistical test, strong baselines |
| **Equilibrium**: "A approximates Nash" | Solution concept, exploitability/NashConv or justified proxy, best-response assumptions |
| **Algorithmic**: "modification M improves convergence/sample efficiency" | Ablation isolating M, scaling curve, multiple games/seeds, compute match |
| **Theoretical**: "M converges / is unbiased / is safe" | Formal statement, assumptions, proof or proof sketch with failure cases |
| **Generalization**: "works for this class of games" | Evidence across representative games plus argument that the class features match |

If the claim type is unclear, the work is not ready to run. Clarify the scientific
question first.

---

## 2. Formal game model checklist

Before importing a theorem or metric, specify the actual mathematical object:

- **Players** `N`; two-player zero-sum, constant-sum, cooperative, general-sum, or
  mixed-motive.
- **Histories / states** `H`, terminal histories `Z`, legal actions `A(h)`, player
  function `P(h)`, and chance distribution.
- **Information partitions** `I_i`: what each player observes, not what the simulator
  knows.
- **Perfect recall**: confirm players never forget their own actions/observations.
  Many CFR-style guarantees assume it; lossy abstractions can break it.
- **Utilities** `u_i(z)` and payoff scale. Boundedness matters for regret bounds and
  confidence intervals.
- **Strategy object**: behavioral strategy over information sets, average strategy,
  current iterate, population, or search-time policy.
- **Abstractions**: card/action/state abstraction; lossless symmetry reduction vs
  lossy approximation; translation back to the real game.

Do not say "Nash", "CFR guarantee", or "exploitability" until this model is explicit
enough to check the assumptions.

---

## 3. Choose the right solution concept

Wrong solution concept = scientifically invalid target.

| Setting | Usually appropriate target | Warning |
|---|---|---|
| 2-player zero-sum, perfect recall | Nash equilibrium / minimax; exploitability | CFR guarantees apply to the average strategy under assumptions |
| General-sum 2-player | Nash may exist but can be hard/unstable; consider empirical game analysis | A single "exploitability" number may not answer the question |
| Many-player competitive | Nash is often intractable; use population metrics, alpha-rank, empirical games | Do not import 2p0s convergence claims |
| Cooperative imperfect info | Team policy, convention robustness, ad-hoc coordination | Exploitability is not the central metric |
| Human-facing play strength | Performance vs defined opponent distribution | This is not an equilibrium claim |

When the target is not Nash, name it explicitly and choose metrics that match it.

---

## 4. Theorem discipline

For every theoretical or guarantee-bearing claim, write:

1. **Statement**: what exactly is guaranteed?
2. **Assumptions**: game class, perfect recall, exact vs approximate updates, bounded
   payoffs, sampling distribution, optimizer assumptions, function approximation.
3. **Object of convergence**: average strategy, last iterate, QRE, CCE/CE,
   population meta-strategy, or best response.
4. **Error terms**: regret bound, approximation error, sampling error, abstraction
   error, evaluation error.
5. **Failure case**: one plausible setting where the theorem does not apply.

Never transfer a theorem by analogy. "CFR converges in 2p0s perfect-recall games" does
not imply a neural, sampled, function-approximated, lossy-abstraction variant converges
in a many-player game. At best, it gives a hypothesis to test.

For a new method, the default proof obligations are:

- identify the no-regret learner or optimization dynamic;
- show the estimator is unbiased or bound its bias;
- bound variance or show it is empirically controlled;
- show legal-action support and information-set conditioning are preserved;
- show abstraction or search-time solving does not increase exploitability beyond a
  stated bound, if "safe" is claimed.

If these are absent, call the method empirical, not theoretically grounded.

---

## 5. Validity threats

Use the standard validity split; it prevents hand-wavy science.

### Construct validity

Does the metric measure the claim?

- Win rate vs random measures exploitation of a weak policy, not equilibrium quality.
- Elo measures relative ladder strength, not distance to Nash.
- LBR is a lower bound on exploitability, not a certificate of low exploitability.
- NashConv on a shrunk game measures the shrunk game unless the abstraction is linked
  back to the target game.

### Internal validity

Could a confound explain the result?

- Different compute, different tuning effort, different seeds, better feature
  engineering, information leakage, stronger search budget, lucky matchmaking, or
  cherry-picked checkpoints.
- Ablate one component at a time; otherwise say "the system improved", not "component
  X caused the improvement".

### External validity

Would the conclusion survive outside this exact benchmark?

- Test at least two structurally different games when claiming algorithmic progress.
- Explain why the benchmark captures the target difficulty: hidden information,
  chance variance, depth, action branching, long-term credit assignment, conventions,
  opponent adaptation.
- Do not generalize from Kuhn/Leduc to "poker-scale" or from poker to cooperative
  games without an argument.

### Statistical conclusion validity

Could the result be noise?

- Use enough games/seeds for the claimed effect size.
- Report confidence intervals, not just means.
- Predefine the success threshold; do not move it after seeing results.

---

## 6. Abstraction validity

Most large imperfect-information systems use abstractions. Treat them as scientific
assumptions, not engineering details.

- **Lossless symmetry reduction** is acceptable when it preserves information sets,
  action legality, utilities, and reach probabilities.
- **Lossy abstraction** changes the game. Report results as "in the abstract game"
  unless you evaluate the translated strategy in the full game.
- **Imperfect-recall abstraction** can break guarantees and create non-monotonic
  behavior: a finer abstraction can sometimes become more exploitable.
- **Action abstraction** needs translation rules; translation can be the main source
  of exploitability.
- **Learned state features** are not the information set unless proven sufficient;
  they may leak hidden state or discard needed recall.

Always state where the result lives: full game, abstract game, shrunk game, benchmark
proxy, or opponent distribution.

---

## 7. Contribution ladder

Calibrate the scientific contribution before writing the paper-style claim:

| Level | Acceptable claim |
|---|---|
| Reproduction | "We reproduced X under these conditions." |
| Negative result | "X did not hold under matched compute / this game class." |
| Benchmark | "This environment/evaluator exposes failure mode F." |
| Empirical improvement | "M improves metric Y over baselines under matched compute on games G1..Gk." |
| Mechanistic insight | "Ablations support that component C, not confound D, drives the gain." |
| Algorithmic contribution | "M has empirical gains and a plausible mechanism; theory is absent/partial." |
| Theoretical contribution | "M satisfies theorem T under assumptions A, with proof and limitations." |

A lower level is not worse. It is worse only to claim a higher level than the evidence
supports.

---

## 8. Reviewer-2 pass

Before endorsing a claim, try to reject it:

- Did you check nearby practical prior art — theses, student projects, repos,
  benchmark reports — before presenting this as novel?
- What exact theorem are you relying on, and do all assumptions hold?
- Could a best response exploit the policy despite high win rate?
- Is the result actually about the full game or a proxy game?
- Did the metric answer the scientific question or a convenient substitute?
- Was the baseline tuned as carefully as the proposed method?
- Would the conclusion survive a different seed, opponent mix, abstraction, or game?
- What single counterexample would falsify the claim fastest?

If you cannot answer these, the honest output is a research plan or a limitation, not
a scientific conclusion.

---

## 9. Elite peer-review challenge for every decision

Apply this whenever making a consequential scientific decision: framing the problem,
choosing the solution concept, selecting the algorithm, accepting a baseline set,
choosing a metric, using an abstraction, interpreting a result, or wording the final
claim.

Use this format:

```text
Decision: <the choice being made>
Elite reviewer objection: <the strongest objection a top Nature/Science/NeurIPS/ICML
reviewer would raise>
Evidence required: <what would satisfy that reviewer>
Resolution: <changed decision | evidence exists | limitation logged | decision rejected>
```

Standards for the objection:

- Assume the reviewer is technically deep in RL/game theory and is hostile to
  overclaiming.
- Attack novelty, assumptions, construct validity, baseline strength, compute match,
  statistical support, reproducibility, abstraction validity, and external validity.
- Prefer the objection that would kill the claim fastest, not the easiest one to
  answer.
- If the only defense is "this seems reasonable", the decision is not yet defensible.
- If the evidence cannot be produced in the current setting, keep the decision but
  label it explicitly as a limitation or hypothesis, never as established science.
