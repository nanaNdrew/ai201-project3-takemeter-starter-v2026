# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

This project classifies public movie discussion posts from the `r/movies` community into distinct categories based on substance, evidence, and communicative tone. Posts on `r/movies` range from detailed filmmaking critiques to impulsive emotional outbursts and unhedged hot takes. The classifier sorts these posts into three functional categories: `analysis` (arguments backed by specific scenes, craft techniques, or checkable facts), `hot_take` (bold, unhedged claims or predictions with no supporting evidence), and `reaction` (immediate affective or emotional responses to viewing a movie or trailer).

### Candidate Distinctions Observed (from reading posts):
1. **Specific craft / scene citation vs. general assertion**: Some posts specifically cite scenes, director choices, cinematography, or box office statistics; others offer sweeping claims without citing any specific element of the work.
2. **Unhedged superlative claims without justification**: Posts stating definitive value judgments ("this is the worst film of the decade", "unwatchable", "instant masterpiece") with zero supporting rationale.
3. **First-person affective response vs. critique of the work**: Posts describing the viewer's immediate physical or emotional state ("I was crying the entire third act", "got chills when the score dropped") rather than evaluating the craftsmanship of the film.

---

## Label Taxonomy

### `analysis`

**Definition:** Makes an argument about a film's quality, craft, or commercial performance backed by a specific, checkable fact (such as a scene reference, technical technique, box office figure, or quote).

**Example 1:**
> The long unbroken take during the stairway shootout in John Wick 4 works because the camera slowly pulls up to an overhead god's-eye perspective, letting the audience track the spatial geography and muzzle flashes without rapid cuts.

**Example 2:**
> Furiosa underperformed at the domestic box office not because of reception, but because its $168M production budget required a $350M+ break-even point in an R-rated Memorial Day window where overall theatrical attendance was down 30% from 2019.

### `hot_take`

**Definition:** A confident, sweeping claim or prediction about a movie, franchise, or filmmaker with no supporting evidence or justification offered.

**Example 1:**
> Christopher Nolan has completely forgotten how to write compelling human dialogue and Oppenheimer is going to age worse than Tenet.

**Example 2:**
> CGI peaked in 2006 with Pirates of the Caribbean: Dead Man's Chest and every single blockbuster made since looks like a cheap video game cutscene.

### `reaction`

**Definition:** An immediate, visceral response describing personal emotional feelings, physical sensations, or mood from seeing a film or trailer.

**Example 1:**
> Just walked out of the theater after seeing Dune 2 and my hands are literally shaking, that sound design in IMAX had my entire chest vibrating for two hours.

**Example 2:**
> I was crying like a baby during the last ten minutes of The Wild Robot, wasn't expecting an animated movie to hit me in the feelings this hard.

### The hardest boundary

**Which two labels:** `analysis` and `hot_take`

**The decision rule I used every time:**
If the post points to a specific, checkable element of the film or its production (such as a named scene, a technical filmmaking choice, a dialogue quote, or a box office statistic) to substantiate its claim, it is `analysis`—even if the tone is aggressive, hyperbolic, or opinionated. If it only asserts a judgment or prediction without pointing to any verifiable detail or scene, it is `hot_take`.



---

## The Dataset

**Where the posts came from:**
Public discussion threads, post-viewing reactions, and film analysis commentary from `r/movies`.

**How I labelled them:**
I labelled the first 20 posts cold without external assistance or tools to calibrate taxonomy boundaries. The remaining 180 posts were pre-labelled with AI assistance based on the strict taxonomy definitions and boundary decision rules, followed by individual manual inspection, review, and corrections.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `analysis` | 70 | 35.0% |
| `hot_take` | 68 | 34.0% |
| `reaction` | 62 | 31.0% |
| **Total** | 200 | 100% |

**Three hard cases**

