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
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

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

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



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
