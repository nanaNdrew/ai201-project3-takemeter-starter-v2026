# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

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

---

## 1. Overall Accuracy

Overall accuracy on the held-out test split is at least **0.65** across all three seeds.

**Why this target:**
With three balanced classes, random chance is approximately 33.3%, and a simple majority baseline would yield around 35–45%. A target of 0.65 requires the model to correctly classify nearly two-thirds of held-out community posts despite the noise and conversational informality of `r/movies`, establishing a clear and defensible margin over naive guessing.

---

## 2. Per-Label Performance

Each individual label (`analysis`, `hot_take`, `reaction`) achieves an F1 score of at least **0.55** on the held-out test split across all three seeds.

**Why this target:**
With a 200-post dataset and a 15% test split (30 posts total), an individual class will only have about 8 to 12 evaluation examples per split. Because the boundary between nuanced `analysis` and unbacked `hot_take` is challenging, a floor of 0.55 guarantees that the model does not completely collapse or abandon predicting any single label while accounting for test-split variance across seeds.

---

## 3. Label Balance

No single label accounts for more than **50%** (100 posts) of the 200 collected posts, and each label represents at least **20%** (40 posts).

**Why this target:**
Severe class imbalance biases transformer fine-tuning toward majority-class frequency prediction, degrading minority recall. Constraining all three labels between 20% and 50% ensures that even the smallest class has at least 28 examples in the 70% training split (140 posts total), providing sufficient signal to learn distinguishing lexical features.

---

## 4. Consistency / Agreement

Raw agreement on the 30 shared calibration posts against the reference staff labels (evaluated with `agreement.py`) is at least **0.70** (at least 21 out of 30 matching).

**Why this target:**
Annotating forum commentary is inherently subjective, but an agreement rate below 70% signals that label definitions and boundary decision rules are too vague to yield a reliable training ground truth. A 70% match demonstrates that our written decision rule (identifying specific checkable facts vs. unsubstantiated assertions) is reproducible across different human annotators.

---

## 5. Confidence Calibration

In `results.json`, `accuracy_most_confident_third` is at least **0.15** higher than `accuracy_least_confident_third`.

**Why this target:**
A reliable classifier should know when it is unsure rather than outputting arbitrary high confidence across errors. Demanding a minimum 15-percentage-point accuracy advantage for the top-confidence third over the bottom-confidence third ensures that softmax prediction probabilities can be practically leveraged to auto-triage clear posts and route ambiguous cases to human review.



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
