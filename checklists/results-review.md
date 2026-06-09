# Adversarial self-review — before claiming a result

Attack your own result as a skeptical reviewer would. Every "no" is a finding to fix
or disclose **before** you write "it works".

## Is the number real?
- [ ] Can you point to the **exact command** and **raw output / artifact path** that
      produced it? (If not, you have not measured it — do not claim it.)
- [ ] Is it reproducible from the logged seed + config?
- [ ] Did you report the metric you think you reported? (NashConv vs exploitability;
      average vs current strategy.)

## Is the scientific claim valid?
- [ ] Claim type is stated, and the evidence meets that claim type's burden
      (`references/scientific-validity.md`).
- [ ] The solution concept is appropriate for the game class.
- [ ] The theorem/guarantee, if any, applies under the actual assumptions used.
- [ ] Construct validity holds: the metric measures the thing claimed.
- [ ] External validity is not overstated beyond the tested games/proxy.
- [ ] Abstractions/reductions are disclosed, and the claim says where the result
      lives (full game, abstract game, shrunk game, or opponent distribution).
- [ ] The claim survived an elite peer-review challenge, or the unresolved objections
      are listed as limitations.

## Is it an artifact?
- [ ] NashConv ≈ 0 — did you rule out the untrained/degenerate-policy artifact
      (distribution validity, comparison to uniform baseline, cross-check with strength)?
- [ ] Any chance of **information leakage** inflating strength?
- [ ] Is "weak result" actually just **under-training** (check visits per information
      set), not a method failure?

## Is the comparison fair?
- [ ] Same compute budget across compared methods?
- [ ] Strongest **known** baseline included (not just random/greedy)?
- [ ] Positions/seats mirrored to remove luck/positional bias?

## Is it statistically supported?
- [ ] Win rates carry **Wilson CIs** and the n is stated.
- [ ] Variance reduction (AIVAT/mirrored deals) used where the game is high-variance.
- [ ] Multiple seeds for the performance claim (not a single lucky run)?
- [ ] "A > B" is actually supported at this n (intervals/test), not eyeballed?

## Is the claim ⊆ the evidence?
- [ ] No "solved / converged / superhuman / beats X" beyond what metric + CI + matched
      compute support.
- [ ] Observed fact, inference, and remaining uncertainty are clearly separated.
- [ ] Anything not actually run is labeled "not run".
- [ ] Limitations of each metric are stated (e.g. "LBR is a lower bound only").

If every box is checked, write it up with `templates/experiment-report.md`. If not,
the honest headline may be a **negative or partial** result — which is still a real
contribution when reported cleanly.