**1.**
> *The post:* "The second act completely grinds to a halt once they reach the farmhouse because there's 35 straight minutes of exposition before any conflict resumes."
>
> *Could have been:* `hot_take` or `analysis`
>
> *I chose, because:* Chose `analysis` because even though the qualitative remark "grinds to a halt" sounds like an unhedged opinion, the poster grounds their critique in a specific, checkable narrative structure (35 minutes of exposition at the farmhouse setting).

**2.**
> *The post:* "That ending of The Mist left me staring at the screen in pure shock, my jaw was literally on the floor for ten minutes."
>
> *Could have been:* `hot_take` or `reaction`
>
> *I chose, because:* Chose `reaction` because the core of the post describes the viewer's physical shock ("jaw was literally on the floor") and lingering emotional disorientation rather than an argument about cinematic quality.

**3.**
> *The post:* "I got absolute goosebumps when the brass section kicked in during the Star Wars main title crawl."
>
> *Could have been:* `analysis` or `reaction`
>
> *I chose, because:* Chose `reaction` because although it names a concrete musical element (brass section in main crawl), the communicative function is an immediate physical sensation ("got absolute goosebumps") rather than music theory or thematic analysis.

---

## The Training Run

**Base model:** `distilbert-base-uncased`

**Settings:** 3 epochs, learning rate 2e-5, per-device batch size 16, max token length 128, seed 42.

**Anything I changed from the defaults, and why:**
I kept the recommended defaults. A learning rate of 2e-5 and batch size of 16 are well-tested hyperparameter defaults for fine-tuning DistilBERT on smaller text classification datasets, preventing catastrophic forgetting while converging smoothly across 3 epochs. The 128 max token length comfortably accommodates full Reddit posts without incurring unnecessary compute or memory overhead.

**Split sizes:**
- **Train:** 140 posts (70%) — `analysis`: 49, `hot_take`: 48, `reaction`: 43
- **Validation:** 30 posts (15%) — `analysis`: 10, `hot_take`: 10, `reaction`: 10
- **Test:** 30 posts (15%) — `analysis`: 11, `hot_take`: 10, `reaction`: 9

All classes in the test split have at least 9 examples, clearing the 8-example threshold without any thin classes.

---

## How I Used AI

**Moment 1**

- *What I asked for:* I asked an AI model to generate 8 edge-case posts that sit directly on the boundaries between `analysis` and `hot_take`, and between `hot_take` and `reaction`.
- *What came back:* It produced posts that used aggressive, opinionated slang alongside specific factual scene details (e.g., claiming a movie "grinds to a halt" but citing "35 minutes of exposition at the farmhouse").
- *What I changed:* Seeing these edge cases revealed that relying on tone (calm vs. heated) would lead to inconsistent labeling. I tightened my hardest boundary rule to state that if checkable, specific evidence or scene details are cited, the post is `analysis` regardless of how heated or hyperbolic the tone is.

**Moment 2**

- *What I asked for:* I supplied my finalized taxonomy definitions and asked an AI tool to generate a pre-labelled candidate batch for the remaining 180 posts.
- *What came back:* The model occasionally misclassified purely visceral emotional posts that happened to name an actor, cue, or setting (e.g. "got goosebumps during the brass fanfare in the opening crawl") as `analysis`.
- *What I changed:* I manually reviewed and corrected those instances to `reaction`, establishing that merely referencing a soundtrack instrument or actor's name does not make a visceral, emotional reaction an analytical argument.

**Pre-labelling disclosure:**
The first 20 rows of `labels.csv` were labelled completely cold and unaided (marked `cold` in the `note` column). The remaining 180 rows were pre-labelled with AI assistance using the written definitions and decision rules, followed by full manual inspection, verification, and correction of every single row (marked `pre-labelled, checked` in the `note` column).

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.667 | 1.000 | +0.333 |
| Macro F1 | 0.599 | 1.000 | +0.401 |
| F1 — `analysis` | 0.667 | 1.000 | +0.333 |
| F1 — `hot_take` | 0.182 | 1.000 | +0.818 |
| F1 — `reaction` | 0.947 | 1.000 | +0.053 |

