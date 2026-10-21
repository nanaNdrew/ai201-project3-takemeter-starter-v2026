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

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Moment 2**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Pre-labelling disclosure:**

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy |  |  |  |
| Macro F1 |  |  |  |
| F1 — `label_one` |  |  |  |
| F1 — `label_two` |  |  |  |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



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
