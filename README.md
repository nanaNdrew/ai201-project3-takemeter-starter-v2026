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

**Device:** `mps` (Apple Silicon GPU — MPS) | **PyTorch:** 2.14.1

### Spread across three seeds

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---|---|---|---|
| Overall accuracy | 1.000 | 1.000 | 0.967 | 0.033 |
| Macro F1 | 1.000 | 1.000 | 0.967 | 0.033 |
| F1 — `analysis` | 1.000 | 1.000 | 0.952 | 0.048 |
| F1 — `hot_take` | 1.000 | 1.000 | 1.000 | 0.000 |
| F1 — `reaction` | 1.000 | 1.000 | 0.947 | 0.053 |

*Accuracy moved 0.033 across seeds. Stable enough to trust.*

### Criteria tracking

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | >= 0.65 | 1.000 | 1.000 | 0.967 | MET |
| 2. Per-label F1 floor | >= 0.55 | 1.000 | 1.000 | 0.947 | MET |
| 3. Label balance (max/min) | 20% - 50% | 35% / 31% | 35% / 31% | 35% / 31% | MET |
| 4. Calibration agreement | >= 0.70 | 1.000 (30/30) | 1.000 (30/30) | 1.000 (30/30) | MET |
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
| 4 | Calibration Agreement | >= 0.70 | MET | Achieved 1.000 (30/30 matching, 100%) against staff reference labels using agreement.py. |
| 5 | Confidence Calibration | >= 0.15 | MISSED | Separation was 0.000 on Seed 42, 0.000 on Seed 7, and 0.100 on Seed 2024, missing the 0.15 target. |

**Diagnoses**

- **Criterion 5 (Confidence Separation) — MISSED:**
  - *Observed:* Separation was 0.000 on Seed 42 (1.000 vs 1.000), 0.000 on Seed 7 (1.000 vs 1.000), and 0.100 on Seed 2024 (1.000 vs 0.900), failing to reach the >= 0.15 threshold across all trials.
  - *Evidence from Agreement Report:* In Milestone 4, the agreement check achieved 100% agreement (30/30) against the staff reference set. This confirms that the taxonomy definitions and decision rules (particularly Rule 1 on load-bearing facts and Rule 2 on general claims vs. personal reports) were applied consistently without labeling drift or ground-truth noise. The miss is therefore a property of the model's prediction distribution, not a labeling inconsistency.
  - *Evidence from Confusion Matrix:* Across all 90 evaluations across the three seeds (30 posts per seed), the model made 89 correct predictions and only a single mistake (1 real `analysis` post predicted as `reaction` on Seed 2024).
  - *Root Cause Diagnosis:* The failure to achieve a 0.15 separation is an accuracy ceiling effect. Because the fine-tuned model achieved 96.7% to 100% overall accuracy, even its least-confident third answered 90% to 100% of held-out examples correctly. A 0.15 margin is mathematically impossible when the lower third scores 0.90 or 1.00 (since top-third accuracy cannot exceed 1.00). Directionally, the model's confidence was well-calibrated—on Seed 2024, its mean confidence when wrong (0.364) was lower than when right (0.441)—but the near-absence of misclassifications compressed the accuracy spread between the thirds.



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** 30 / 30 = 100%

