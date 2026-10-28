# Proposal

## Hypothesis

A raw lifter's chance of making a third squat attempt depends on how big a jump they take from a made second attempt, and a model of that chance can pick a third attempt that adds more expected kilograms than a fixed 5% jump. The quantity is the probability that the third squat attempt is made, for one lifter at one meet, as a function of the jump in kg.

## Data

Source: the OpenPowerlifting bulk CSV (snapshot dated 2026-10-24), a public-benefit archive of historical meet results compiled by the OpenPowerlifting project. The unit of observation is one lifter's squat at one full-power (SBD) meet, raw equipment, with all three squat attempts recorded, a made second attempt, and a third attempt heavier than the second. That leaves 759,877 attempts from 303,417 lifter names, 1965 to 2026, with 729,584 since 2015. The third attempt was made 68.3% of the time. Failed attempts are stored as negative weights. Not every federation reports attempts, so the sample covers only federations that do.

What I measure differs from what the decision needs. The lifter chooses the jump after feeling the second attempt, and that feeling is not in the data. This shows up already: jumps of 2% or less are made only 57.6% of the time, against about 70% for 4–8% jumps, most likely because lifters take small jumps after a hard second. That is selection bias, not noise, and more rows will not remove it. The `Equipment` label also records the category entered, not what the lifter wore.

The data are contributed to the public domain (bundled `LICENSE.txt`), so the files go on the release tagged `data`.

## Decision affected by the model

A lifter or coach picks the third squat attempt after a made second. The model gives the chance of making each candidate weight, and the rule picks the jump that maximises expected kilograms added: P(make | jump) × jump. The baseline is a fixed 5% jump, close to the median jump in the data (4.7%). Missing costs more than going light: a miss adds 0 kg and gives up the whole planned gain, while a light attempt gives up only the gap to what the lifter could have made.
