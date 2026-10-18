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

## 1.

Overall accuracy is at least 0.70 on the held-out test posts, in all three seeds.

**Why this target:**

With four labels, random guessing would be about 25% accurate, and always predicting my largest label (general_care) would be about 27% accurate. I chose 0.70 because it is well above both baselines and would be high enough for me to trust the classifier to route most posts without manually reviewing every prediction. I did not choose a higher target because some posts contain overlapping topics, especially between general_care and watering_soil, which creates label boundaries that are not always clear even for a human annotator.

---

## 2.

Every label has an F1 score of at least 0.55 on the held-out test posts, in all three seeds.

**Why this target:**

With four labels, random guessing would perform at about 25%. I chose 0.55 because it is more than double that baseline while still being realistic for the smallest class, pest_management, which contains only 40 posts. A lower target would not show that the model learned each label, while a much higher target may be unrealistic because some posts sit on unclear boundaries between general_care and watering_soil.

---

## 3.

No label represents more than 28% of the complete dataset.

**Why this target:**

My labels currently range from about 23% to 27% of the dataset. Keeping every label below 28% prevents one category from dominating the data while still allowing some growth in individual labels as I add more examples.

---

## 4.

The difference between the highest and lowest seed accuracy is no more than 0.10.

**Why this target:**

The same model is trained three times with different random seeds. I expect some variation because the held-out test set contains only about 27 posts, but a gap larger than 0.10 would suggest that performance depends too heavily on the seed rather than on patterns learned from the labels.

---

## 5.

The accuracy of the most-confident third of predictions is at least 0.10 higher than the accuracy of the least-confident third, or both groups achieve an accuracy of 1.00, in all three seeds.

**Why this target:**

The notebook reports accuracy separately for the most-confident and least-confident thirds of predictions. With about 27 held-out test posts, each third contains about 9 posts, so a difference of 0.10 corresponds to roughly one additional correct prediction. I chose this target because a useful confidence score should make the most-confident predictions more accurate than the least-confident predictions. I also allow a tie when both groups achieve an accuracy of 1.00, because confidence should not be penalized when all predictions are correct.

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
