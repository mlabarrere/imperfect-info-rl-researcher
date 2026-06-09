# Imperfect-Information RL Researcher — a Claude skill

A [Claude Code](https://claude.com/claude-code) **skill** that turns Claude into a
world-class researcher in **reinforcement learning and computational game theory for
imperfect-information games** — poker, Scopa/Chkobba, Bridge, Hanabi, Mahjong,
Stratego, Liar's Dice, and similar partially-observed, multi-agent, and self-play
settings.

It is not an algorithm dump. Its value is **scientific discipline**: intellectual
honesty (no fabricated results), game-theoretic soundness (respect information sets,
perfect recall, no information leakage), exploitability-first evaluation, compute
awareness, reproducibility, and statistical rigor — the things that separate a
researcher from an optimistic code generator.

## What it does

When engaged, Claude works the full research cycle: frame the game → ground in the
literature → select an algorithm family → design a falsifiable experiment →
implement (OpenSpiel-first) → evaluate with the right metric → adversarially
self-review → report honestly.

It covers, among others:
- **Algorithms:** CFR / CFR+ / DCFR, MCCFR, Deep CFR / SD-CFR / ESCHER, NFSP, PSRO &
  variants, IS-MCTS, continual/subgame solving, ReBeL & RL+search, MMD/NeuRD.
- **Metrics:** exploitability / NashConv, LBR, win-rate with Wilson CIs, Elo/Glicko,
  alpha-rank, AIVAT — and how to avoid metric artifacts.
- **Practice:** OpenSpiel recipes (with pointers to RLCard / PokerRL), experimental
  protocol, pre-flight and self-review checklists, and a report template.

## Structure

```
imperfect-info-rl-researcher/
├── SKILL.md                       # persona, principles, workflow, and router (loaded first)
├── references/                    # loaded on demand
│   ├── algorithms.md              # taxonomy + when to use what + failure modes
│   ├── metrics-and-evaluation.md  # exploitability, NashConv, LBR, CIs, Elo, alpha-rank, AIVAT
│   ├── experimental-protocol.md   # hypothesis-driven workflow + common failure modes
│   ├── landmark-systems.md        # Cepheus/DeepStack/Libratus/Pluribus/ReBeL/… + compute calibration
│   ├── tooling.md                 # OpenSpiel recipes + ecosystem pointers
│   ├── literature-and-ecosystem.md # where to find papers; appraising soundness; tool/library/site map
│   └── the-research-loop.md       # closing the empirical loop; live grounding; lab notebook; the skill's limits
├── checklists/
│   ├── experiment-design.md       # before launching a run
│   └── results-review.md          # before claiming a result
└── templates/
    └── experiment-report.md       # structured, evidence-first write-up
```

It uses **progressive disclosure**: `SKILL.md` stays lean and routes to the deeper
references only when a task needs them.

## Install

Copy the folder into your Claude skills directory:

```bash
# user-level (available in every project)
cp -r imperfect-info-rl-researcher ~/.claude/skills/

# or project-level
cp -r imperfect-info-rl-researcher /path/to/project/.claude/skills/
```

Claude auto-discovers skills in `.claude/skills/`. Confirm it loads by asking
something in scope (see below).

## Usage — try these

- "Which algorithm should I use to approximate a Nash equilibrium in a 2-player
  zero-sum card game with ~10¹² information sets, CPU-only?"
- "Design an experiment to measure the exploitability of a CFR+ agent on Leduc poker."
- "My neural policy reports NashConv ≈ 0 after 100 iterations — did I solve the game?"
- "My agent beats the random bot 78% of the time. Is that a result?"

You should get calibrated, evidence-demanding answers — and pushback when a claim
outruns its evidence.

## Scope & honesty

This skill encodes methodology and well-known results; it does **not** run your
experiments for you, and it will refuse to assert results it has not observed. Code
recipes in `tooling.md` are illustrative idioms — verify APIs against your installed
OpenSpiel version before relying on them.

## Contributing

Issues and PRs welcome: new algorithm entries, sharper failure modes, additional
landmark systems with sourced compute figures, or game-specific recipes. Keep the
discipline intact — claims need citations or evidence.

## License

MIT — see [LICENSE](LICENSE).
