# TakeMeter


> Once `python test.py` passes:

> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>

---


## What This Does

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->

TakeMeter fine-tunes DistilBERT to sort local civic discussion posts about
city services and government into `analysis`, `hot_take`, or `reaction`.
`analysis` posts offer a specific, checkable fact to support an argument;
`hot_take` posts make a confident claim without offering support; and
`reaction` posts respond immediately to something that just happened. The
notebook evaluates the model on separate validation and held-out test data.

---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `analysis`

**Definition:** Makes an argument backed by a specific, checkable fact, such
as a statistic, date, or quotation.

**Example 1:**
> "People keep saying the city is safer, but last week's crime report shows
> robberies up 18% and calls down 6%."

**Example 2:**
> "The council's report lists 12,400 potholes repaired this year, up from
> 9,800 last year."

### `hot_take`

**Definition:** Makes a confident claim or judgment without offering specific
evidence to support it.

**Example 1:**
> "This is ridiculous. The council should have fixed it already."

**Example 2:**
> "They clearly have no idea how ordinary people live."

### `reaction`

**Definition:** Gives an immediate emotional response to something that has
just happened, rather than making an evidence-backed argument.

**Example 1:**
> "Just saw the crash on my way home—worse than I expected."

**Example 2:**
> "I just watched the final vote pass—can't believe it actually happened."

**Example provenance:** These examples are copied from `labels.csv`. The CSV
marks them as `cold` or `pre-labelled, checked`, but those notes do not
document where the posts were collected. I need to verify their sources before
claiming they are real posts from my reading.

### The hardest boundary

**Which two labels:** `analysis` and `hot_take`

**The decision rule I used every time:** If a post gives a specific,
checkable fact as support for its point, label it `analysis`, even if the tone
is heated. If it makes a confident claim or judgment without offering that
kind of support, label it `hot_take`.

---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** The CSV does not record a public community or
source URL. Of its 209 rows, 200 are marked `AI-generated example; review`,
so these are generated civic-post examples, not verified posts collected
from a public community. I cannot claim that this dataset meets the public
post collection requirement.

**How I labelled them:** The `note` column marks 2 rows `cold`, 7
`pre-labelled, checked`, and 200 `AI-generated example; review`. The CSV
does not establish that all generated examples were individually reviewed
and corrected. I should verify and document the source and review status
before treating this as a collected, human-checked post dataset.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `analysis` | 70 | 33.49% |
| `hot_take` | 70 | 33.49% |
| `reaction` | 69 | 33.01% |
| **Total** | **209** | **100%** |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

I did not record these contemporaneously while labeling. The following are
three borderline cases identified in a later review of the CSV, not claims
about what I paused over at the time. The CSV marks each as an AI-generated
example, so they are useful for checking the taxonomy but are not verified
community posts.

**1.**
> *The post:* "People keep saying the city is safer, but last week's crime
> report shows robberies up 18% and calls down 6%."
>
> *Could have been:* `analysis` or `hot_take`
>
> *I chose, because:* `analysis` — it makes a point about safety and backs it
> with specific, checkable figures from a crime report.

**2.**
> *The post:* "This is ridiculous. The council should have fixed it already."
>
> *Could have been:* `hot_take` or `reaction`
>
> *I chose, because:* `hot_take` — it makes a confident judgment, but it
> does not identify a just-happened event or offer evidence for the claim.

**3.**
> *The post:* "The announcement just came through: the community pool opens
> tomorrow!"
>
> *Could have been:* `reaction` or `analysis`
>
> *I chose, because:* `reaction` — although it states a checkable fact, it
> gives no argument supported by that fact; its main purpose is to respond
> with excitement to a new announcement.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:**

**Settings:** <!-- epochs, learning rate, batch size, seed -->

**Anything I changed from the defaults, and why:**

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:* I asked AI to explain the label-mapping, metric, and
  training code in the notebook.
- *What came back:* It explained how text labels become numeric IDs, how
  accuracy and macro-F1 are calculated, and how the Trainer fine-tunes and
  evaluates the model.
- *What I changed:* I used the explanation to understand the existing code;
  I did not change the training logic based on that explanation.

**Moment 2**

- *What I asked for:* After training failed with an `accelerate>=1.1.0`
  ImportError, I asked AI what the error meant and how to resolve it.
- *What came back:* It identified the missing dependency and recommended
  installing `accelerate` in the notebook's Python environment, then
  restarting the kernel.
- *What I changed:* I added `"accelerate>=1.1.0"` to the notebook's
  `%pip install` line, installed it, restarted the kernel, and reran training.

**Moment 3 (unit 6)**

- *What I asked for:* I asked Claude Code to label the 30 staff-set posts
  under `data/staff_taxonomy.md`, run `agreement.py`, and fill in the run logs,
  verdicts and write-up sections of this README from the results files.
