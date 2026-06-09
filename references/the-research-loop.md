# The research loop, live grounding, and the limits of this skill

This file operationalizes the two things that separate a **researcher** from a
knowledgeable assistant: **closing the empirical loop** and **knowing the boundary of
your own competence**. Read it whenever the task is to actually *do* research (run and
iterate), not merely answer a question about the field.

---

## 1. Why a static skill is not (yet) a researcher — functional decomposition

A researcher's job decomposes into functions. Be honest about which this skill covers.

| Function | Status with this skill |
|---|---|
| Know the state of the art (algorithms, metrics) | **Covered**, but memory is frozen at a cutoff → must be refreshed against live sources (§3) |
| Find information where it lives; appraise a method's soundness from the literature; know the tooling ecosystem | **Closable here** — see `references/literature-and-ecosystem.md` |
| Methodology & rigor (baselines, CIs, anti-artifact) | **Covered** — the skill's core strength |
| Close the empirical loop (hypothesis → run → measure → update) | **Closable here** — but only if you actually execute (§2) |
| Source / verify (citations, compute figures) | **Closable here** — by looking it up, not recalling (§3) |
| Long-horizon memory / a research program | **Closable here** — via a persisted lab notebook (§4) |
| Calibrated self-knowledge (know what you don't know) | **Closable here** — make epistemic status explicit (§3, §6) |
| Invent *novel* theory / algorithms | **Fundamental limit** — you synthesize the frontier; ideation→falsification (§5) is the honest approximation |
| Research *taste* over months | **Fundamental limit** — scaffold it, don't claim it |
| Operational grind (provisioning, multi-day runs, infra) | **Fundamental limit** — plan it, you can't be it |
| Accountability to a peer community | **Fundamental limit** — reproduction and defense need real peers |

**Conclusion:** full replacement of a human researcher = **no**. The defining act —
*closing the empirical loop while knowing your limits* — is achievable only if you
deliberately do §2–§6. Knowledge alone is not research.

---

## 2. Close the empirical loop (the defining act)

The loop is **hypothesis → run → observe raw output → update → repeat.**

- **Run, don't narrate.** When tools/compute are available, *execute*. Capture the
  exact command, raw stdout/stderr, return code, and artifact paths. "I would run X
  and expect Y" is not a result and must never be written as if it were.
- **Iterate on what you saw, not what you expected.** Flat learning curve? Diagnose
  (under-training? leakage? metric artifact? bug?) *before* re-running. The
  diagnosis is the research.
- **Smallest instance first.** Reproduce on Kuhn/Leduc (or a shrunk game), confirm
  NashConv drops, validate the harness — only then scale. A method that fails small
  will not succeed big.
- **When you genuinely cannot run it** (no tools, no compute, no data): say so
  plainly, and deliver (a) the runnable artifact — script, config, seed — and (b) the
  explicit prediction + success criterion you would test. Hand the user something
  they can execute, not a description of execution.

---

## 3. Ground in the LIVE literature (don't let knowledge rot, don't hallucinate)

Trained memory is frozen at a cutoff and will confidently invent plausible-looking
citations and numbers. Defend against both staleness and fabrication:

- For any claim that hinges on a **specific** paper, result, "first to…", SOTA
  number, or API signature: **search and verify** (arXiv / the paper / the library
  source) before asserting it.
- **Cite verifiably:** title + authors + venue/year, ideally a link or DOI. If you
  cannot locate the source, write "unverified — could not confirm," and do **not**
  fabricate a reference to fill the gap.
- **Compute figures and benchmark numbers are the highest-risk claims.** Treat every
  one as unverified until sourced — *including the order-of-magnitude figures in this
  skill's own `landmark-systems.md`*, which are calibration aids, not quotable facts.
- Distinguish, in writing, **recalled** (from training) vs **verified** (you just
  checked) vs **observed** (you just ran it).

---

## 4. Keep a lab notebook / research program

A researcher accrues; a chatbot restarts. Persist across sessions (to a `results/`
log, a notebook file, or the harness's memory):

- the **running hypothesis** and the **current best result** (with seed, config, compute);
- **dead ends and *why* they failed**, so they are not silently re-tried;
- **open questions** and the **next experiment** queued.

A multi-experiment thread with a through-line is the difference between *research* and
a sequence of disconnected one-offs. Update the notebook every time you observe a real
result.

---

## 5. Ideation → falsification (the only honest route toward novelty)

You will not invent a correct new theorem on demand, and claiming to is the worst
failure mode. But you can do the next best thing — and it is genuinely useful:

1. **Generate** candidate ideas / conjectures explicitly (a new variant, a new
   estimator, a new abstraction, a new hypothesis about why a method fails).
2. For each, **design the cheapest test that could kill it**: the smallest game with
   exact NashConv, a targeted counterexample search, an ablation.
3. **Run the test.** Report survivors as *conjectures-not-yet-refuted*, never as
   results or theorems. An idea is worth exactly the falsification it survived.

This converts speculation into something with scientific standing, and keeps you from
dressing a guess as a discovery.

---

## 6. Self-limits — what to say out loud

When a task exceeds the medium, **name the limit** instead of bluffing past it:

- "This needs *novel theory* I can scaffold and conjecture, but cannot guarantee or
  prove sound on my own — it needs derivation and peer scrutiny."
- "This is an *operations* problem (multi-day runs, cluster, provisioning); I can
  design and script it, but not execute or babysit it here."
- "This claim needs *reproduction by a real peer*, not my assertion."
- "I am *recalling* this, not verifying it — confidence X; let me look it up before we
  rely on it."

Naming the boundary is not a weakness of the skill; it is the single most
researcher-like behavior in it. The amateur move is to step over the line silently.
