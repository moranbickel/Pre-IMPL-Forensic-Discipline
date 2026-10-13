# Evidence

What this document is: a plain statement of what supports this discipline and what does not. It is published as a v0.1 draft, and the reader deserves to know what is missing.

## What the evidence is

The checklist came out of one author's experience on one private project, working with several AI coding sessions against a shared repository. That experience is anecdotal: selected, narrated, and reported by the person who designed the discipline.

- **Effectiveness is unmeasured.** No controlled comparison against working without the check has been run.
- **No public data supports a catch rate.** This repo does not publish one, and readers should not infer one from the examples, which are synthetic.
- **No second-project pilot exists yet.** Whether the checklist helps in other codebases, with other agent stacks or team shapes, is unknown.

## The cost-benefit math

The break-even catch rate is the cost per check divided by the cost avoided per catch:

```text
break-even catch rate = cost per check / cost avoided per catch
```

If a check costs 5-30 minutes and a catch avoids 0.5-5 hours of wrong work, that ratio spans roughly 1.7% (a 5-minute check against 5 hours avoided) to 100% (a 30-minute check against 30 minutes avoided, where a catch merely pays for itself). The discipline pays off only where checks are cheap relative to the wrong work they prevent. Estimate your own numbers; the ones above are illustrative bounds, not measurements.

## Patterns observed

These are impressions from the author's own use, not metrics.

**Pattern 1 - State-drift (Class A) seemed the most frequent kind of catch.** The typical case is "the framing was true when written; a parallel agent or a prior commit moved the world since." This is the cheapest class to check (`git log` plus a current read). The single most valuable habit is reading the named artifact at pickup, every time.

**Pattern 2 - Class C catches seemed the costliest when missed.** A framing that cites a source which doesn't say what's attributed to it looks authoritative and can survive a review that isn't checking the source.

**Pattern 3 - Documentation does not enforce.** The failure mode recurred at pickup even while a memo describing exactly that failure mode sat in the repo. The check is the discipline; the doc is a reference. This most shaped the framing in [PROTOCOL.md §9.1](./PROTOCOL.md).

**Pattern 4 - Authority asymmetry suppresses the check.** When a senior person's framing was treated as more authoritative than an agent's forensic finding, the check got overridden and the drift survived. The reframe that helps - "the framing was authored at T1; the forensic verifies T2" - had to be made explicit before the check was applied reliably to premises written by senior people.

**Pattern 5 - The subclasses are retrieval aids, not theory.** In the author's experience, each named subclass was named because it recurred. The catalog's value is recognition: the next pickup sees the shape and reaches for the mitigation that worked last time.

## What this does NOT show

- **That the discipline generalizes to other projects.** The shape appears general - premise drift is a consequence of velocity and parallelism - but "appears general" is an observation, not a validation.
- **That A/B/C is the best taxonomy.** It is the smallest set of drift classes the author found that keeps a distinct verification question per class. That is not a proof of optimality.
- **That it pays off everywhere.** A slower or more serial project would see fewer catches, possibly below break-even, at which point the discipline is correctly skipped.
- **Any false-positive or false-negative rate.** None has been measured.

## Conditions for v1.0

This repo is published at v0.1 deliberately. Promotion to v1.0 requires public, checkable evidence:

1. **Cross-project pilot with published results.** At least one second-project pilot, ideally in a different domain and agent stack, with its results (including misses) published.
2. **Documented false-positive examples.** Public cases where the check fired and cost more than it saved, so that the "when not to fire" guidance rests on examples rather than assertion.
3. **A second author's account.** At least one adoption report from someone other than the original author.

The fastest path to all three is the one input this repo is asking for: see [CONTRIBUTING.md](./CONTRIBUTING.md).

## Honest limitation

A more rigorous evidence pack - controlled comparison against working without the check, false-positive and false-negative rates on a held-out set, multi-author and multi-project corpora - is future work, not present claim. Until then, this is a checklist one author found useful on one project, offered to others on the hypothesis that the failure mode it targets is structural to high-velocity parallel-agent development rather than specific to one project.
