# Evidence

What this document is: a short account of how the discipline has actually been applied, with no individual receipts attached. The goal is to characterize the corpus it was built against — not to prove correctness, just to ground the claim that it is born from real work, not a thought experiment. It also states plainly the preconditions that gate a v1.0, because this is published as a v0.1 draft and the reader deserves to know what is missing.

## The corpus

- **Setting.** A production AI system generating Israeli civil-litigation documents, developed by multiple AI coding sessions running in parallel against a shared repository, coordinated through status documents and handoff memos.
- **Measured window.** A sustained ~36-hour working window of parallel AI-assisted development, during which the discipline's fires and catches were tracked.
- **Volume.** 24+ instances where the check fired and produced a reframe (Class A, B, or C).
- **Subclasses.** Eight distinct drift subclasses surfaced and were named (the catalog in [PROTOCOL.md §4](./PROTOCOL.md)).
- **Fire rate.** Roughly one fire per 1.5 hours of active session work.
- **Catch rate.** 20–30% of forensically-checked tasks produced a reframe — i.e., the framing was contradicted on one in three to one in five tasks checked.
- **Conservative cost-saved estimate.** 25–40 hours of dead-end implementation avoided across the window.

## The cost-benefit math

Each forensic check costs 5–30 minutes. Each catch prevents 0.5–5 hours of wrong work. The discipline breaks even when 5% or more of checked tasks would have been wrong-framed. The observed catch rate (20–30%) sits well above break-even. The math is not delicate — it would survive a large error in either the cost or the catch-rate estimate and still favor running the check.

## Patterns observed

These are not metrics. They're patterns the discipline's application surfaced, in roughly the order they became apparent.

**Pattern 1 — State-drift (Class A) dominates the corpus.** The most common catch by far is "the framing was true when written; a parallel agent or a prior commit moved the world since." This is the cheapest class to check (`git log` + a current read) and the highest-frequency. The single most valuable habit is reading the named artifact at pickup, every time.

**Pattern 2 — The expensive catches are Class C.** Doctrinal-mis-citation — a content-bearing framing that cites a source which doesn't say what's attributed to it — is rare relative to Class A but far more damaging when missed, because the citation looks authoritative and survives review that isn't checking the source. In a domain where cited content is load-bearing for the output, this is where the worst defects hide.

**Pattern 3 — Documentation does not enforce.** Several instances showed the failure mode recurring at pickup *while a memo describing exactly that failure mode sat in the repo.* The documentation was never read at pickup. The check is the discipline; the doc is a reference. This is the observation that most shaped the framing in [PROTOCOL.md §9.1](./PROTOCOL.md).

**Pattern 4 — Authority asymmetry suppresses the check.** When a senior operator's framing was treated as more authoritative than an agent's forensic finding, the check got overridden inappropriately and the drift survived. The reframe that resolves this — "the framing was authored at T1; the forensic verifies T2" — had to be made explicit before the check fired reliably against operator-authored premises.

**Pattern 5 — The subclasses are retrieval aids, not theory.** Each named subclass earned its name by recurring. The catalog's value is that the next pickup recognizes the shape ("this is operator-substrate-confabulation again") and reaches for the mitigation that worked last time, rather than re-deriving it.

## What this corpus does NOT prove

The corpus is one author's application of the discipline on one project. It does not prove:

- **That the discipline generalizes cleanly to other projects.** The shape *appears* general — premise drift is a consequence of velocity and parallelism, not of any domain specific. But "appears general" is an observation, not a validation. There is no second-project pilot yet.
- **That A/B/C is the optimal taxonomy.** It's the smallest set of drift classes I found that preserves a distinct verification question per class without inviting hedge. That's an observation, not a proof of optimality.
- **That the catch rate generalizes.** 20–30% reflects this project's velocity, parallelism, and memo-discipline. A slower or more serial project would see a lower fire rate and lower catch rate — possibly below break-even, at which point the discipline is correctly skipped.
- **That the cost-saved estimate is precise.** "Dead-end work avoided" is counterfactual by nature — I'm estimating the hours a wrong premise *would* have cost. The estimate is conservative and the direction is not in doubt; the magnitude is soft.

## Publication preconditions (the v1.0 gate)

This repo is published at v0.1 deliberately. The discipline is real and battle-tested, but on a single project. Three conditions gate promotion to a v1.0 methodology:

1. **Cross-project validation.** At least one second-project pilot, ideally in a non-legal domain and a different agent stack, confirming the check fires and catches.
2. **Subclass library at 30 instances.** Currently ~24. The catalog earns entries empirically; 30 is the threshold at which the eight subclasses are each backed by enough instances to be more than anecdote.
3. **Originating project past active crisis.** Publishing a "how to work reliably" methodology while the originating project is itself in active deliverable crisis would be incongruent. The methodology should be drafted from a position of demonstrated stability.

The fastest path to (1) is the one input this repo is asking for: see [CONTRIBUTING.md](./CONTRIBUTING.md).

## Honest limitation

This evidence is anecdotal — selected, narrated, and reported by the same person who designed the discipline and ran the project. The reader should weight it accordingly. The corpus exists; the patterns are real; the framing is mine.

A more rigorous evidence pack — controlled comparison against working without the check, false-positive and false-negative rates on a held-out set, multi-author and multi-project corpora — is future work, not present claim. Until then, this is a discipline that demonstrably paid for itself on one project, offered to others on the hypothesis that the failure mode it catches is structural to high-velocity parallel-agent development rather than specific to mine.
