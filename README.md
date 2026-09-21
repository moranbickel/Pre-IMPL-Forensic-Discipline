# Pre-IMPL Forensic Discipline

Check whether a task's premise is still true before implementing it.
**Status: v0.1 draft.** The [evidence](EVIDENCE.md) describes one author's
experience on one project; broader effectiveness has not been established.

## Try the failure case

Synthetic task: "The port parser accepts zero. Add a lower-bound check."
The current parser already rejects zero. Implementing the note without reading
the code would repeat completed work.

Run the [already-fixed crash test](https://github.com/moranbickel/agent-crash-tests/tree/main/cases/already-fixed):

```text
git clone https://github.com/moranbickel/agent-crash-tests
cd agent-crash-tests
node bin/crash-tests.js demo already-fixed
```

Requires Node.js 22 or newer. The demo uses authored responses and synthetic
files; it does not call or evaluate a model. A paired control has the bug still
present, so blanket refusal is not the right answer either.

## Use the checklist

Read the [full checklist](templates/forensic-check-checklist.md). At pickup:

1. Open the current file named in the task.
2. Reproduce the claimed problem, where possible.
3. Check the relevant history and integration points.
4. Verify that cited sources support the premise.
5. If the premise is wrong, record the finding and correct the scope before work.

Use the [reframe template](templates/reframe-memo-template.md) to record the
original request, the contradicting evidence, and the revised next step.

## Read further

- [Protocol](PROTOCOL.md): complete checklist and categories of mistaken premises.
- [State-drift example](examples/state-drift-catch-walkthrough.md) and
  [wrong-artifact example](examples/forensic-catch-walkthrough.md): synthetic walkthroughs.
- [Adoption guide](docs/how-to-adopt.md), [FAQ](docs/faq.md), and [rationale](docs/rationale.md).
- [Contributing](CONTRIBUTING.md): reports from a second project are particularly useful.

This adds an inspection step. It cannot guarantee that every mistaken assumption
will be found. [Russian-Judge](https://github.com/moranbickel/Russian-Judge) addresses
review of completed work; [Three-Body-Protocol](https://github.com/moranbickel/Three-Body-Protocol)
provides the handoff templates whose contents still need checking.

Maintained by [Moran Bickel](https://github.com/moranbickel).
Prose: [CC BY 4.0](LICENSE-CC-BY-4.0). Templates and code: [MIT](LICENSE-MIT).
