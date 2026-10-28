# Decisions

This file is the history of the project. Append an entry when a choice changes the work: the quantity, the data, the split, the model, the loss, or the action. Leave old entries in place. A later entry can reverse an earlier one. If a result depends on a random draw, record the seed under Why. The split entry records the cutoff date, or the seed and the fraction. The pull request that merges the code names this entry.

## 2026-10-27: Third squat attempt after a made second, raw lifters, OpenPowerlifting

- Decision. Forecast whether a raw lifter makes the third squat attempt after a made second, and use it to choose the jump. Data: OpenPowerlifting bulk CSV, snapshot 2026-10-24.
- Why. 759,877 qualifying attempts with a 68.3% make rate, so both outcomes are common and the model is straightforward to fit and check. The loss is in kilograms, which the lifter uses directly. The data are public domain.
- Alternative. Opening-attempt choice to avoid a squat bomb-out. Bigger stakes (a bomb zeroes the total), but raw lifters with a prior meet bomb only 0.97% of the time (5,463 of 562,649), which makes the probabilities hard to estimate and check. Also considered and set aside: a telecom churn campaign dataset, where the costs would have been assumed rather than measured.
- Revisit. If a model of the jump cannot beat the fixed 5% jump on held-out meets in expected kg, or if the selection bias (small jumps after hard seconds) makes the jump effect impossible to separate.
