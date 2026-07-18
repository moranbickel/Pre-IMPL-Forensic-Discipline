# Reframe Memo - Template

File this when a [§B forensic check](./forensic-check-checklist.md) contradicts a task's framing, **before** writing implementation code. The memo converts a private forensic observation into a routed correction. Without it, the catch gets re-discovered and re-lost on the next pickup.

Keep it short - five fields. The discipline is in the *receiver* field as much as the finding.

---

```
# REFRAME: <task id / short title>

DATE: <YYYY-MM-DD>    CLASS: <A | B | C>    SUBCLASS: <optional - see PROTOCOL §4>

## 1. Original frame
<What the task assumed, quoted or closely paraphrased. The premise as inherited.>

## 2. Forensic finding
<What the §B check actually found. Name the artifact, the command, the evidence.
 Be specific: "opened builder.py:412 - the guard is present, restored in commit
 abc1234 on <date>" beats "the bug seems fixed".>

## 3. Reframed scope
<What the task actually is, now that the premise is corrected. Often "nothing -
 already done" (Class A), or "fix the actual artifact, which is <elsewhere>"
 (Class B), or "the cited basis is wrong; re-ground the approach" (Class C).>

## 4. Class
<A - state-drift (was true, world moved)
 B - structural-misreference (named the wrong artifact / missed an existing one)
 C - doctrinal-mis-citation (cited a source that doesn't support the claim)>
<+ subclass label if one applies, e.g. OPERATOR-SUBSTRATE-CONFABULATION>

## 5. Confirmed receiver
<The workstream / backlog row / code locus that OWNS the reframed scope.
 NOT "probably belongs in X" - confirm by reading X's current state.
 If no receiver exists, file a stub as the receiver and name it here.>
```

---

## The two fields people get wrong

**Field 2 (finding) is evidence, not a hunch.** "Seems already fixed" is not a finding; "builder.py:412 has the guard, added in `abc1234` 2026-05-12, predating this pickup" is. The next reader has to be able to trust the finding without re-running the check.

**Field 5 (receiver) is mandatory and must be confirmed.** A reframe with no receiver is a memo into the void (PROTOCOL §5.3). "This probably belongs in the validation workstream" is insufficient - open the validation workstream's current state and confirm it can own this. If nothing can, the correct move is to *create* the receiver (a stub row / issue) and name it. The correction has to land somewhere durable, or it isn't a correction - it's a note you'll lose.
