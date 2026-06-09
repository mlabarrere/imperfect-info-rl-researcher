# Experiment report — <title>

> Fill every section. Keep **observed fact** strictly separate from **inference**.
> A number without a command, seed, and CI is not a result.

## 1. Context & hypothesis
- **Game:** <name; players; zero-sum?; info structure; size (info sets / actions / depth)>
- **Question:** <progress-to-equilibrium | strength-vs-opponent>
- **Hypothesis (falsifiable):** <e.g. "CFR+ drives exploitability < 0.01 on Leduc within 1e4 iters">
- **Success criterion (pre-registered):** <threshold + metric, decided before the run>

## 2. Setup
- **Method:** <algorithm + key hyperparameters>
- **Baselines:** <random / greedy / domain SOTA (e.g. IS-MCTS)>
- **Metric(s):** <exact NashConv | LBR (lower bound) | win-rate+Wilson CI | Elo | alpha-rank>
- **Compute:** <iterations/samples; wall-clock; hardware (CPU/GPU)>
- **Seed(s):** <values>
- **Environment:** <OpenSpiel version; framework version; Python; OS>

## 3. Exact commands
```
<paste the exact command(s) run>
```

## 4. Raw results (observed fact)
- **Artifact path(s):** <paths to logs / checkpoints / CSVs>
- **Raw output (excerpt):**
```
<paste raw numbers / log lines, including return code if available>
```
- **Headline numbers:** <metric = value, CI[lo, hi], n; vs baseline X = …>

| Method | Compute (matched) | Metric | Value | CI / n |
|---|---|---|---|---|
| <method> | <iters/samples> | <NashConv/winrate/…> | <value> | <CI, n> |
| <baseline> | <matched> | <same> | <value> | <CI, n> |

## 5. Interpretation (clearly labeled)
- **Inference:** <what the facts imply — at matched compute, with CIs>
- **Hypothesis status:** <confirmed | refuted | inconclusive at this n/compute>
- **Anti-artifact checks done:** <distribution validity; uniform-baseline comparison;
  leakage audit; visits/info-set; average-vs-current>

## 6. Remaining uncertainty & limitations
- <metric limitations (e.g. LBR lower bound); compute ceiling; single-game scope;
  seeds run; what was NOT run>

## 7. Verdict
- <one honest sentence: what is now established, and what is not>
