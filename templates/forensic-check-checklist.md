# §B Forensic Check - Checklist

Copy this into your task tracker, pickup ritual, or agent prompt. Run it on any task that arrives with **inherited framing** and is more than trivial, **before** writing any implementation. (When to skip entirely: see [PROTOCOL.md §1.5](../PROTOCOL.md).)

The discipline is satisfied by *doing* the actions, not by reading them. Each item is a concrete operation against a real artifact.

---

## The seven items

```
TASK: <one line - what the inherited framing says to do>
SOURCE OF FRAMING: <backlog row / handoff memo / plan ref / status bullet / audit memo>
AUTHORED AT (T1): <when the framing was written, if known>
PICKED UP AT (T2): <now>

[ ] 1. READ THE NAMED ARTIFACT.
       Opened: <path>  →  matches framing? [ yes / NO ]
       (If a path/line is named, go there and read it. Does the file exist?
        Does the code match the description?)

[ ] 2. RUN THE PRODUCER/CONSUMER.
       Ran: <command>  →  behaves as framing claims? [ yes / NO / n/a ]
       (For "subprocess fail" / "silent pass" framings: invoke the named
        producer with the real arguments. Capture stderr/stdout.)

[ ] 3. CHECK MTIMES VS CLAIMS.
       Claimed: <date/state>   Actual: <mtime>   →  consistent? [ yes / NO / n/a ]

[ ] 4. CROSS-VERIFY PATH ACROSS WORKSPACES.
       Checked trees/branches/envs: <list>  →  one canonical version? [ yes / NO / n/a ]
       (In parallel-agent systems the "same" file exists in several places.)

[ ] 5. SCAN FOR SIBLING INSTANCES (if the bug class is recognizable).
       grep pattern: <pattern>   hits: <n>
       → 5+ hits means file a class-sweep BEFORE the symptom fix. [ isolated / CLASS ]

[ ] 6. READ THE INTEGRATION PARTNERS' CODE.
       Partner(s): <gate / script / registry / schema / tool>
       → partner does what the framing assumes? [ yes / NO / n/a ]
       (Item 1 verifies the bug surface; item 6 verifies the integration surface.)

[ ] 7. RE-READ STATUS DOCS AFTER ANY BOOT MERGE.
       Did this workspace pull to catch up at start? [ yes / no ]
       If yes: re-read <status doc> AFTER the merge, BEFORE forming intent. [ done ]

CONTRADICTION FOUND? [ none - proceed / YES - file a reframe memo first ]
```

---

## The contradiction rule

If **any** item comes back `NO`, stop. Do not implement against the original framing. File a reframe memo ([template](./reframe-memo-template.md)) naming the class (A/B/C) and a confirmed receiver, then decide what to actually build - it is routinely different from what the task said.

## The fire/skip reminder

Don't run this on: typo/cleanup work, premises you authored or verified this same session, or code-invariant changes where every site is in scope by definition. Firing on everything trains you to skim it. Gate hard, then trust it when it fires.
