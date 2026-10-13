# Rationale

Why Pre-IMPL Forensic exists, what it replaces, and when it is worth the friction.

*This is a short motivation, not evidence. It makes no measured claims; see [EVIDENCE.md](../EVIDENCE.md).*

---

## Why this exists

A common way to hand off work is to write a note ("next: fix X") and have the next session read the note and do it. That works while the system changes slowly. It breaks when the system changes quickly or in parallel, because the note records the state *as of when it was written*.

A note can be correct when written and wrong when read. Nothing in a typical workflow checks the gap. A backlog row may describe a bug that has since been fixed. A memo may classify something that a later note corrected. An instruction may reference a file that has been renamed. Each is a small waste, and occasionally the wrong premise survives into built, reviewed work.

The pattern is the same each time: **the framing of a task drifts from the state of the system between the moment the framing is written and the moment it is acted on.** In a slow, serial workflow the gap is small. In a fast, parallel one it can be large.

The discipline is built from three things:

1. **A check that fires on the premise, not the work.** Code review checks the output. Nothing in the usual toolkit checks whether the work solves a problem that has moved or never existed. The check has to fire *before* implementation, against the assumptions the task carries.
2. **A taxonomy for the drift.** "The framing was wrong" is not actionable. *How* it was wrong determines how you verify it and how cheap the check is. Class A (state-drift), Class B (misreference) and Class C (mis-citation) each have a distinct verification question.
3. **An obligation to reframe, with a receiver.** Catching the drift privately and moving on loses the correction. The reframe memo (original frame, finding, corrected scope, class, *and a confirmed owner*) turns a private observation into a routed correction. A catch with no receiver gets re-discovered and re-lost.

---

## What it replaces

Nothing, exactly - it fills a gap the other disciplines leave open.

- **Code review / [RJ](https://github.com/moranbickel/russian-judge)** assumes the task was correctly framed and checks the output. It cannot catch a wrong premise; the work built on it can be flawless.
- **TDD** assumes the spec is current and checks the build against it. A green suite says you built the thing right, not that it was still worth building.
- **The handoff memo itself** is supposed to carry correct state forward. It carries the state as of when it was written, which is exactly the thing that drifts.

Pre-IMPL Forensic is the step none of these own: verifying that the premise still holds, at the moment of consumption, before any work rests on it.

---

## When velocity matters

In conventional development, task framings drift too, but slowly. Most tasks are picked up soon after they are filed, so re-reading at pickup is implicit.

AI-assisted development can change that in three ways:

1. **Velocity.** Work that took weeks may take days, so a "current" status can go obsolete quickly.
2. **Parallelism.** Multiple agents can ship work that invalidates each other's inherited assumptions, with no synchronization point that forces a re-read.
3. **Memo-driven coordination.** Sessions bridge through notes, status documents and memory artifacts, each a snapshot of one moment that is consumed as if it were current.

The hypothesis is that this combination raises the rate of framing-versus-reality drift enough that an explicit pickup check becomes worth its cost. That is a hypothesis, not a demonstrated result; a slow, serial project may not need the discipline at all.

---

## Practical notes

- **Mechanize the cheap checks.** File-mtime against a claim, `git log` against a state claim, and grep for siblings can be scripted. Leaving them manual adds friction exactly where friction tempts skipping.
- **Make the receiver mandatory.** A reframe without a confirmed owner tends to get lost again.
- **Documentation is not enforcement.** A memo describing the failure mode does nothing unless the check actually fires at pickup.
- **Resist over-firing.** Running the check on everything trains people to skim it. The "when it doesn't fire" list (PROTOCOL §1.5) is as important as the "when it fires" list.
