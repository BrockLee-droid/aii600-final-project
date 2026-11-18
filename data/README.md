# Data

## Source and terms

OpenPowerlifting bulk CSV, snapshot dated 2026-10-24, from the OpenPowerlifting project (https://www.openpowerlifting.org). OpenPowerlifting compiles meet results reported by powerlifting federations. The data are contributed to the public domain (see `LICENSE.txt` inside the zip), so the file is republished on this repository's release tagged `data`.

This project uses data from the OpenPowerlifting project, https://www.openpowerlifting.org.

## File

| File | On the `data` release | SHA-256 |
|---|---|---|
| `openpowerlifting-latest.zip` | yes | `26be6b73f4b16ba5f4f8fb568f258d2c5ab165f379aed21e2489af9d0022afe7` |

The notebook reads the CSV inside the zip directly and does not unzip it. On first run it saves the filtered rows to `data/squat3_room.pkl`, which git ignores.

Download:

```bash
gh release download data --dir data --repo BrockLee-droid/aii600-final-project
shasum -a 256 data/openpowerlifting-latest.zip
```

## What the notebook uses

- Unit of observation: one lifter's third squat attempt at one full-power (SBD) meet, raw equipment, after a made second attempt, with a third attempt 0–25% heavier than the second, sex M or F, and a recorded bodyweight: 757,813 attempts, 302,485 lifter names, 1965-09-04 to 2026-10-18.
- Room left: the lifter's best made raw squat at any earlier meet date, from the same file. 484,979 attempts have one; 456,553 have a second attempt within 80–120% of it.
- Model rows: those 456,553 attempts with a jump of 4% or more, 258,222 attempts.
- Failed attempts are stored as negative weights. Not every federation reports attempts, so the sample covers only federations that do.