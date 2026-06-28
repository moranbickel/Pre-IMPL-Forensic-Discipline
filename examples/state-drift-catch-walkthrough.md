# Worked Example: A State-Drift Catch, End to End

A complete Pre-IMPL Forensic cycle on the **most common** class of catch: state-drift (Class A), where the framing *was* accurate when written and a parallel commit moved the world before pickup. Per [EVIDENCE.md](../EVIDENCE.md), this class dominates the corpus, and it's the cheapest to verify.

This is the full version of the story the [README](../README.md#the-failure-it-solves) opens with: the dropped export-gate guard. Synthetic in its specifics, real in its shape.

The contrasting case, [the Class B walkthrough](./forensic-catch-walkthrough.md), shows a framing that was *never* right (wrong artifact named). This one shows a framing that was right and went stale. Same checklist, same reframe shape, much cheaper check, and a different ending: here the correct build is *nothing*.

---

## The inherited framing (T1)

A session-end handoff note, written the evening before pickup:

> **Next:** fix the silent-pass in the export gate — the empty-payload guard
> around line 400 of `gates/export_gate.py` got dropped in yesterday's refactor.
> Exports with an empty payload are slipping through as `client_ready` instead
> of failing. Re-add the guard.

This is a *good* note. It's specific, it names the file and the line, it explains the symptom, and the bug it describes was real when the note was written. The tempting move is to open `export_gate.py`, scroll to line 400, write the guard back, add a test, and ship. The premise (*the guard is still missing*) feels too obvious to check.

It is exactly the premise to check.

---

## The §B forensic check (T2)

The picker-up runs the [checklist](../templates/forensic-check-checklist.md) before writing anything. State-drift is the class the first two items are built to catch.

**§1 — Read the named artifact.** Open `gates/export_gate.py` and go to line 400. Two things are off at once. First, the guard the note said was dropped is *present*:

```python
def evaluate(payload, run_dir):
    if not payload or len(payload) == 0:          # <-- the "dropped" guard, here
        return Verdict(state="hard_fail", reason="empty payload")
    ...
```

Second, line 400 isn't even where this is: the function was rewritten, and the guard now lives around line 360. The note's line number points at code that no longer exists. Both signals say the same thing: *the file moved after the note was written.*

**§3 — Check mtimes vs claims.** `git log -- gates/export_gate.py` confirms it:

```
a1b2c3d  (yesterday, 21:40)  fix(export): restore empty-payload guard dropped in refactor
9f8e7d6  (two days ago)      refactor: extract export_gate.evaluate (regression: dropped guard)
```

The regression (the dropped guard) and its fix are *both* already in history. The fix landed at 21:40 yesterday, **after** the handoff note was written, by a parallel session that hit the same bug. The note was true when written and false by pickup.

**§2 — Run the producer/consumer.** Don't trust the read alone. Reproduce. Run the gate against an empty-payload fixture:

```
$ run-gate export_gate --fixture empty_payload
HARD_FAIL: empty payload
```

It fails correctly. The silent-pass the note described does not reproduce. Two independent signals now agree: the code shows a guard, and the behavior shows the guard working.

**§7 — Re-read status after the boot merge.** The tell that closes it: this workspace pulled `main` at session start. That pull is what brought in the 21:40 fix. The handoff note was read *before* the merge, so the intent was stale the moment it was formed. Re-reading status after the merge, before writing anything, would have surfaced "export guard: restored" without even opening the file.

Items §1, §2, §3 all came back `NO` (the framing's premise does not hold). The check stops.

---

## The classification

This is **Class A — state-drift.** The framing was accurate at T1: the refactor really had dropped the guard, exports really were silent-passing. Between T1 and T2 a parallel session shipped the fix. Nothing was misframed: the note named the right file, the right symptom, the right guard. The world simply moved underneath it.

It's not Class B: the artifact named (`export_gate.py`) is the correct one; the guard does live there. It's not Class C: no source is cited or mis-attributed. It's the cheapest class, caught by a read and a `git log`, no integration-partner archaeology required.

But "already fixed" is not yet a finished check. **The forensic question for a Class A catch is not "is there a guard?" It's "does the shipped fix cover the scope the note described?"** The note's scope was *empty payload.* Read the restored guard again: `if not payload or len(payload) == 0` covers both the null case and the empty-but-non-null case. Full coverage of the note's scope. If it had only guarded `payload is None`, the reframed scope would be the surviving sliver (empty-but-non-null), not "nothing." Here, it's nothing.

One more cheap item before closing: **§5 — scan for siblings.** The regression was a *refactor* dropping a guard; did the same refactor drop the guard in any sibling gate? `grep -L "len(payload) == 0" gates/*_gate.py` → no other gate consumes `payload` this way. Isolated. (Had the grep surfaced three more gates missing the guard, the real scope would have been a class-sweep, bigger than the note imagined, not smaller.)

---

## The reframe memo

Class A memos are short: the verification was cheap and the finding is clean. Filed before touching anything, per the [template](../templates/reframe-memo-template.md):

```
# REFRAME: export-gate empty-payload guard

DATE: 2026-05-26    CLASS: A    SUBCLASS: parallel-commit-shipped-it

## 1. Original frame
Handoff note: re-add the empty-payload guard dropped from gates/export_gate.py
(~line 400) in the refactor; empty-payload exports are silent-passing.

## 2. Forensic finding
- export_gate.py already contains the guard (now ~line 360; function was rewritten,
  so the note's line 400 is stale too).
- git log: guard restored in a1b2c3d at 21:40 yesterday — AFTER the note (T1) — by a
  parallel session. The dropping refactor (9f8e7d6) and its fix are both in history.
- Reproducer: `run-gate export_gate --fixture empty_payload` → HARD_FAIL (correct).
  The silent-pass does not reproduce.
- Coverage check: restored guard covers empty AND null payload = the note's full scope.
- Sibling scan: no other *_gate.py dropped the same guard. Isolated.
- This workspace's boot merge is what pulled in the fix; the note predates the merge.

## 3. Reframed scope
Nothing to build. The fix already shipped in a1b2c3d and covers the note's full scope.
Original prescription (re-add the guard) would re-implement existing code.

## 4. Class
A — state-drift. Framing correct at T1; parallel commit a1b2c3d moved the world by T2.

## 5. Confirmed receiver
The handoff item itself. Closing it as already-done, citing a1b2c3d. Verified the
commit is on the canonical branch this workspace tracks (not a parallel worktree that
hasn't merged) — `git merge-base --is-ancestor a1b2c3d HEAD` → yes. No sliver remains;
no follow-up row needed.
```

---

## The corrected outcome

The work that ships is **nothing**, and that's the point. The handoff item is closed as already-done with a pointer to `a1b2c3d`. No guard is written, no test is added, no PR is opened.

The forensic cost about five minutes: one file read, one `git log`, one reproducer run. It prevented re-implementing a guard that already existed, which would not have been harmless. The naive build would have produced a second, redundant guard (a confusing diff that reviewers waste time on), or a *merge conflict* against the parallel session's commit, or, the genuinely bad case, a subtly different guard (`payload is None` instead of `len(payload) == 0`) that fights the existing one and reopens the question of which guard is authoritative.

"Nothing to build" is a real deliverable. It's the cheapest possible outcome and the forensic is what lets you reach it with confidence instead of by luck.

---

## What to notice

- **This is the cheapest class and the most common one.** Per [EVIDENCE.md](../EVIDENCE.md), state-drift dominates the corpus. The entire catch is one read plus one `git log`, which is exactly why skipping it feels safe and is not. The cheaper the check, the less excuse for not running it.
- **Two signals agreeing is what makes it decisive.** The code shows a guard *and* the reproducer no longer fails *and* `git log` shows post-T1 activity. Any one alone is suggestive; together they're conclusive. The read tells you what the code says; the reproducer tells you what it does.
- **The boot merge is often the very thing that shipped the fix.** You pulled `main` at session start, then picked up a note that the pull had already closed. Item §7 (re-read status after the merge, before forming intent) catches this class before you even open the file.
- **"Already done" still requires a coverage check, not just an existence check.** The forensic question is whether the shipped fix covers the *scope the note described*, not merely whether some guard is present. A partial parallel fix leaves a sliver, and the sliver is the real (smaller) scope.

---

## What this example does NOT show

- A **Class B** catch (structural-misreference), where the framing named the wrong artifact and the premise was *never* true. That's the [forensic-catch-walkthrough](./forensic-catch-walkthrough.md); it costs more to verify because reading the named file isn't enough; you have to read the integration partner.
- A **Class C** catch (doctrinal-mis-citation), where a content-bearing framing cites a source that doesn't support the claim. The most expensive class; the verification is looking the source up and reading it.
- A **skip**, a task where the check correctly does *not* fire (a typo fix, a premise you verified this same session). Knowing when not to run the check is half the discipline (see [PROTOCOL.md §1.5](../PROTOCOL.md)).

For the full class taxonomy and the subclass catalog, see [PROTOCOL.md §3–§4](../PROTOCOL.md).
