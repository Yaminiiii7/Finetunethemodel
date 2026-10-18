# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

## How F1 and macro-F1 are calculated

For one label, count the following on the held-out set:

- **True positives (TP):** posts whose true label is this label and that the
  model correctly predicts as this label.
- **False positives (FP):** posts whose true label is something else but that
  the model predicts as this label.
- **False negatives (FN):** posts whose true label is this label but that the
  model predicts as something else.

First calculate precision and recall for that label:

```text
precision = TP / (TP + FP)
recall    = TP / (TP + FN)
```

Precision answers, “Of the posts predicted as this label, how many really
belong to it?” Recall answers, “Of the posts that really belong to this label,
how many did the model find?”

The label's F1 score is the harmonic mean of its precision and recall:

```text
F1 = 2 × (precision × recall) / (precision + recall)
```

Equivalently, when the denominator is nonzero:

```text
F1 = 2 × TP / (2 × TP + FP + FN)
```

F1 ranges from 0 to 1. It is high only when both precision and recall are
high. If there are no predicted or actual examples for a label, the score
implementation must define how to handle the zero denominator; this project's
metric code uses `zero_division=0`.

For this three-label classifier, calculate one F1 score for each label, then
take their unweighted arithmetic mean:

```text
macro-F1 = (F1_analysis + F1_hot_take + F1_reaction) / 3
```

“Unweighted” means each label contributes equally, regardless of how many
examples it has. Macro-F1 can therefore reveal weak performance on a smaller
label that overall accuracy might obscure.

---

## 1.

The model achieves at least **0.60 accuracy** on the held-out set for each of the three seeds.

**Why this target:** With three labels, random guessing would achieve about 0.33 accuracy. A 0.60 target sets a higher bar while recognizing that these categories can be difficult to distinguish.

---

## 2.

The model achieves a **macro-F1 of at least 0.60** on the held-out set for each of the three seeds.

**Why this target:** Macro-F1 weighs `analysis`, `hot_take`, and `reaction` equally, so strong results on one label cannot hide weak performance on another.

---

## 3.

Each label has an **F1 score of at least 0.50** on the held-out set for each of the three seeds.

**Why this target:** The held-out set contains relatively few examples per label, so individual scores may vary. The 0.50 minimum still requires useful performance for every label.

---

## 4.

On a sample of 30 posts labeled independently by a second person, my labels agree with theirs on at least **24 posts (80%)**.

**Why this target:** The categories distinguish evidence-backed arguments, unsupported claims, and immediate reactions. Agreement on 80% of a sample would indicate that the definitions are clear enough to apply consistently.

---

## 5.

The difference between the highest and lowest held-out accuracy across the three seeds is no more than **0.10**.

**Why this target:** I want the model's performance to be reasonably stable across runs, rather than depending on one particularly favorable random seed.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