**What I predicted before I looked:**
I predicted that the fine-tuned model would beat the zero-shot baseline most dramatically on `hot_take` and `analysis`, because zero-shot language models typically struggle to distinguish confident unhedged assertions from factually substantiated critiques without explicit training on our boundary rule requiring checkable scene citations.

**What the gap actually means:**
Fine-tuning delivered a substantial performance improvement over zero-shot prompting (+0.333 overall accuracy and +0.401 macro F1). The zero-shot baseline performed well on `reaction` (0.947 F1) because personal emotional and physical affective vocabulary is readily identifiable. However, it struggled severely on `hot_take` (0.182 F1, 0.100 recall), almost always classifying bold, confident claims as `analysis` because both categories share film critique jargon. Fine-tuning on 140 domain-specific examples taught the model our precise boundary rule: requiring checkable scene facts or technical details to qualify as `analysis`.

*Definitions ablation:* Running the baseline with bare label names yielded 0.567 accuracy and 0.524 macro F1 (`analysis` F1 collapsed to 0.167). Providing clear label definitions in `label_definitions.txt` increased baseline accuracy by +10.0 percentage points (to 0.667) and boosted `analysis` F1 from 0.167 to 0.667, demonstrating that definition prompt engineering significantly clarifies label scope, even though fine-tuning is still necessary to master the subtle boundary.



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | >= 0.65 | 1.000 | 1.000 | 0.967 | MET |
| 2. Per-label F1 floor | >= 0.55 | 1.000 | 1.000 | 0.947 | MET |
| 3. Label balance (max/min) | 20% - 50% | 35% / 31% | 35% / 31% | 35% / 31% | MET |
| 4. Calibration agreement | >= 0.70 | (eval in M4) | (eval in M4) | (eval in M4) | PENDING |
| 5. Confidence separation | >= 0.15 | 0.000 | 0.000 | 0.100 | MISSED |

### Confusion matrix

| true \ predicted | analysis | hot_take | reaction |
|---|---|---|---|
| **analysis** | 11 | 0 | 0 |
| **hot_take** | 0 | 10 | 0 |
| **reaction** | 0 | 0 | 9 |

**My biggest off-diagonal number, and what it means:**
In Seed 42's test split the model achieved 0 off-diagonal mistakes; across all 90 seed evaluations, the single off-diagonal error was 1 real `analysis` post misclassified as `reaction` in Seed 2024 (and 0 the other way), which occurred because the post analyzed emotional audience reception while citing scene details, causing the model to lean slightly toward the affective label.



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Overall Accuracy | >= 0.65 | MET | Scored 1.000 (Seed 42), 1.000 (Seed 7), and 0.967 (Seed 2024); all three seeds exceeded 0.65. |
| 2 | Per-Label Performance | >= 0.55 | MET | Lowest F1 across all seeds was 0.947 (reaction on Seed 2024); all others were 0.952 to 1.000. |
| 3 | Label Balance | 20% - 50% | MET | Labels are balanced: analysis 35.0% (70), hot_take 34.0% (68), reaction 31.0% (62). |
| 4 | Calibration Agreement | >= 0.70 | PENDING | To be evaluated against reference staff labels upon release in Milestone 4. |
| 5 | Confidence Calibration | >= 0.15 | MISSED | Separation was 0.000 on Seed 42, 0.000 on Seed 7, and 0.100 on Seed 2024, missing the 0.15 target. |

**Diagnoses**

- **Criterion 5 (Confidence Separation) — MISSED:**
  The target of a 0.15 separation between the most-confident third and least-confident third was missed due to a ceiling effect on model accuracy. Because the fine-tuned model achieved 100% accuracy on Seeds 42 and 7 and 96.7% on Seed 2024, even the least-confident third achieved 90% to 100% accuracy on held-out test splits. A 0.15 advantage is mathematically impossible when least-confident accuracy is 0.90 or 1.00 (since the ceiling is 1.00). When errors did occur (Seed 2024), the model was genuinely less confident (mean probability 0.364 when wrong vs. 0.441 when right), confirming directional calibration, but the high performance compressed the spread.



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
