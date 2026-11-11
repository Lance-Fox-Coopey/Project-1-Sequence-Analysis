# Read Mismatch Analyser

## Purpose
My program checks sequencing reads for mismatches against a reference sequence, and compares a control sample to a treatment sample.

## How to run
Open the notebook in Colab, upload read data files and the press run all and the final analysis is printed at the bottom.

## Inputs and outputs
The input expects a set of samples with either a control or treatment group and a list of read sequences and the reference sequence. The output is:

- Invalid / valid read counts.
- The percentage of valid reads with a mismatch.
- Substitution types per 100 valid reads.
- Top three mismatch positions and for those three a Welch's t-test.

## Code design

**Validation** – `validate_read` collects every single error in the order of brief sets, instead of stopping at the first one. In the invalid character loop I use `break` so that an error is only added one time per read even if there are multiple errors.

**Tie breaking** – `get_top_positions` sorts with `key=lambda pos: (-position_counts[pos], pos)`. The pairs are compared item by item, therefore negating the count puts the larger one first and the position number can settle ties so the earlier position will win. I could have sorted twice but I believe this one key does both jobs.

**Summary** – `build_summary` uses `setdefault` In order to make a samples record the very first time it shows up, this is so I don't need a separate had it been done before check.

## Testing

1. Step 4, `get_top_positions` with `{7: 2, 3: 2, 9: 2}` gave `[3, 7, 9]` as this was how I tested whether my ties of mismatch count would come out in order of position.
2. Step 4, `build_summary` without any reads was important to test as if my code tried to divide by 0 that would crash the program.
3. Step 5, for identical or invalid groups in `compare_positions` I checked whether `ValueError` was raised as without it a wrong call would end up still returning a t-test and p-value meaning it would go unnoticed.
