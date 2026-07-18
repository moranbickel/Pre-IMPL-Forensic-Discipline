# Pre-IMPL Forensic Discipline - Protocol Specification

**Version:** 0.1 (draft)
**Status:** Operational on one project; pre-v1.0 pending cross-project validation
**Author:** Moran Bickel

This document specifies the discipline formally. It is the canonical reference. The README is a friendly entry point; this is the source of truth.

A note on the name: "§B" is a historical label. In the originating project the forensic check was item B of a larger rule. The label stuck because the artifacts (memos, checklists, traces) all reference it. It is preserved here verbatim rather than renamed, so adopters reading the worked examples and the originating literature see the same token.

---

## §1. Scope and Roles

**§1.1 What the discipline operates on.** Not code, not the work product, not requirements. It operates on the *premise* of a task: the set of claims about the current state of the system that the task's framing assumes to be true. The premise is almost always inherited: authored at one moment (T1), consumed at another (T2).

**§1.2 The drift it catches.** Between T1 and T2 the system changes. The premise does not. The discipline fires at T2 and asks whether each load-bearing claim in the premise still holds against the system as it actually is now.

**§1.3 Roles.** Three, possibly played by the same actor.

- **Framer (T1).** Authors the task framing: a plan item, a backlog row, a handoff memo, a status bullet, an audit classification. Acts in good faith on the state visible at T1.
- **Picker-up (T2).** Receives the framing and is about to act on it. Owns the forensic obligation. This is the role the discipline binds.
- **Operator.** Decides whether a given task warrants the check (per §6), and adjudicates reframes whose corrected scope exceeds the picker-up's authority.

**§1.4 When it fires.** The discipline fires when a task carries *inherited framing* AND wrong-framing cost ≥ verification cost. Concretely, when the task arrived via any of:

- A plan or spec reference ("WS-X item 4").
- A backlog row in any tracking document.
- A pickup memo from a prior session.
- A reframe pointer from another agent's output.
- An audit-memo classification.
- A registry annotation citing empirical frequency.
- A status document's "outstanding / next session" bullet.
- A finding emitted by a generated artifact (a test result, a CI report, a build or analysis verdict) treated as describing the current code.
- A coverage or subsumption claim ("X already handles this," or its inverse, "this needs a new X") where X is named but its actual scope has not been read.

**§1.5 When it does NOT fire.** The discipline is selective, not a universal speed-tax. It does not fire when:

- The task is purely doctrinal, typo-class, or cleanup work with no load-bearing inherited claim.
- The premise is being **proposed for the first time** in the same session: the framing *is* the authoring scope, not an inherited claim.
- The cited artifact was **freshly verified in the same session** by the same actor.
- The task targets a code-level invariant where every site is in scope by definition (e.g., "rename function X across all callers"; Find Usages is the trivial check).
- Per-task implementation cost is XS such that the forensic exceeds the implementation (net-negative discipline).

The "does not fire" list is load-bearing. A discipline that fires on everything trains the actor to skim it, and a skimmed check catches nothing.

---

## §2. The §B Forensic Check

When a task triggers the discipline, the picker-up runs this seven-item check BEFORE any implementation work. Each item is a concrete action against a real artifact, not a mental review.

**§2.1 Read the named artifact at the named path.** If the task says "X is broken at Y, line Z," open Y, go to line Z, read it. Does the code match the description? If the task names a file, verify the file exists at that path. *(Catches the most common drift: the named thing is no longer what the framing describes, or no longer there.)*

**§2.2 Run the producer/consumer reachable from the runtime.** If the task is about a "subprocess failure" or a "silent pass," invoke the named producer manually with the same arguments the real system uses. Capture stderr/stdout. *(State-drift detection zone: the failure described at T1 may already be fixed, or may fail differently now.)*

**§2.3 Check actual file mtimes against the task's claims.** If the task says "the artifact's last-modified date is the 21st," verify it. *(Distinguishes "stale and unread" from "fresh but the framing didn't account for it.")*