*I matched staff on every post (30/30). The 100% agreement reflects that the decision rules in `data/staff_taxonomy.md`—particularly Rule 1 (load-bearing fact vs. decoration), Rule 2 (general claim vs. writer's personal experience), and Rule 3 (recency vs. evidence)—were applied strictly rather than relying on intuitive impressions. Several posts in the set were specifically designed as edge cases.*

**Adjudication of Hardest Edge Cases**

**1.**
> *The post:* "Times are down about 3% since the shoe rules changed, so obviously the shoes are the whole story and the sport has a problem." (sp_014)
>
> *Staff said / I said:* `hot_take` / `hot_take` (Agreed)
>
> *My call, and why:* The post mentions a statistic ("3%") and an event ("shoe rules changed"), which superficial keyword matching might call `analysis`. However, Rule 1 explicitly dictates that a fact must carry the argument, not decorate it. The writer uses the number as a flourish to jump to an unhedged sweeping assertion ("obviously the shoes are the whole story and the sport has a problem"). The fact is decorative rather than load-bearing.
>
> *Which pile:* **(a) The rule covered it directly and was applied strictly.**

**2.**
> *The post:* "Three of us ran the same session on the same day, different shoes, within 10 seconds of each other over 5 miles. I'm not saying shoes do nothing, but whatever they do was smaller than the noise between three people." (sp_010)
>
> *Staff said / I said:* `analysis` / `analysis` (Agreed)
>
> *My call, and why:* One could argue this is informal personal chatter, but the poster structured a controlled comparative trial (same session, same day, different shoes, 10s split over 5 miles) that directly supports and limits their conclusion ("smaller than the noise between three people"). Under Rule 1 and Rule 3, the checkable comparative data carries the argument.
>
> *Which pile:* **(c) Defensible boundary application where an informal tone could tempt an alternate label, but the load-bearing empirical evidence makes `analysis` the winning call.**

**3.**
> *The post:* "Just realised the shoes I set every PB in this year are the ones I bought by accident in the wrong size." (sp_029)
>
> *Staff said / I said:* `reaction` / `reaction` (Agreed)
>
> *My call, and why:* The post mentions PBs and running shoes, but Rule 2 clarifies the distinction: `reaction` reports what just happened to the writer and how it felt, while `hot_take` asserts something general about the world. This is an immediate personal realization ("Just realised...") about the writer's own footwear rather than an argument about shoe sizing.
>
> *Which pile:* **(b) A subtle edge case where the rule differentiates general claims from personal experience, confirming that first-person realizations belong in `reaction`.**

**What the pattern in my agreement tells me:**
Achieving 100% agreement confirms that consistency in labeling depends almost entirely on disciplined application of decision rules rather than subjective impressions of tone. In particular, distinguishing decorative numbers from load-bearing evidence (Rule 1) and separating general claims from first-person reports (Rule 2) eliminated the errors that typically cause agreement to drop into the 70s. This same rigor transferred directly to my own 200-post dataset, explaining why the fine-tuned model achieved clean separation across classes.



---

## The Improvement

**What I changed:**
Added 30 new, independently verified examples to my thinnest label (`reaction`), bringing its representation from 62 to 92 examples (230 total posts in `labels.csv`, composed of 40.0% `reaction`, 30.4% `analysis`, and 29.6% `hot_take`).

**Which diagnosis pointed at it:**
In the Before run, `reaction` was our smallest class (62 posts, 31.0%) and exhibited the lowest F1 floor across all seeds (0.947 on Seed 2024, where a scene-analysis post was misclassified as `reaction`). Expanding the thinnest label aimed to provide richer emotional vocabulary, eliminate minority-class sample variance, and test whether increased representation broke the confidence ceiling.

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | >= 0.65 | 0.914 | 0.971 | 0.943 | MET |
| 2. Per-label F1 floor | >= 0.55 | 0.824 | 0.947 | 0.900 | MET |
| 3. Label balance (max/min) | 20% - 50% | 40% / 30% | 40% / 30% | 40% / 30% | MET |
| 4. Calibration agreement | >= 0.70 | 1.000 (30/30) | 1.000 (30/30) | 1.000 (30/30) | MET |
| 5. Confidence separation | >= 0.15 | 0.273 | 0.091 | 0.091 | MISSED |

**Did it help, and how do I know:**
Comparing the Before and After results yields a nuanced, revealing finding:
1. **Confidence separation (Criterion 5):** On Seed 42, adding 30 reaction examples successfully broke the accuracy ceiling. The most-confident third scored 1.000 accuracy while the least-confident third scored 0.727—achieving a **0.273 separation** and passing the criterion on that split for the first time. However, on Seeds 7 and 2024, accuracy remained high (0.971 and 0.943), leaving the separation at 0.091, so the criterion remains technically MISSED across all three seeds.
2. **Overall Accuracy and Spread:** Mean accuracy moved from 0.989 before (1.000, 1.000, 0.967) to 0.943 after (0.914, 0.971, 0.943), with spread widening from 0.033 to 0.057.
3. **Class Balance Impact:** By enlarging `reaction` from 31% to 40% of the dataset, the model developed a slight prior lean toward `reaction`. In Seed 42's confusion matrix, 3 real `hot_take` posts were called `reaction` (0 in the other direction).
*Verdict:* It partially helped by creating meaningful calibration variance on Seed 42, but showed that expanding one label to 40% creates a slight prior pull that pulls borderline unhedged opinions into the expanded class.

---

## What's Still Broken

**For each criterion still missed: what you'd do, and why you stopped:**
Criterion 5 (Confidence separation >= 0.15 across all seeds) was met on Seed 42 (0.273) but missed on Seeds 7 (0.091) and 2024 (0.091).
- *What I would do:* Apply temperature scaling to the logits during inference or add hard contrastive negative pairs specifically targeting the boundary where aggressive `hot_take` language resembles visceral `reaction` language.
- *Why I stopped:* The course specification mandates testing exactly one isolated improvement between Before and After to measure its unconfounded effect. Layering temperature scaling or hyperparameter changes on top of dataset expansion would invalidate the single-variable comparison.

**The gap between what I meant my labels to capture and what the model learned:**
I intended `hot_take` to capture sweeping claims about a film's quality and `reaction` to strictly denote first-person physical or emotional sensations. The confusion matrix shows that when `reaction` was expanded, the model latched onto heated, hyperbolic emotional tone as a proxy for `reaction`, occasionally misclassifying aggressively stated opinions as personal reactions.

<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [x] criteria.md has five numbered criteria, each naming a NUMBER
       [x] Each has a reason underneath tied to your data or taxonomy
       [x] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [x] No label above 70%
       [x] All five unit 5 sections have real content
       [x] Label Taxonomy includes the decision rule for your hardest boundary
       [x] The Dataset includes three hard cases
       [x] results.json and test_split.csv committed (the notebook does this)
       [x] At least four new commits
       [x] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [x] Baseline vs. Trained table, with your prediction written beforehand
       [x] Run Log — Before, five criteria across three seeds
       [x] Confusion matrix TYPED AS A MARKDOWN TABLE
       [x] A verdict on every criterion
       [x] A diagnosis for every miss, using the agreement report as evidence
       [x] Agreement Report with every disagreement adjudicated
       [x] One improvement, with Run Log — After
       [x] What's Still Broken
       [x] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and agreement_results.json committed
       [x] At least four new commits
       [x] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
