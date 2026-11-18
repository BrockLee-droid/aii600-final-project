# Decisions

This file is the history of the project. Append an entry when a choice changes the work: the quantity, the data, the split, the model, the loss, or the action. Leave old entries in place. A later entry can reverse an earlier one. If a result depends on a random draw, record the seed under Why. The split entry records the cutoff date, or the seed and the fraction. The pull request that merges the code names this entry.

## 2026-10-27: Third squat attempt after a made second, raw lifters, OpenPowerlifting

- Decision. Forecast whether a raw lifter makes the third squat attempt after a made second, and use it to choose the jump. Data: OpenPowerlifting bulk CSV, snapshot 2026-10-24.
- Why. 759,877 qualifying attempts with a 68.3% make rate, so both outcomes are common and the model is straightforward to fit and check. The loss is in kilograms, which the lifter uses directly. The data are public domain.
- Alternative. Opening-attempt choice to avoid a squat bomb-out. Bigger stakes (a bomb zeroes the total), but raw lifters with a prior meet bomb only 0.97% of the time (5,463 of 562,649), which makes the probabilities hard to estimate and check. Also considered and set aside: a telecom churn campaign dataset, where the costs would have been assumed rather than measured.
- Revisit. If a model of the jump cannot beat the fixed 5% jump on held-out meets in expected kg, or if the selection bias (small jumps after hard seconds) makes the jump effect impossible to separate.

## 2026-11-17: Rows used

- Decision. Keep raw, full-power (SBD) meets with all three squat attempts recorded, a made second attempt, and a third attempt more than 0% and at most 25% heavier than the second. Keep sex M or F with a recorded bodyweight. 757,813 attempts remain.
- Why. Jumps above 25% are rare (1,727 attempts) and most likely entry errors or unusual meets. The Mx category has 134 attempts, too few to estimate. Bodyweight is missing for 0.03% of attempts.
- Alternative. Keep every jump and impute bodyweight. Rejected because it adds rows that look like errors and changes almost nothing in the count.
- Revisit. If the federation or data-entry pattern behind the large jumps turns out to be real lifting, put them back.

## 2026-11-17: Add room left (responds to issue #1)

- Decision. Add room left: the second attempt divided by the lifter's best made raw squat at any earlier meet date in the archive (`second_vs_best`). Keep only attempts with an earlier meet and a second attempt between 80% and 120% of that best: 456,553 attempts.
- Why. The jump is confounded with a hard second attempt: lifters who barely made the second take small jumps, so a model that sees only the jump learns "jump bigger". How the second felt is not in the file, but how close it was to the lifter's earlier best is, and the lifter knows it before choosing the third. Outside 80–120% the earlier best looks like a comeback or two lifters with the same name.
- Alternative. Keep every attempt and leave room left out. Rejected after the instructor's feedback (issue #1): the jump effect cannot be read without it. Also considered: the lifter's best total instead of best squat, which mixes in bench and deadlift.
- Revisit. Lifters without an earlier meet (about 36% of attempts) are left out, so the rule does not apply to first-time lifters. If first-meet lifters behave differently, they need their own treatment.

## 2026-11-17: Limit the rule to jumps of 4% or more

- Decision. The room left check (make rate by jump size within room-left groups) passes from about 5% up and fails below 4%: within every group, 0–2% jumps are made least often (0.524 to 0.613), also among lifters who made their opener. The rule, the model fit and the scores use only jumps of 4% or more: 258,222 attempts.
- Why. Below 4%, room left is not a good enough stand-in for a hard second attempt, so the data cannot say what a small jump does for a given lifter. Fitting on those rows would bend the curve with the same bias the check exposed.
- Alternative. Keep the full range and report that the rule is not ready below 4%. Deferred to the final: before 9 December, try a better stand-in for how hard the second attempt was (the first-to-second jump, or the lifter's record on earlier third attempts) and rerun the check.
- Revisit. If a better stand-in makes the small-jump rows fall in line, extend the rule below 4%.

## 2026-11-17: Split by date, cutoff 2025-01-01

- Decision. Train on meets before 2025-01-01 (192,789 attempts, made 69.2%). Hold out every meet from 2025-01-01 to 2026-10-18 (65,433 attempts, made 70.3%). No random draw is involved in the split.
- Why. The decision is about meets that have not happened yet, so the held-out set is a later time. Room left uses only earlier meets, so a held-out row never sees its own meet. A random split would put the same lifter's earlier and later meets on both sides.
- Alternative. A random 80/20 split, or a split by lifter. Rejected: a random split leaks the future into training, and a split by lifter ignores that the decision is made at a later date.
- Revisit. If the make rate or the jump habits change sharply after 2025, use a more recent training window.

## 2026-11-17: Logistic regression on jump, room left, opener result, 1st-to-2nd jump, sex and bodyweight

- Decision. Logistic regression for the chance of making the third attempt, linear in the jump (as a fraction of the second attempt), room left, whether the opener was made, the 1st-to-2nd jump, sex and bodyweight. Fitted unpenalised (`C=1e6`) with `tol=1e-8`. The 95% interval on predictions comes from 20 bootstrap refits, seed 600. Fitted coefficients: jump −3.19, room left −2.26, opener made +0.46.
- Why. It is the simplest model that turns the inputs into a probability, and its coefficients can be read as log-odds. With room left in and jumps of 4% or more, both main coefficients have the expected sign: a bigger jump and less room left both lower the chance. A squared jump term changed the held-out scores by less than 0.001 kg, so it was dropped. At the default tolerance (`1e-4`) the optimiser stopped early: in an earlier fit on all jumps, the jump coefficients came out as 2.77 and 0.09 instead of the converged 12.14 and −57.08. With `tol=1e-8` the fit converges and the bootstrap intervals are about ±0.004.
- Alternative. A jump-and-room-left-only model, or a flexible model such as gradient-boosted trees. The two-input model scores worse (MAE 4.106 kg against 4.039 kg). A flexible model is harder to explain and defend for a first version.
- Revisit. If the jump coefficient turns positive in a later training window, or with a better stand-in for a hard second attempt, the confounding is back.

## 2026-11-17: Score predictions in kilograms

- Decision. Score each prediction by expected gain, P(make) × jump in kg, against realised gain, the jump if made and 0 if missed, as RMSE and MAE in kg on the held-out meets, with the Brier score alongside. The prediction baseline is the training make rate for every attempt. The proposal's fixed 5% jump stays as the baseline for the decision rule, which is scored in the Decision section.
- Why. The decision is in kilograms, so the score is too. The fixed 5% jump is a rule for choosing an action, not a prediction, so it cannot be scored against outcomes the same way. Results on 65,433 held-out attempts: baseline RMSE 4.874 kg, MAE 4.143 kg, Brier 0.2089; model RMSE 4.827 kg, MAE 4.039 kg, Brier 0.2056. The model is closer by about 0.10 kg per attempt on MAE.
- Alternative. Accuracy or log loss alone. Rejected because neither is in the units of the decision.
- Revisit. If the gap between model and baseline is within noise across other test windows, the inputs carry too little information and the decision rule should fall back to the baseline.
