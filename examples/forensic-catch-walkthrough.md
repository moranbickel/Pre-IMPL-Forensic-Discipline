# Worked Example: A Forensic Catch, End to End

A complete Pre-IMPL Forensic cycle on a small, realistic task. Synthetic — chosen to illustrate the mechanics rather than represent a real production catch. It walks a task from inherited framing, through the §B check, to a contradiction, a classification, a reframe memo with a receiver, and a corrected build that is *different* from what the task prescribed.

The setting: a documentation pipeline that renders Markdown source files into published HTML. There's a lint gate in the pipeline. A prior session left a backlog row.

---

## The inherited framing (T1)

A backlog row, authored two days before pickup:

> **DOC-214** — The em-dash characters in our source files are breaking the
> downstream PDF encoder (it expects ASCII). Extend the HTML lint gate
> (`gates/html_lint.py`) to flag em-dashes so they get caught before
> they reach the encoder. Should be a small change — the gate already
> walks every file.

Clear, specific, names the file, sounds small. The tempting move is to open `gates/html_lint.py`, add an em-dash rule, and ship. A careful engineer might even write a test first. Either way, the premise — *the gate walks the source files and is the right place to catch this* — goes unexamined.

---

## The §B forensic check (T2)

The picker-up runs the [checklist](../templates/forensic-check-checklist.md) before writing anything.

**§2.1 — Read the named artifact.** Open `gates/html_lint.py`. It exists. But the first thing the code does:

```python
def run(run_dir):
    html_files = glob(f"{run_dir}/rendered/*.html")   # <-- rendered output, not source
    for path in html_files:
        ...
```

The gate walks `rendered/*.html` — the *output* of the render step, not the Markdown source. The row said "the gate already walks every file," implying source files. It walks rendered HTML.

**§2.6 — Read the integration partner.** The row's real concern is the *PDF encoder.* Find it: `pipeline/pdf_encode.py`. Read where it reads from:

```python
def encode(run_dir):
    source = read(f"{run_dir}/source/{doc}.md")   # <-- reads SOURCE markdown directly
    ...
```

The encoder reads the **source Markdown directly** — it does not consume the rendered HTML at all. So a lint rule added to `html_lint.py` (which scans rendered HTML) would never see the characters the encoder chokes on (which live in source Markdown). The two artifacts the row conflates — the HTML lint gate and the PDF encoder — are on *different inputs.*

**§2.3 — mtime sanity.** `git log` on `pdf_encode.py`: last touched the morning the row was filed. The encoder's source-reading behavior isn't new drift; it was already true at T1. The row was *never* accurate about where the em-dashes needed catching.

The check stops here. Item §2.1 and §2.6 both came back `NO`. The premise is contradicted.

---

## The classification

This is **Class B — structural-misreference.** The framing named the wrong artifact: it pointed at the HTML lint gate as the place to catch em-dashes, but the gate operates on rendered HTML while the failure occurs on source Markdown that a *different* component (the encoder) reads directly. Adding the rule where the row said would have produced a gate that passes while the encoder keeps breaking — a fix that's technically correct in isolation and useless in context.

It's not Class A (nothing drifted — the encoder behaved this way at T1 too). It's not Class C (no cited source is mis-attributed). It's B: the artifact named in the framing isn't the artifact that owns the problem.

Note what caught it: **§2.6, reading the integration partner.** Item §2.1 alone (read the named gate) would have shown the gate scans rendered HTML — a hint something was off, but you might rationalize "I'll just make it also scan source." Reading the *encoder* is what proved the HTML gate is the wrong locus entirely, because the encoder doesn't touch HTML.

---

## The reframe memo

Filed before any code, per the [template](../templates/reframe-memo-template.md):

```
# REFRAME: DOC-214 — em-dash encoder break

DATE: 2026-05-26    CLASS: B    SUBCLASS: —

## 1. Original frame
DOC-214 says: extend gates/html_lint.py to flag em-dashes so they're caught
before the PDF encoder breaks. Premise: the HTML lint gate is the right place,
"it already walks every file."

## 2. Forensic finding
- gates/html_lint.py:2 globs rendered/*.html — it walks RENDERED OUTPUT, not source.
- pipeline/pdf_encode.py:2 reads source/<doc>.md DIRECTLY — it never consumes the
  rendered HTML.
- A lint rule in html_lint.py would scan HTML the encoder doesn't read; the em-dashes
  that break the encoder live in source Markdown the HTML gate never sees.
- git log: pdf_encode.py's source-reading behavior predates the row (not drift).
- The two artifacts the row conflates operate on different inputs.

## 3. Reframed scope
The em-dash check must run on SOURCE Markdown, on the encoder's input path —
either a pre-encode source-lint step, or inside pdf_encode.py before it reads.
Extending html_lint.py does NOT address the failure. Original prescription dropped.

## 4. Class
B — structural-misreference. Named the HTML lint gate; the problem lives on the
encoder's source-input path.

## 5. Confirmed receiver
Checked: there is a `pipeline/preflight/` stage that already validates source files
before render (verified: preflight/validate_source.py exists and runs first).
That stage owns "source-level checks before downstream consumption." DOC-214's real
scope belongs there. Re-pointing DOC-214 at pipeline/preflight/validate_source.py;
the html_lint.py extension is closed as misframed.
```

---

## The corrected build

The work that actually ships is an em-dash check in `pipeline/preflight/validate_source.py` — on the source Markdown, before render, on the path the encoder reads. It's a comparable amount of code to what the row imagined, but in a *different file*, against a *different input*, owned by a *different stage.* And `gates/html_lint.py` — the file the row named — is never touched.

The forensic cost about ten minutes (two file reads and a `git log`). It prevented shipping a lint rule in the wrong gate that would have passed clean while the encoder kept breaking — and the follow-up confusion of "we added the check the ticket asked for, why is it still broken?"

---

## What to notice

- **The premise was the bug, not the code.** Nothing was wrong with `html_lint.py`. What was wrong was the *assumption* that it was the right place. No amount of careful implementation against that assumption would have helped.
- **Item §2.6 did the real work.** Reading the *integration partner* (the encoder), not just the *named artifact* (the gate), is what proved the misreference. The gate read alone was ambiguous; the partner read was decisive.
- **The reframe changed what got built.** The point wasn't to annotate DOC-214 — it was to redirect the work to the right file. A reframe that left the build unchanged would have been theater.
- **The receiver was confirmed, not guessed.** "Belongs in preflight" was verified by checking that `preflight/validate_source.py` exists and owns source-level checks — not asserted. The correction lands somewhere durable.

---

## What this example does NOT show

- A **Class A** catch (state-drift) — the more common case, where the framing *was* right and a parallel commit moved the world. Cheaper to verify (`git log` + a read), same reframe shape. It has its own walkthrough: [`state-drift-catch-walkthrough.md`](./state-drift-catch-walkthrough.md).
- A **Class C** catch (doctrinal-mis-citation) — where a content-bearing task cites a source that doesn't support the claim. The most expensive class; the verification is looking the source up and reading it.
- A **skip** — a task where the check correctly does *not* fire (a typo fix, a freshly-verified premise). Knowing when not to run the check is half the discipline (see [PROTOCOL.md §1.5](../PROTOCOL.md)).

For the full class taxonomy and the subclass catalog, see [PROTOCOL.md §3–§4](../PROTOCOL.md).