**§2.4 Cross-verify path resolution against the running workspace.** If the task says "writes to X," inspect every working tree / branch / environment for recently-modified versions at X. *(Cross-workspace pollution zone: in parallel-agent systems the "same" file exists in several places at once.)*

**§2.5 Scan for sibling instances if the bug class is recognizable.** The task flags one site; grep the codebase for the same pattern shape. Five-plus siblings means the right move is a class-sweep workstream, filed *before* the individual symptom fix. *(Turns a one-off fix into a root-cause fix, or surfaces that the "one bug" is one of many.)*

**§2.6 Read the integration partners' actual code.** When the task references a named partner the work will consume from or produce for (a gate, script, registry, schema, tool), read that partner's actual code before authoring tests, specs, or the fix. Item §2.1 verifies the bug surface; item §2.6 verifies the *integration* surface. *(Catches the framing that assumes a partner exists, or does something it doesn't.)*

**§2.7 Re-read status documents after any boot-time merge.** If the actor started in a workspace that was behind canonical and pulled to catch up, the status document was authored before the canonical state arrived. Priority intent formed pre-merge can be wrong by construction. Re-read after the merge, before forming intent. *(Catches the meta-drift where the very document you used to choose the task was stale.)*

**§2.8 The contradiction rule.** If ANY of §2.1-§2.7 contradicts the task's framing, the picker-up files a reframe memo (per §5) BEFORE writing code. The fix scope after reframe is routinely different from the task's original prescription.

---

## §3. The Three Classes

Drift between authored framing (T1) and pickup-time reality (T2) takes three shapes. Every reframe names its class.

| Class | What's wrong | Verification question | Detection cost |
|-------|--------------|----------------------|----------------|
| **A - State-drift** | Framing was correct at authoring; the world moved. | "Is this still true *today*?" | Cheap - git log + current code. |
| **B - Structural-misreference** | Framing named the wrong artifact, OR missed a correctly-named existing one. | "Was this *ever* true?" | Medium - read the named artifact + verify it does what the framing claims. |
| **C - Doctrinal-mis-citation** | (Subclass of B for content-bearing work.) Framing cites a source whose actual content does NOT match the claim attributed to it. | "Does the source *say* what the framing claims it says?" | Expensive - source lookup + content reading + claim mapping. |

**§3.1 Class A - State-drift.** The cheapest and most common. The framing was accurate when written; a commit, a parallel agent, or a prior session changed the underlying reality. The verification is a `git log` and a current read. The opening example in the README (guard already restored) is Class A.

**§3.2 Class B - Structural-misreference.** The framing was never accurate: it pointed at the wrong file, named a function that doesn't do what it's said to do, or claimed an artifact exists that doesn't. More expensive because confirming requires reading the named artifact *and* establishing what it actually does.

**§3.3 Class C - Doctrinal-mis-citation.** The most expensive. A content-bearing framing cites an authoritative source (a statute, a spec section, a standard) and attributes a claim to it that the source does not support. Confirming requires looking up the source, reading its actual content, and mapping it against the claim. In domains where the cited content is load-bearing for the output (legal, medical, regulated, standards-conformant), this class is where the most damaging defects hide, because the citation *looks* authoritative.

---

## §4. The Subclass Catalog

Beyond A/B/C, recurring failure modes earn named subclasses for retrieval. Each shares the parent shape (T1 description drifts from T2 truth) but differs in artifact type, drift mechanism, and the verification that catches it. The catalog earns entries empirically: a subclass is added when a recurring instance is documented, not by enumeration.

**§4.1 OPERATOR-SUBSTRATE-CONFABULATION.** An operator's local cache of project state (a stale knowledge mount, an old mental model) carries filenames or dates that no longer exist on canonical. Instructions reference identifiers that have been renamed or never existed. *Mitigation:* verify by **role/content**, not by name. Search for what the artifact *does* (a function, a section header, a doctrine keyword) before searching for its name; an empty name-search is evidence about the *name*, not about the capability. When the operator-named identifier differs from the canonical one, surface the discrepancy. Do not silently substitute, because the mismatch is the signal.

**§4.2 AGENT-PRIOR-OUTPUT-AS-PREMISE.** One agent's earlier output is adopted as a premise by the same or another agent without decomposition. Aggregates ("N events across M components") are especially hazardous: they propose an action while embedding a causal interpretation that needs separate verification. *Mitigation:* decompose the aggregate before acting on it; verify the interpretation, not just the count.

**§4.3 AGENT-PICKUP-MEMO-RE-OPENS-RESOLVED-SCOPE.** A session-bridging memo re-opens scope that a prior session already settled, because the memo's author worked from memory of "what was open" rather than from current canonical state. *Mitigation:* verify each inherited "still open" claim against the canonical state record before honoring it.

**§4.4 AUDIT-MEMO-DOC-DRIFT.** A canonical document (an audit memo, a registry annotation, a tracking row) carries a stale classification while authoritative ground truth (a code state, a corrected registry note) has moved. Two authoritative sources disagree. *Mitigation:* when two sources conflict, the one closest to executable reality (the code, the runtime) wins over the one closest to prose.

**§4.5 ROW-TEXT-DRIFT-FROM-SHIPPED-IMPL.** A tracking row describes intended scope at filing time; the shipped implementation evolved the actual scope; the row text never auto-updated. *Mitigation:* read the shipped implementation, not the row's description of it.

**§4.6 OPERATOR-INSTINCT-FROM-MAGNITUDE.** Impressive data (a large count, a big cost delta) suggests an action, but the structural reasoning about *which* action requires separate verification. **Magnitude is a signal, not a fix selector.** *Mitigation:* treat the magnitude as a reason to investigate, not as a reason to act; verify the mechanism before choosing the fix.

**§4.7 STRUCTURAL-FINDING-ON-SINGLE-SAMPLE.** An empirical finding rests on N=1 evidence and is generalized to a population. The single sample may be unrepresentative. *Mitigation:* cross-sample before broad action; one instance is a hypothesis, not a pattern.

**§4.8 SURFACE-FIX-MASKS-DEEPER-ROOT-CAUSE.** The instinctive fix at the symptom layer would mask a defect at an upstream layer, and is particularly hazardous when the symptom-layer fix is technically correct in isolation. *Mitigation:* before fixing at the symptom layer, trace one layer upstream and confirm the root is not there.

**§4.9 GENERATED-ARTIFACT-FINDING-WITHOUT-PROVENANCE.** A finding emitted by a generated artifact (a test result, a CI report, a build log, a static-analysis verdict, an output bundle) is treated as describing the *current* code, when the artifact was in fact produced by an *earlier* version of the tree. The finding is real; its subject is stale. Acting on it (filing a defect, citing it as done-criteria, closing a task, opening a reframe) propagates a conclusion about code that no longer exists in that form. *Mitigation:* before treating a generated finding as live, verify the artifact's provenance (the commit or tree version that produced it) is current. If the producing tree is behind canonical, the finding may describe the old producer; re-generate or re-verify against the current code before acting. This is the artifact-layer analog of §2.7: there, the *status document* you chose the task from was stale; here, the *result* you are reasoning from is. It is also cheaply mechanizable: stamping each generated artifact with the producing commit, then comparing that against canonical at consumption time, is exactly the kind of checkable provenance §8.1 calls for.

**§4.10 SUBSUMPTION-CLAIM-BY-NAME.** A change is scoped on the premise that an existing artifact already covers it ("rule / component / document X handles this"), or its inverse, that nothing covers it, so a new artifact is needed. The premise is settled by the candidate's *name or title*, not by its *predicate* (what it actually governs). A name that sounds adjacent is taken as coverage; the candidate's real scope is never read. *Mitigation:* read the candidate's actual predicate and test subsumption directly. Conclude "already covered" only when the new concern is genuinely a subset of what the candidate governs; create a new artifact only when it is genuinely *not* a subset of any existing one. The discipline is a subsumption *test* (read-the-predicate), not a default-to-merge or a default-to-create *bias*; both directions are the same name-matching error. This subclass is self-referential: it governs the growth of this very catalog (is a freshly-caught drift a new subclass, or an instance of an existing one?), which is exactly why the test is read-the-predicate, not match-the-title.

---

## §5. The Reframe Obligation

**§5.1 The trigger.** Any contradiction surfaced by §2 obliges a reframe memo *before* implementation. The memo is the artifact that converts a private forensic observation into a routable correction.

**§5.2 The five required fields.** A reframe memo names:

1. **Original frame** - what the task assumed, quoted or closely paraphrased.
2. **Forensic finding** - what the §B check actually found, with the artifact and the evidence.
3. **Reframed scope** - what the task actually is, now that the premise is corrected.
4. **Class** - A, B, or C (plus subclass if one applies, per §4).
5. **Confirmed receiver** - the workstream, backlog row, or code locus that owns the reframed scope.

**§5.3 The confirmed-receiver rule.** A reframe without a confirmed receiver is a memo into the void. "This probably belongs in workstream X" is insufficient. Confirm by reading X's current state and establishing that it can own the corrected scope. If no receiver exists, file a stub *as* the receiver. A correction nobody owns is a correction that gets re-discovered, re-investigated, and re-lost on the next pickup.

**§5.4 The fix is usually different.** The point of the reframe is not to annotate the task. It is to change what gets built. After a Class A reframe the work may be "nothing, already done." After Class B it may be "fix the *actual* artifact, which is elsewhere." After Class C it may be "the cited basis is wrong; the whole approach needs re-grounding." Proceeding with the original prescription after a contradiction defeats the discipline.

---

## §6. The Fire/Skip Judgment

**§6.1 The cost asymmetry.** The decision to fire or skip is asymmetric in favor of firing. Skipping verification on a misframed task wastes hours-to-days of downstream implementation; firing it on a correctly-framed task wastes 5-30 minutes of forensic. When uncertain, fire.

**§6.2 The break-even.** Each forensic check costs 5-30 minutes. Each catch prevents 0.5-5 hours of wrong work. The discipline pays for itself when **5% or more** of checked tasks would have been wrong-framed. In the originating project's measured window the actual catch rate was 20-30%, well above break-even.

**§6.3 The skip is a real option, not a failure.** Skipping per §1.5 is correct discipline, not a shortcut. Firing on a typo fix or a freshly-verified premise is the same anti-pattern as code-reviewing a comment edit: it dilutes the signal. The skill is calibration, not maximalism.

---

## §7. Relationship to Adjacent Disciplines

**§7.1 Code review / adversarial review.** Operates on completed work; Pre-IMPL Forensic operates on the premise. Sequential, not overlapping: forensic before, review after. A premise that passes forensic produces work that can pass review meaningfully; review of work built on a wrong premise is review of the wrong thing.

**§7.2 TDD.** TDD constrains the build against a spec. Pre-IMPL Forensic verifies the spec is current. They compose: forensic first (is the spec real?), TDD second (does the build match it?).

**§7.3 Root-cause analysis.** §2.5 (sibling scan) and subclass §4.8 (surface-masks-root) are root-cause discipline applied at pickup time. Where root-cause analysis is usually retrospective (after a defect ships), Pre-IMPL Forensic pulls it forward to before the fix is even written.

**§7.4 Wiring / data-flow verification.** §2.6 (read integration partners) is the static-analysis sibling of runtime data-flow tracing. Forensic reads the partner's code; runtime tracing confirms the value actually flows. Forensic is cheaper and earlier; tracing is the fallback when reading is inconclusive.

---

## §8. The Mechanization Boundary

The checklist splits into a mechanizable subset and a judgment subset.

**§8.1 Mechanizable.** File-mtime against a memo claim, `git log` against a state claim, grep for sibling instances, ancestry checks of a cited commit against canonical. These are tool-able and should be tooled: they are the cheap, high-frequency checks where automation removes the friction that tempts skipping.

**§8.2 Judgment.** Is this a *load-bearing* claim or narrative color? Is this artifact substantively misnamed or just informally referenced? Does the cited source actually support the claim (§3.3 Class C)? These require reading and judgment and will remain human/AI discretion.

**§8.3 The honest limit.** Tooling the mechanizable subset does not retire the discipline; it lowers the cost of the cheap checks so the expensive ones (the judgment subset, where the costliest drift hides) get the attention. A green mechanical check is not a passed forensic; it is one input to it.

---

## §9. Anti-Patterns

**§9.1 Documentation mistaken for discipline.** A memo that *describes* this discipline does not *enforce* it. The discipline operates only when the pickup-time check actually fires. In the originating project, methodology documentation sat in the repo at the same time as the failure mode it described kept recurring at pickup, because the documentation was never read at pickup. The check is the discipline; the doc is a reference.

**§9.2 Velocity-pressure skip.** Under "just ship" pressure, the check is the first thing dropped. The cost-saved math does not change under pressure; only the *apparent* friction does. The catches the check would have produced are exactly the ones that turn into the next session's crisis.

**§9.3 Authority-asymmetry override.** Treating a senior framer's premise as more authoritative than the picker-up's forensic finding. The correct framing: the framer authored at T1; the forensic verifies T2. Seniority does not stop the world from moving between those two moments. The reframe is not insubordination; it is the discipline working.

**§9.4 Reframe-into-void.** Filing a reframe with no confirmed receiver (violates §5.3). The correction is real but unrouted; it gets re-lost.

**§9.5 Premise-of-the-aggregate.** Acting on another agent's summarized count or conclusion without decomposing it (subclass §4.2). The aggregate smuggles an interpretation past verification.

**§9.6 Fire-on-everything.** Running the forensic on typo fixes and freshly-verified premises (violates §1.5). Dilutes the signal until the actor skims and the check catches nothing.

**§9.7 Subsumption-by-name.** Concluding "already covered by X" (or "needs a new X") from a name match rather than a read of what X actually governs (subclass §4.10). Both the false merge (folding a genuinely-new concern into an artifact that doesn't cover it, where it is silently dropped) and the false split (creating a redundant artifact for a concern an existing one already owns) are the same error: the title was checked, the predicate was not.

**§9.8 Stale-result-as-current.** Reasoning from a generated artifact's finding without checking the artifact's provenance (subclass §4.9). The result is real but describes an earlier tree; the conclusion is applied to code that has since moved.

---

## §10. Limitations

The discipline does not solve:

- **Operator-side authoring drift.** It fires reliably on agent-side pickup. The operator authoring the pickup memo or status bullet is subject to the same drift, but operators have no procedural forensic obligation in the same sense. Operator-side enforcement is weaker by construction.
- **Unknowable premises.** If the premise's truth cannot be checked against any reachable artifact (it depends on external state the actor can't observe), the check cannot fire. The discipline catches drift in *checkable* claims.
- **Cross-project generality.** All current evidence is single-project. The shape appears general; it has not been validated in a second-project pilot. This is the gating limitation for v1.0 (see [EVIDENCE.md](./EVIDENCE.md)).
- **The judgment subset.** "Is this load-bearing?" and "is this misnamed?" remain discretion. A picker-up who misjudges load-bearingness skips a check that mattered. The discipline structures the judgment; it does not produce it.

---

## §11. Versioning

This is v0.1, a draft, explicitly pre-v1.0. Material changes to the A/B/C taxonomy, the §B checklist item set, or the reframe obligation produce a major version. Refinements to the subclass catalog, the fire/skip heuristics, or the anti-patterns produce minor versions (v0.2, v0.3, …). v1.0 is gated on the publication preconditions in [EVIDENCE.md](./EVIDENCE.md): cross-project validation, a subclass library at the 30-instance target, and the originating project past active crisis.

---

*End of specification.*
