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

My community is houseplant owners: the advice threads and questions where people trade tips on keeping plants alive. My classifier reads a single post or comment and sorts it by the care topic it is mainly about. There are eight topics: `beginner_recommendation`, `soil_repotting`, `watering`, `general_care`, `pest_management`, `light_placement`, `pet_safety` and `propagation`. The point is to route a question to the people and answers that fit it, such as sending "why are my leaves yellow and the soil damp" to watering advice rather than to a general pile.

---

## Label Taxonomy

### `beginner_recommendation`

**Definition:** Suggests which plants (or how many) a new owner should start with.

**Example 1:**
> Pothos! They are great for beginners... they don't need a ton of light, so they're good indoors.

**Example 2:**
> If you want colorful foliage, look into aglaonemas... For a first shelf: pothos, snake plant, spider plant and a peperomia.

### `soil_repotting`

**Definition:** About potting mix, pot size, drainage, or when and how to repot.

**Example 1:**
> Immediately repot everything into huge pots when I bought them. So many things died that way

**Example 2:**
> I take it home and immediately repot in new, fresh soil/medium of my own.

### `watering`

**Definition:** About how much, how often, or by what method to water, including over- and underwatering.

**Example 1:**
> Watering on a schedule rather than checking the soil, and not knowing that different plants have different watering requirements

**Example 2:**
> I water by pot weight... After a while you get to know when certain plants need watering.

### `general_care`

**Definition:** Routine upkeep that isn't one specific topic: inspections, cleaning, fertilizing, seasonal adjustments, and the general habit or mindset of looking after plants.

**Example 1:**
> I do a walkthrough weekly to check which ones need water.

**Example 2:**
> Most of them have slow release fertilizer so I don't have to worry about that.

### `pest_management`

**Definition:** About spotting, preventing or treating pests, including quarantining new plants.

**Example 1:**
> I used to think quarantining new plants wasn't all that important... and helllooo thrips. Goodbyyyye lots of plants.

**Example 2:**
> Beneficial nematodes... that combined with the yellow sticky traps seems to have handled it.

### `light_placement`

**Definition:** About how much light a plant gets and where to put it.

**Example 1:**
> Light is more important than you think. You should be able to read small print without turning a light on.

**Example 2:**
> Research your growing conditions... use a light meter app to see usable light.

### `pet_safety`

**Definition:** About whether plants are toxic or safe around cats, dogs and other pets.

**Example 1:**
> If you have pets, make sure what you're getting isn't toxic :)

**Example 2:**
> Just adopted a kitten. Which of my plants are toxic to cats? I have a pothos, a peace lily, a snake plant and a spider plant.

### `propagation`

**Definition:** About making new plants from an existing one: cuttings, water rooting, division, offsets.

**Example 1:**
> My pothos got really leggy so I cut a few vines just below a node and put them in a jar of water on the kitchen counter. Roots showed up in about two weeks...

**Example 2:**
> Spider plant finally sent out a bunch of babies. Can I pot them up while they are still attached to the mother, or should I snip them off first and root them in water?

### The hardest boundary

**Which two labels:** `watering` and `soil_repotting`. Overwatered plants and bad pots show up together: a plant stays wet and droops because it was watered too often, or because the pot has no drainage.

**The decision rule I used every time:** Label by the cause the post blames or the fix it asks about. If it's about the pot, soil, drainage or repotting, it's `soil_repotting`, even if watering is mentioned as a symptom. If it's about when, how often or how much to water, it's `watering`. If the post names neither as the cause, I go with whichever topic it spends most words on.


---

## The Dataset

**Where the posts came from:** TODO: name the source (which forum or thread, and when). The retrieved posts are short comments and excerpts from houseplant advice threads. Some are cut off in the source, and their notes say so. 40 of the 71 rows so far are synthetic examples generated by an AI model and labelled by me; the other 31 are retrieved posts. Synthetic rows are marked `synthetic` in the `note` column.

**How I labelled them:** TODO: say how many of the 20 I labelled cold, unaided, and mark them `cold` in the CSV. The retrieved posts were labelled by me, reading each one. Several retrieved posts first had labels that aren't in my taxonomy (`care_challenges`, `fertilizing`). I relabelled them into `general_care`, and the note on each row says so.

**Counts per label** (71 rows so far; target is 150 minimum):

| Label | Count | Share |
|---|---|---|
| `beginner_recommendation` | 12 | 17% |
| `soil_repotting` | 11 | 15% |
| `watering` | 10 | 14% |
| `general_care` | 10 | 14% |
| `pest_management` | 9 | 13% |
| `light_placement` | 8 | 11% |
| `pet_safety` | 6 | 8% |
| `propagation` | 5 | 7% |
| **Total** | 71 | 100% |

**Three hard cases**

**1.**
> *The post:* "A lot of the typical not enough light/too much water set ups we see asking for help on here were me for my first year or two. I didn't realize how little light I had"
>
> *Could have been:* `watering` or `light_placement`
>
> *I chose, because:* `light_placement`. Watering is only mentioned as part of a familiar problem. The author's point is that they underestimated their light.

**2.**
> *The post:* "Plant jail (the bathroom) for a few weeks until I can be certain there are no health hazards"
>
> *Could have been:* `pest_management` or `general_care`
>
> *I chose, because:* `pest_management`. The plan is quarantine, and my definition lists quarantining new plants under pests, even though no pest is named.

**3.**
> *The post:* "Tradescantia tolerates low light, is very forgiving, props like a champ..."
>
> *Could have been:* `beginner_recommendation` or `propagation`
>
> *I chose, because:* `beginner_recommendation`. It's recommending an easy plant, and propagation gets one passing mention.

---|---|---|
|  |  |  |
|  |  |  |
|  |  |  |
| **Total** |  | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:*
>
> *Could have been:*
>
> *I chose, because:*

**2.**
> *The post:*
>
> *Could have been:*
>
> *I chose, because:*

**3.**
> *The post:*
>
> *Could have been:*
>
> *I chose, because:*

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

**Pre-labelling disclosure:** 40 rows in `labels.csv` are synthetic examples generated by an AI model and labelled by me. They are marked `synthetic` in the `note` column. The other rows are retrieved posts that I labelled myself.

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
