# Pre-flight checklist — before launching a run

Run through this before spending compute. If you cannot check a box, fix it or
record it as a known limitation. Do not start "to see what happens".

## Game framing
- [ ] Players, zero-sum vs general-sum, and chance are written down.
- [ ] Information structure is explicit; perfect recall confirmed (or its absence noted).
- [ ] Game size (information sets, actions, depth) estimated to an order of magnitude.
- [ ] Any symmetry/abstraction used is confirmed **lossless** for game-theoretic state.

## Hypothesis
- [ ] A single, **falsifiable** hypothesis is stated.
- [ ] A **success criterion** (threshold + metric) is fixed *now*, before results.
- [ ] The relevant question is named: progress-to-equilibrium vs strength-vs-opponent.

## Method & feasibility
- [ ] Algorithm family matches game size + information structure + hardware
      (`references/algorithms.md`).
- [ ] For neural methods: validated on Kuhn/Leduc (or a shrunk game) first.
- [ ] Compute budget estimated (iterations/samples × cost) and it fits; expected
      visits-per-information-set is enough to be non-trivial.

## Baselines & metric
- [ ] Random and greedy/heuristic baselines are in place.
- [ ] The strongest **known** baseline for this game (e.g. IS-MCTS) is included as the bar.
- [ ] Equilibrium metric chosen (exact NashConv if tractable; else LBR lower bound).
- [ ] Strength metric chosen (win rate + Wilson CI; mirrored deals; variance reduction
      if high-variance).
- [ ] Comparisons are planned at **matched compute**.

## Reproducibility
- [ ] Seed(s) chosen and logged; multiple seeds planned for any performance claim.
- [ ] Full config + library/Python/hardware versions will be recorded.
- [ ] Checkpointing + artifact paths defined so any number can be regenerated.

## Integrity
- [ ] Observation tensor audited for **information leakage** (no hidden state).
- [ ] Distribution validity check in place (legal-action support, sums to 1) to catch
      metric artifacts.
- [ ] Plan to evaluate the **average** strategy (for CFR-family), not the current iterate.
