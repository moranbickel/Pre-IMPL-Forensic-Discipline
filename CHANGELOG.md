# Changelog

## Unreleased

- Renamed drift Class C from "Doctrinal-mis-citation" to "Source-mis-citation" in PROTOCOL.md (§3 table, §3.3), the reframe-memo template, both walkthroughs and the diagram description. Definition unchanged in substance: a framing cites a source (spec, document, standard, upstream comment) that does not say what is attributed to it.
- PROTOCOL.md §4.9 - new subclass **GENERATED-ARTIFACT-FINDING-WITHOUT-PROVENANCE**: a finding emitted by a generated artifact (test result, CI report, build/analysis verdict, output bundle) treated as describing the *current* code when it was produced by an *earlier* tree. Mitigation: verify the artifact's producing commit/tree is current before acting on the finding. Adds a §1.4 firing trigger and a paired anti-pattern §9.8 (stale-result-as-current); flagged as cheaply mechanizable per §8.1 (stamp artifacts with the producing commit, compare against canonical at consumption).
- PROTOCOL.md §4.10 - new subclass **SUBSUMPTION-CLAIM-BY-NAME**: a change scoped on "X already covers this" (or its inverse, "this needs a new X") settled by X's *name* rather than its *predicate*. Mitigation: read what X actually governs and test subsumption directly - conclude "covered" only on a genuine subset, create new only on a genuine non-subset. Self-referential (governs this catalog's own growth). Adds a §1.4 firing trigger and a paired anti-pattern §9.7 (subsumption-by-name).
- EVIDENCE.md / README.md / CONTRIBUTING.md - subclass count updated eight → ten.
- `examples/state-drift-catch-walkthrough.md` - new end-to-end **Class A (state-drift)** worked example, the full version of the README's opening anecdote (the dropped export-gate guard, already restored by a parallel commit). Previously only the Class B case had a walkthrough. Wired into the README docmap and the Class B walkthrough's cross-reference.

## v0.1 - 2026-05-26

Initial draft release. This is a scoping artifact graduated into repository shape, not a finished v1.0 methodology. See [EVIDENCE.md](./EVIDENCE.md) for the publication preconditions that gate v1.0.

- README.md - what / why / when-applies / when-doesn't / vs-alternatives / series context.
- PROTOCOL.md - formal specification (§§1-11): scope, the §B forensic check, the A/B/C drift taxonomy, the subclass catalog, the reframe obligation.
- EVIDENCE.md - the empirical basis (single-project, anecdotal) and what it does not prove.
- Templates: forensic-check-checklist, reframe-memo-template.
- Examples: a synthetic forensic-catch walkthrough.
- Docs: rationale, how-to-adopt, faq.
- Diagram: the catch → classify → reframe → route lattice (`diagram.svg`).
- Licensing: CC BY 4.0 for prose, MIT for templates.

### Known gaps at v0.1 (tracked toward v1.0)

- No published false-positive examples yet.
- Single-project evidence; no second-project pilot yet.
- Mechanization of the checkable subset (mtime/git-log/grep checks) not yet tooled.
