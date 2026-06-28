# Pre-IMPL Forensic Discipline

**A verification step that checks a task's *premise* before any implementation begins.** The framing of a task (what was assumed true when the task was written) drifts from the *current state* of the system by the time someone picks it up. This discipline gives you a checklist, a taxonomy of how the framing drifts, and an obligation to reframe, so the drift gets caught before it turns into a wrong commit.

If you've ever picked up a two-day-old note that said "fix the broken X in module Y," started writing the fix, then opened module Y to find it had been rewritten yesterday and the bug was already gone, this is for you.

I built it while developing [ORCA](#about-orca), an AI legal reasoning system for Israeli civil litigation. It's one of a series of methodology pieces I'm publishing from that work, alongside [Russian Judge](https://github.com/moranbickel/russian-judge), [Three-Body Protocol](https://github.com/moranbickel/three-body-protocol), [Peer-Worker Convergence](https://github.com/moranbickel/peer-worker-convergence), and [CSAE](https://github.com/moranbickel/csae).

> **Status: v0.1 draft.** Unlike its siblings, this piece is published as a scoping artifact, not a finished methodology. The discipline is operational and battle-tested, but only on a single project. The evidence is strong and narrow. See [EVIDENCE.md](./EVIDENCE.md) for the preconditions that gate a v1.0, and [CONTRIBUTING.md](./CONTRIBUTING.md) for the one input that would move it: second-project adoption reports.

---

## The failure it solves

A session ended with a handoff note: *"Next: fix the silent-pass in the export gate. The guard around line 400 was dropped."*

The next session picked it up, read the note, and started writing the guard back in. Twenty minutes later, opening the file to place the fix, the picture changed: the function had been rewritten the previous evening by a parallel session. The guard was already there. The line numbers in the note pointed at code that no longer existed. The bug the note described had been fixed *before the note was even picked up.*

The note was true when it was written, and false by the time it was read. Nothing in the workflow forced anyone to check the gap between those two moments.

Twenty minutes is the cheap version. The expensive version is when the wrong premise survives all the way into a shipped commit. You build the fix the stale note described, it passes review (the *work* is fine, and review looks at the work, not the premise), and it ships. Now you've spent hours building something that solved a problem that no longer existed, or worse, that *reintroduced* one a parallel agent had just removed.

This isn't a rare edge case in AI-assisted development. It's the structural result of three things happening at once:

1. **Velocity.** A workstream that used to take weeks now takes days. A status that was current two days ago can be stale by the time someone picks it up.
2. **Parallelism.** Multiple agents (sessions, worktrees, operator-plus-AI in tandem) each ship work that invalidates the others' inherited assumptions, with no explicit synchronization.
3. **Memo-driven coordination.** Sessions hand off through notes, status documents, and memory artifacts. Each is a snapshot of one moment that the next agent treats as current, and the snapshot ages between writing and reading.

The faster and more parallel the system, the more the premise drifts. Past a certain speed, checking the premise before acting on it stops being optional hygiene. It becomes a precondition for the system to produce reliable output at all.

Pre-IMPL Forensic is that check.

---

## What it is, concretely

Three artifacts, applied at the moment a task is picked up:

1. **The §B Forensic Check** is a seven-item checklist you run *before* any implementation. Open the named file. Run the named producer. Check the mtimes. Read the integration partners' actual code. (Full list in [PROTOCOL.md §2](./PROTOCOL.md).)

2. **The A/B/C drift taxonomy** is how you classify the drift when the check contradicts the framing. **Class A** is state-drift: it was true, the world moved. **Class B** is structural-misreference: the framing named the wrong artifact. **Class C** is doctrinal-mis-citation: it cited a source that doesn't say what the framing claims. There's also a catalog of ten named subclasses seen in production.

3. **The reframe obligation** says a contradicted premise produces a *reframe memo* before any code is written. The memo records the original frame, the forensic finding, the reframed scope, the class, and a **confirmed receiver** (the workstream or place that owns the corrected scope). The fix after a reframe is routinely *different* from what the task asked for.

---

## What this is not

The discipline overlaps with established practices but isn't any of them.

**It isn't code review.** Code review (including [Russian Judge](https://github.com/moranbickel/russian-judge)) looks at completed work. Pre-IMPL Forensic looks at the *premise* before work begins. It catches "we're about to do the wrong work," not "the work we did is flawed." The two compose: forensic checks the premise, review checks the result.

**It isn't test-driven development.** TDD needs a clear specification to write tests against. Pre-IMPL Forensic checks that the specification itself isn't built on stale reality. A green test suite proves you built the thing right. It says nothing about whether the thing was still worth building.

**It isn't requirements elicitation.** Elicitation gathers what the user wants. Pre-IMPL Forensic checks what the system *currently is*, against the assumption baked into the task framing.

**It isn't refactoring discipline.** Refactoring assumes the work to be done is known. Pre-IMPL Forensic checks that the work-to-be-done is correctly framed in the first place.

**Its closest sibling is scientific replication.** Both share the shape "before publishing the result, verify the prerequisite holds in the actual system." But replication operates on findings: low-frequency, high-stakes per check. Pre-IMPL Forensic operates on task framings: high-frequency, low-stakes per check, and far more amenable to a checklist.

---

## Pre-IMPL Forensic vs. alternatives

| Dimension | Code review / [RJ](https://github.com/moranbickel/russian-judge) | TDD | Requirements elicitation | Pre-IMPL Forensic |
|---|---|---|---|---|
| Operates on | the completed work | the work, against a spec | what the user wants | the **premise** of the task |
| Fires | after the work is done | before + during implementation | before the work | before the work, **at pickup** |
| Catches | flawed output | regressions against the spec | the wrong target | the wrong **framing** — a stale or misnamed premise |
| Assumes | the task was correctly framed | the spec is current | — | nothing; it **verifies** the spec is current |
| Cost per application | minutes–hours | ongoing | upfront | 5–30 minutes |

The disciplines are layers, not substitutes. In my own practice the stack is: forensic checks the premise, TDD constrains the build, [RJ](https://github.com/moranbickel/russian-judge) audits the result, [CSAE](https://github.com/moranbickel/csae) attests the landing. Each answers a question the others don't.

---

## Sibling pieces

[Russian Judge](https://github.com/moranbickel/russian-judge) is the review layer this one is explicitly *not*. RJ asks "is the work I did flawed?" Pre-IMPL Forensic asks "is the work I'm about to do correctly framed?" RJ fires after the work; this fires before. Run both, and a premise that survives verification produces work that survives review.

[Three-Body Protocol](https://github.com/moranbickel/three-body-protocol) and [Peer-Worker Convergence](https://github.com/moranbickel/peer-worker-convergence) are the coordination layers that *produce* the artifacts this discipline distrusts. Their handoff notes, status documents, and worker branches are exactly the snapshots that age between writing and pickup. Three-Body keeps sessions aligned across time, Peer-Worker keeps parallel workers converging, and Pre-IMPL Forensic assumes both will drift anyway and checks the premise at the moment of consumption. The three compose: coordination reduces the drift, forensic catches the residue.

[CSAE](https://github.com/moranbickel/csae) attests what landed on `main`. Pre-IMPL Forensic checks what *should* be worked before it lands at all. CSAE guards the entrance; this guards the intent.

---

## Repository contents

| File | What it is |
|---|---|
| [README.md](./README.md) | This file: what, why, when, and how it compares. |
| [PROTOCOL.md](./PROTOCOL.md) | The formal specification (§§1–11). The source of truth. |
| [EVIDENCE.md](./EVIDENCE.md) | The empirical basis, the single-project caveat, the v1.0 preconditions. |
| [docs/rationale.md](./docs/rationale.md) | Why it exists, what it replaces, why velocity made it load-bearing. |
| [docs/how-to-adopt.md](./docs/how-to-adopt.md) | Concrete steps to start applying it. |
| [docs/faq.md](./docs/faq.md) | Anticipated questions. |
| [templates/forensic-check-checklist.md](./templates/forensic-check-checklist.md) | The §B checklist as a copy-paste artifact. |
| [templates/reframe-memo-template.md](./templates/reframe-memo-template.md) | The reframe-memo structure. |
| [examples/state-drift-catch-walkthrough.md](./examples/state-drift-catch-walkthrough.md) | A synthetic end-to-end **Class A** (state-drift) catch — the dominant class, where the correct build is *nothing*. |
| [examples/forensic-catch-walkthrough.md](./examples/forensic-catch-walkthrough.md) | A synthetic end-to-end **Class B** (structural-misreference) catch — the named artifact was the wrong one. |
| [diagram.svg](./diagram.svg) | The catch → classify → reframe → route lattice. |

---

## About ORCA

ORCA is a production AI legal reasoning system for Israeli civil litigation: a decision system, not a document generator. It reasons about which causes of action hold, which elements the evidence supports, and what relief follows. (A programmer builds a document generator; a litigator builds a decision system.) It runs several AI coding sessions in parallel against a shared repository, coordinated through status documents and handoff memos. That setup is exactly the one that produces premise drift: high velocity, parallel agents, memo-driven bridging.

The discipline emerged because acting on a stale premise in that environment was costing hours of dead-end work per day. The checklist, the taxonomy, and the subclass catalog are sanitized generalizations of internal rules that earned their place by catching drift again and again. This repo is the public version of that discipline; the legal-domain specifics stay in ORCA.

---

## License

Dual-licensed. Prose (README, PROTOCOL, EVIDENCE, docs) under [CC BY 4.0](./LICENSE-CC-BY-4.0); templates and any code under [MIT](./LICENSE-MIT). See [CITATION.md](./CITATION.md) to cite.

— Moran Bickel