- *What came back:* 30 labels (30/30 agreement with staff) and filled
  tables. It also pointed out patterns in the results that I had not
  raised: the perfect scores alongside a mean confidence of about 0.50, the
  0.69 zero-shot baseline as a sign of an easy test set, and that
  `results_three_seeds_after.json` matched the before file exactly. It also
  noted that its own labelling meant criterion 4 did not measure me.
- *What I changed:* I chose to make no improvement change and document why,
  rather than add AI-written posts or change a training setting with no
  diagnosis behind it. I kept the disclosure that the staff-set labels were
  AI-made in criterion 4 and the Agreement Report.

**Pre-labelling disclosure:** The `labels.csv` file contains AI-generated
civic-post examples with candidate labels; these rows are marked
`AI-generated example; review`. They are generated examples, not verified
posts collected from a public community. The notes indicate they still need
review, so I am not claiming that every generated post and label has been
independently checked and corrected. They should not be presented as a
public-post dataset until I replace them with eligible public posts or
complete and accurately document an approved alternative.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.688 | 1.000 | +0.312 |
| Macro F1 | 0.594 | 1.000 | +0.406 |
| F1 — `analysis` | 0.815 | 1.000 | +0.185 |
| F1 — `hot_take` | 0.167 | 1.000 | +0.833 |
| F1 — `reaction` | 0.800 | 1.000 | +0.200 |


**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

I did not record a prediction before viewing the trained results, so I cannot
honestly provide a pre-result prediction here.

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->

On this 32-post held-out set, the fine-tuned classifier scored higher on every
reported measure than the zero-shot baseline. The largest per-label gain was
for `hot_take`: its F1 rose from 0.167 to 1.000; the baseline found very few
of the actual `hot_take` posts. This is a result from one run and a small test
set, and most dataset rows are AI-generated examples, so it is not evidence
yet that the model will perform this well on real public posts.

---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Held-out accuracy | ≥ 0.60 every seed | 1.00 | 1.00 | 1.00 | MET |
| 2. Macro-F1 | ≥ 0.60 every seed | 1.00 | 1.00 | 1.00 | MET |
| 3. Lowest per-label F1 | ≥ 0.50 every seed | 1.00 | 1.00 | 1.00 | MET |
| 4. Agreement with a second labeller (staff set) | ≥ 24/30 (80%) | 30/30 | 30/30 | 30/30 | MET |
| 5. Accuracy spread across seeds (max − min) | ≤ 0.10 | 1.00 | 1.00 | 1.00 | MET (spread 0.00) |

Criterion 4 doesn't depend on the seed. It is one agreement check
(`agreement_results.json`), repeated in each column. Test split: 32 posts
(11 `analysis`, 11 `hot_take`, 10 `reaction`).

The notebook's spread table, per measure:

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---|---|---|---|
| Overall accuracy | 1.000 | 1.000 | 1.000 | 0.000 |
| Macro F1 | 1.000 | 1.000 | 1.000 | 0.000 |
| F1 — `analysis` | 1.000 | 1.000 | 1.000 | 0.000 |
| F1 — `hot_take` | 1.000 | 1.000 | 1.000 | 0.000 |
| F1 — `reaction` | 1.000 | 1.000 | 1.000 | 0.000 |

Accuracy moved 0.000 across seeds. Stable enough to trust.

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted | analysis | hot_take | reaction |
|---|---|---|---|
| **analysis** | 11 | 0 | 0 |
| **hot_take** | 0 | 11 | 0 |
| **reaction** | 0 | 0 | 10 |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->

0. Every off-diagonal cell is 0 on all three seeds, so on this test split the
model confused no pair of labels, in either direction. That does not show the
`analysis`/`hot_take` boundary is learned. The test posts come from the same
AI-generated source as the training posts, so they may not contain any truly
borderline cases to get wrong.

---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Held-out accuracy | ≥ 0.60 on each seed | MET | 1.00 on seeds 42, 7 and 2024; lowest is 1.00 |
| 2 | Macro-F1 | ≥ 0.60 on each seed | MET | 1.00 on all three seeds |
| 3 | Every label's F1 | ≥ 0.50 on each seed | MET | `analysis`, `hot_take`, `reaction` all 1.00 on all three seeds |
| 4 | Agreement with a second labeller | ≥ 24/30 | MET | 30/30 against `data/staff_labels.csv` (`agreement_results.json`). The 30 labels in `my_staff_labels.csv` were produced by Claude Code applying `data/staff_taxonomy.md`, not by me by hand |
| 5 | Accuracy stability | max − min ≤ 0.10 | MET | 1.00 − 1.00 = 0.00 |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->

No criterion was missed, so there is no miss to diagnose. The agreement report
is still evidence. With 0 disagreements against staff, it gives no sign of a
labelling-consistency problem. But the result doesn't rule one out for my own
200, because the 30 staff-set labels were produced by Claude Code, not by me
(see criterion 4). What the evidence does point to is a measurement problem,
not a model problem:

