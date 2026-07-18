# How to Adopt Pre-IMPL Forensic

Concrete steps to start applying the discipline today. Estimated time to functional use: **one task**. Estimated time to fluent use: **a week of real pickups**.

There's nothing to install. The discipline is a habit plus two templates. The work is building the reflex of running the check before you act on an inherited premise.

---

## The 10-minute version

The next time you pick up a task that arrived as an inherited framing - a backlog row, a handoff note, a plan reference, a "next session" bullet - do this before writing any code:

1. **Open the named artifact.** If the task names a file and line, go there and read it. Does the code match the description? Does the file even exist at that path?
2. **Ask the one question:** *is the premise still true today?* If the framing claims a bug, a state, a behavior - verify it against the current system, not against the note.
3. **If it still holds,** proceed. The check cost you a few minutes and bought you confidence.
4. **If it doesn't hold,** stop. Write a reframe memo (template: [`templates/reframe-memo-template.md`](../templates/reframe-memo-template.md)): original frame, what you found, the corrected scope, the class (A/B/C), and who owns the corrected scope. *Then* decide what to actually build - it's usually different from what the task said.

That's the loop. Everything else is calibration.

---

## Day one - run it on inherited pickups

Apply the full [§B checklist](../templates/forensic-check-checklist.md) on every task that arrives with inherited framing and is more than trivial. You're calibrating two things: your sense of which claims are load-bearing, and your speed at the checks. Expect the first few to feel slow and a little paranoid. That's the cost floor; it drops fast.

Track one number: how often the check contradicts the framing. If it's near zero, your project may be slow/serial enough that the discipline isn't earning its cost yet (that's fine - see "When it doesn't fit"). If it's one in three to one in five, you've found the same rate the originating project saw, and the discipline is paying for itself.

---

## Week one - calibrate the gate

### Build the "doesn't fire" reflex.

The discipline is selective, not universal. By a few days in you should be *skipping* it confidently on: typo/cleanup work, premises you authored or verified this same session, and code-invariant tasks where every site is in scope by definition (a rename across callers doesn't need a forensic - Find Usages is the check). Firing on those dilutes the signal. (Full skip list: [PROTOCOL.md §1.5](../PROTOCOL.md).)

### Learn the three classes by sorting your catches.

For each contradiction you catch, name its class. **A** (was true, world moved - cheap, `git log`). **B** (named the wrong artifact - read it to confirm). **C** (cited a source that doesn't support the claim - look the source up). Sorting forces the right verification: a Class C catch you treated as Class A is a citation you trusted without reading.

### Always name a receiver.

The discipline's failure mode is the orphan reframe - a correction you found but nobody owns. Before you close a reframe, name the workstream, row, or locus that will carry the corrected scope. If none exists, file a stub as the receiver. (Rule: [PROTOCOL.md §5.3](../PROTOCOL.md).)

---

## Week two and beyond - fluency

### Mechanize the cheap checks.

The mtime-against-claim, git-log-against-state-claim, and grep-for-siblings checks are scriptable. Tool them so the cheap checks cost seconds, not minutes - that's where friction tempts skipping. The judgment checks (is this load-bearing? does the source support the claim?) stay manual.

### Recognize subclasses on sight.

After enough pickups you'll start seeing the named shapes ([PROTOCOL.md §4](../PROTOCOL.md)) without deriving them: *"this is operator-substrate-confabulation - search by role, not by the filename they gave me," "this is an aggregate someone else computed - decompose it before I act."* Recognition is the speed-up; the catalog is the lookup table.

### Apply it to operator-authored premises too.

The hardest catches are the ones that contradict a senior framer. The discipline's stance: the framer authored at T1; your forensic verifies T2; seniority doesn't stop the world from moving in between. Surface the discrepancy, don't suppress it. (Anti-pattern: [PROTOCOL.md §9.3](../PROTOCOL.md).)

---

## What "fluent use" looks like

You'll know you've adopted the discipline when:

- You open the named artifact before writing code, reflexively, on inherited tasks.
- You *don't* run the check on premises you verified this session - and you don't feel guilty about it.
- You catch a stale premise and your first move is the reframe memo, not the fix.
- You name a receiver for every reframe without being reminded.
- You treat an impressive count or cost delta as a reason to investigate, not a reason to act.
- You stop reading a handoff note as instructions and start reading it as a *T1 snapshot to verify.*

This typically takes a week or two of consistent pickups.

---

## Common adoption failures

**Reading the note instead of the artifact.** The whole failure mode is that the note drifted from the artifact. Trusting the note re-commits the original sin. Open the artifact.

**Firing on everything.** Running the check on typos and freshly-verified premises trains you to skim it, and a skimmed check catches nothing. Gate hard.

**Catching the drift but proceeding anyway.** You verify, you find the premise is stale, and then you build the original prescription out of momentum. The catch is worthless if it doesn't change what you build.

**Reframe without a receiver.** "This probably belongs somewhere else" and then moving on. The correction is real but unrouted; it gets re-lost on the next pickup.

**Trusting a citation because it looks authoritative.** A cited statute / spec / standard that you didn't actually look up is a Class C waiting to happen. The authoritative *form* is exactly what lets the wrong *content* survive review.

---

## When it doesn't fit your work

Some workflows genuinely don't need this:

- **Slow, serial, solo work** where you pick up tasks within hours of filing them and nothing else changes the system in between. The premise rarely drifts; the check rarely fires; it's below break-even.
- **Greenfield work** with no inherited framing - you're authoring premises, not consuming them.
- **Tasks where every site is in scope by construction** - code-invariant changes where the check is the trivial Find Usages.

The fire/skip judgment ([PROTOCOL.md §6](../PROTOCOL.md)) is the calibration. When in doubt: if you cannot point at a specific inherited claim the task depends on, you don't need the forensic.

---

## Need help?

Issues and discussions on this repo are open. The single most valuable input is a **second-project adoption report** - a catch (or a miss) from a project that isn't mine. That's the evidence this v0.1 needs to become a v1.0.

- Moran Bickel
