# Rationale

Why Pre-IMPL Forensic exists. What it replaces. Why velocity made it load-bearing.

---

## Why this exists

The first version of my AI-collaborative workflow trusted the handoff. A session would end, write a note about what to do next, and the next session would read the note and do it. That's the obvious shape, and for a while it worked.

It stopped working when the project got fast and parallel.

The breaking instance was mundane. A note said: fix the dropped guard in the export gate, around line 400. The next session read it, started writing the guard back, and twenty minutes in opened the file to find the guard already there — restored the previous evening by a different session. The note had been *correct when written.* It was *wrong when read.* Nothing in the workflow had checked the gap.

Twenty minutes is nothing. But I started watching for the shape, and it was everywhere. A backlog row that described a bug already fixed. An audit memo that classified something a registry note had since corrected. An operator instruction referencing a filename that had been renamed. A "next session: 3 items remaining" bullet where two of the three had shipped in parallel. Each one, individually, was a small waste. Together they were a tax of hours per day — and a few of them weren't small, because the wrong premise survived all the way into built, reviewed, shipped work.

The pattern was always the same: **the framing of a task drifts from the state of the system between the moment the framing is authored and the moment it's acted on.** In a slow serial workflow, that gap is small and the drift is rare. In a fast parallel one, the gap is large and the drift is constant.

That was the moment I built the discipline. Three things had to become explicit:

1. **A check that fires on the premise, not the work.** Code review catches flawed work. Nothing was catching flawed *premises* — work that was fine but solved a problem that had moved or never existed. The check had to fire *before* implementation, against the assumptions the task carried.
2. **A taxonomy for the drift.** "The framing was wrong" is unactionable. *How* it was wrong determines how you verify and how cheap the check is. Class A (state-drift), Class B (misreference), Class C (mis-citation) each have a distinct verification question.
3. **An obligation to reframe, with a receiver.** Catching the drift privately and moving on loses the correction. The reframe memo — original frame, finding, corrected scope, class, *and a confirmed owner* — converts a private observation into a routed correction.

The third piece is the one that keeps the discipline from evaporating. A catch with no receiver gets re-discovered and re-lost on the next pickup. The receiver is what makes the correction stick.

---

## What it replaces

Nothing, exactly — it fills a gap the other disciplines leave open.

- **Code review / [RJ](https://github.com/moranbickel/russian-judge)** assumed the task was correctly framed and checked the output. It cannot catch a wrong premise — the work built on it can be flawless.
- **TDD** assumed the spec was current and checked the build against it. A green suite says you built the thing right, not that the thing was still worth building.
- **The handoff memo itself** was supposed to carry correct state forward. It carries the state *as of when it was written*, which is exactly the thing that drifts.

Pre-IMPL Forensic is the step none of these own: verifying that the premise still holds, at the moment of consumption, before any work rests on it.

---

## Why velocity made it load-bearing

In conventional development, task framings drift too — but slowly. Most tasks are picked up within hours of being filed; status changes infrequently; the gap between filing and pickup is small enough that re-reading at pickup is implicit. You don't need a *discipline* for premise drift because the premise rarely drifts before you act.

AI-assisted development changes the math three ways:

1. **Velocity.** A workstream that took weeks takes days. A "current" status ages to "obsolete" overnight.
2. **Parallelism.** Multiple agents ship work that invalidates each other's inherited assumptions, with no synchronization point that forces a re-read.
3. **Memo-driven coordination.** Sessions bridge through notes and status documents and memory artifacts — each a snapshot of one moment, consumed as if it were current, aging between writing and reading.

The combination produces a *structural* increase in framing-vs-reality drift. Past a certain velocity, the discipline stops being optional hygiene and becomes a precondition for the system to produce reliable output at all. That's the claim that justifies the friction: not "this is a nice practice" but "without this, a fast parallel-agent system degrades to acting on stale premises as its default mode."

---

## What I'd build differently knowing what I know now

**I'd mechanize the cheap checks from day one.** The mtime-against-claim, git-log-against-state-claim, and grep-for-siblings checks are tool-able. Leaving them manual added friction precisely where friction tempts skipping. The expensive judgment checks can't be tooled, but the cheap ones removing their own friction would have raised the fire rate.

**I'd have made the receiver mandatory earlier.** The reframe memos that lacked a confirmed receiver produced corrections that got re-lost. I treated the receiver as a nice-to-have for too long; it's the load-bearing field.

**I'd have written the "documentation ≠ discipline" rule first.** The single most repeated lesson was that having a memo describing the failure mode did nothing to prevent it. The check has to fire at pickup; a reference document read at authoring time and never at pickup time is inert. I learned this by watching the failure recur next to its own documentation, more than once.

**I'd resist over-firing harder.** Early on I leaned toward running the check on everything, which trained me to skim it. The "when it doesn't fire" list (PROTOCOL §1.5) is as important as the "when it fires" list. Calibration beats maximalism.

---

*Version notes: this is the v0.1 rationale. Revisions tracked in [CHANGELOG.md](../CHANGELOG.md).*