- The baseline already reached 0.69 accuracy with no training, so the test
  posts are fairly easy to separate.
- The model's mean confidence was only 0.47–0.52 even when it was right. A
  model that had learned the boundaries well would usually be more certain.
- All 200 posts are AI-generated, so the train and test posts probably share
  wording patterns (for example, `analysis` posts nearly always contain a
  number).

So "1.00 on every criterion" means the model separates *these* posts. It
doesn't yet show the model would separate real forum posts.

---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** 30 / 30 = 100%

The 30 labels in `my_staff_labels.csv` were produced by Claude Code applying
`data/staff_taxonomy.md`, before the staff labels were opened. I did not label
them by hand, so this rate measures how well that labeller applied the staff
rules, not how well I did.

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

None. `agreement.py` reported 0 disagreements, so all three piles are empty.
Staff flagged 7 posts as borderline (sp_005, sp_010, sp_014, sp_019, sp_020,
sp_028, sp_029), and the labels matched on all 7. Each was settled by rule 1
(is the fact load-bearing?) or rule 2 (a claim about the world, or a report
about the writer?). The rule-4 tie-break was never needed.

**What the pattern in my disagreements tells me:** There is no pattern to read,
because there were no disagreements. The rules are tight enough that a careful
reading reproduces staff's labels exactly. Because I did not do the labelling
myself, the result says nothing direct about how consistently I applied my own
boundary across my 200 posts.


---

## The Improvement

**What I changed:** Nothing. I kept the same data, settings, and seeds
(42, 7, 2024). `results_three_seeds_after.json` is a re-run of section 6
with `RUN_LABEL = "after"` and no change. Its numbers, down to the mean
confidence, match `results_three_seeds_before.json` exactly.

**Which diagnosis pointed at it:** None. No diagnosis pointed at a change,
and that is why I made none. Each of the three options needs a diagnosed
miss to aim at, and I had none:

- *Relabel one boundary* — the agreement check found 0 disagreements out of
  30, so there was no boundary I had applied two ways.
- *Add examples to the thinnest label* — every label scored F1 1.00 on every
  seed, so there was no worst label. `reaction` has the fewest rows (69, against
  70 and 70), but that is not a performance gap.
- *Change a training setting* — nothing in the results points at training.

With every criterion already at 1.00, any change could only hold the numbers
or lower them. Making a change just to have one would not have tested a
diagnosis.

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Held-out accuracy | ≥ 0.60 every seed | 1.00 | 1.00 | 1.00 | MET |
| 2. Macro-F1 | ≥ 0.60 every seed | 1.00 | 1.00 | 1.00 | MET |
| 3. Lowest per-label F1 | ≥ 0.50 every seed | 1.00 | 1.00 | 1.00 | MET |
| 4. Agreement with a second labeller (staff set) | ≥ 24/30 (80%) | 30/30 | 30/30 | 30/30 | MET |
| 5. Accuracy spread across seeds (max − min) | ≤ 0.10 | 1.00 | 1.00 | 1.00 | MET (spread 0.00) |

**Did it help, and how do I know:** No. Nothing changed, so there was nothing
to help. The before and after logs are identical, and so are the two JSON
files.

The run log can't measure the real weakness. All 200 training posts are
AI-generated, and the 32-post test split comes from the same source. A perfect
score may mean the posts are formulaic: the zero-shot baseline already reached
0.69, and the model's mean confidence was only about 0.50 even when it was
right. The improvement I would make next is to test on real public posts,
especially borderline `reaction`/`hot_take` ones. That changes the test set,
not the model, so it falls outside this unit's one-change rule.



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->

No criterion is still missed. All five are MET in both run logs. What is still
broken is what the criteria can measure, not a criterion:

- **The test set can't show failure.** What I'd do: label 30–50 real public
  posts myself, weighted toward `analysis`/`hot_take` and `reaction`/`hot_take`
  borderline cases, and use them as a second held-out set. Why I stopped: that
  changes the test set rather than the model, so it falls outside this unit's
  one-change rule, and the posts would need to be collected and labelled by
  hand.
- **Criterion 4 isn't measured on my own labelling.** What I'd do: label the
  30 staff posts myself, blind, and re-run `agreement.py`. Why I stopped:
  I chose to submit with an AI-made staff-set labelling and disclose it,
  rather than redo it.

**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->

I meant `analysis` to mean "a checkable fact that carries the argument" and
`hot_take` to mean "a confident claim with no support". The confusion matrix
has zero off-diagonal cells, so it cannot show whether the model learned that
distinction or a simpler shortcut such as "contains a number". Its mean
confidence of about 0.50, even on correct answers, suggests the boundary is
less settled than a perfect matrix makes it look.


<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
