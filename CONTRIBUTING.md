# Contributing

Issues and discussions are open. Because the evidence base is currently single-project, real-use-case reports from *other* projects are the highest-value input - they are the precondition for promoting this from a v0.1 draft to a v1.0 methodology.

## What's most helpful

- **Second-project adoption reports.** "I applied this in a different codebase / different agent stack; here's a catch it produced and here's one it missed." Cross-project evidence is exactly what this draft lacks.
- **New subclasses.** If a drift you caught doesn't fit the ten subclasses cleanly, that's signal. Name it, describe the T1 framing, the T2 reality, and the verification step that caught it.
- **Counter-evidence.** Cases where the discipline fired and cost more than it saved - net-negative catches. The fire/skip cost asymmetry (PROTOCOL §6) is a claim, not a law; falsifying instances sharpen it.
- **Mechanization.** The checkable subset (file-mtime-vs-claim, git-log-vs-state-claim, grep-for-siblings) is tool-able. Working tooling, or a sketch of it, is welcome.

## What's less helpful

- Style edits to README, PROTOCOL.md, EVIDENCE.md, or docs. The voice is intentional.
- New subclasses proposed without a worked instance behind them. The catalog earns entries empirically, not by enumeration.
- Pull requests that change the A/B/C taxonomy or the §B checklist item set. Those are versioned changes - open an issue first.

## How to propose changes

- **Typos and minor clarifications:** open a PR directly.
- **Substantive changes:** open an issue first to discuss before writing the PR.
- **Adaptations for your own use:** fork. The MIT license on templates and CC BY 4.0 on prose are designed for this.

## Versioning

Per [PROTOCOL.md §11](./PROTOCOL.md):

- Material changes to the A/B/C taxonomy, the §B checklist items, or the reframe obligation → v2.0.
- Refinements to the subclass catalog, the fire/skip heuristics, or anti-patterns → v0.2, v0.3, … then v1.0 once the publication preconditions in [EVIDENCE.md](./EVIDENCE.md) are met.

See [CHANGELOG.md](./CHANGELOG.md) for the version history.
