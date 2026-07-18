# FAQ

Anticipated questions about Pre-IMPL Forensic Discipline.

---

### Why "Pre-IMPL"? And what's "§B"?

"Pre-IMPL" = pre-implementation: the check fires *before* implementation work, on the task's premise. "§B" is a historical label - in the originating project the forensic check was item B of a larger rule, and the artifacts (memos, checklists, traces) all reference it by that token. It's preserved verbatim rather than renamed so the worked examples and originating literature stay consistent. You can call it "the forensic check"; the spec calls it §B.

---

### How is this different from "just reading the ticket carefully" or "being a careful engineer"?

The same way [Russian Judge](https://github.com/moranbickel/russian-judge) is different from "just reviewing carefully": it makes an implicit good habit into an explicit, checkable, named discipline that fires reliably instead of when you happen to remember.

"Be careful" doesn't survive velocity pressure, doesn't tell you *what* to check, doesn't classify what you find, and doesn't route the correction. Pre-IMPL Forensic gives you a seven-item checklist, a three-class taxonomy, and a reframe obligation with a named receiver. The structure is the point - careful instinct degrades under deadline; a checklist doesn't.

---

### Isn't this just code review / TDD / requirements gathering?

No, and the distinction is the whole reason it exists:

- **Code review** operates on completed work. This operates on the *premise* before work begins. Review of work built on a wrong premise is review of the wrong thing.
- **TDD** verifies the build against a spec. This verifies the spec is still *current.* A green suite says you built it right, not that it was worth building.
- **Requirements gathering** establishes what the user wants. This establishes what the system *currently is*, against the assumption the task carries.

They're layers, not substitutes. See the comparison table in the [README](../README.md).

---

### Why three drift classes? Why not two, or eight?

Two classes ("stale" / "wrong") collapses a real distinction: a premise that *was* true and drifted (Class A) is checked differently and far more cheaply than one that was *never* true (Class B). And a cited-source mismatch (Class C) is checked differently again - you have to read the source.

More than three at the top level diffuses the signal; you start hedging between adjacent classes. So the top level is three, each with a distinct verification question, and the recurring specific shapes live in the *subclass catalog* (§4) where they're retrieval aids rather than a taxonomy you have to navigate. Three classes + an open subclass catalog is the structure that held.

---

### Doesn't running a check before every task slow everything down?

Only if you run it on everything - which the discipline explicitly tells you not to (PROTOCOL §1.5). It fires on inherited, non-trivial framings and skips typos, freshly-verified premises, and code-invariant changes.

On the tasks where it does fire, the cost is 5-30 minutes and the expected saving is 0.5-5 hours when it catches something. It breaks even at a 5% catch rate; the originating project saw 20-30%. The check is the cheap path - the expensive path is the hours spent implementing against a premise that had already moved.

---

### Who runs the check - the human or the AI agent?

Either, and ideally the agent by default with the human as backstop. The discipline binds the *picker-up* role - whoever is about to act on the inherited premise. In an AI-assisted workflow that's usually the agent, which is why the checklist is written as concrete tool actions (open the file, run the producer, check the mtime) an agent can execute. The human operator owns the fire/skip judgment on ambiguous cases and adjudicates reframes whose corrected scope exceeds the agent's authority.

---

### How is this different from telling the agent to "think step by step" or "verify your assumptions"?

"Verify your assumptions" is to this discipline what "review carefully" is to a structured review protocol - the right instinct, unstructured. The agent told to "verify" will verify *something*, often the easy or already-believed claims. The §B checklist names the *specific* artifacts to check (the named file, the named producer, the integration partner, the status doc post-merge) and the contradiction rule forces a reframe with a receiver. Structure converts a vague instruction into a reliable, auditable step.

---

### Can this be mechanized / automated?

Partially. The cheap checks - file mtime vs. a claimed date, `git log` vs. a state claim, grep for sibling instances, ancestry of a cited commit - are scriptable and should be (PROTOCOL §8). The judgment checks - is this claim load-bearing? is this artifact substantively misnamed? does the cited source actually support the claim? - require reading and stay human/AI discretion. Tooling the cheap subset doesn't retire the discipline; it removes the friction from the high-frequency checks so attention goes to the expensive ones where the worst drift hides.

---

### Does this only apply to AI-assisted development?

The failure mode (premise drifts between authoring and pickup) exists in any project. But it's *load-bearing* specifically when velocity is high and work is parallel - which is the AI-assisted case. In a slow, serial, solo workflow the premise rarely drifts before you act on it, the check rarely fires, and it's below break-even. The discipline scales with how fast and how parallel your system is.

---

### What if I run the check and the premise was fine - was that wasted time?

No. A check that confirms the premise bought you the confidence to proceed without the nagging doubt, at a cost of minutes. The break-even math already accounts for this: most checks confirm the premise; the value is in the minority that don't. You don't know which is which until you check - that's why it's a check.

---

### What if I disagree with a reframe? What if the operator's original framing was actually right?

The reframe is a finding, not a verdict you must obey blindly. If your forensic contradicts the framing, you surface the contradiction - that's non-negotiable, because suppressing it is how the drift survives. But the operator may look at your finding and decide the original framing stands (you misjudged what was load-bearing, or the "drift" is intended). What's forbidden is *silently* proceeding on a premise you've found to be contradicted. Surface, then let judgment resolve it.

---

### Why is this v0.1 when the sibling pieces are further along?

Because it's honest about its evidence. The discipline is real and battle-tested, but on a *single* project. The siblings were published when their evidence base was mature; this one's isn't yet - no second-project pilot, subclass library below target. Publishing it at v1.0 would overstate what's been validated. v0.1 says: the discipline works, here's exactly what's missing, here's the input that would close the gap. See [EVIDENCE.md](../EVIDENCE.md).

---

### How does it relate to the other ORCA methodology pieces?

It's the *before*-layer. [Russian Judge](https://github.com/moranbickel/russian-judge) audits work after it's done; this verifies the premise before work starts. [Three-Body Protocol](https://github.com/moranbickel/three-body-protocol) and [Peer-Worker Convergence](https://github.com/moranbickel/peer-worker-convergence) produce the handoff notes and status documents this discipline distrusts - they reduce drift, this catches the residue. [CSAE](https://github.com/moranbickel/csae) attests what lands on main; this verifies what should be worked at all. The stack: forensic (premise) → TDD (build) → RJ (result) → CSAE (landing).

---

### Where's the code?

The discipline is the artifact; there's no central tool to install. The templates are copy-paste - a checklist and a reframe-memo skeleton. A thin tool wrapping the mechanizable checks (mtime/git-log/grep) is plausible for a later version; for now, the cheap checks are a few shell commands you run yourself.

---

### How do I cite this?

See [CITATION.md](../CITATION.md) at the repo root.

---

### Suggestions / improvements?

Open an issue. The highest-value input by far is a second-project adoption report - a real catch (or miss) from a project that isn't the author's. That's the evidence this draft needs. Generic suggestions are less useful than one documented instance.
