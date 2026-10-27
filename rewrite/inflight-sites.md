# `\InFlight{}` sites in `3D_RTI_paper/rewrite/`

Regenerated 2026-10-27 from `grep -n 'InFlight{' *.tex` after the defect pass
of the same day, so the line numbers are those of the files as they now stand.

**146 marked values on 42 lines, 5 files.** (The count was 37 lines before
Table~7 was set as two blocks of the same five rows, which splits each of its
rows over two lines and changes no value.) Every marked value is a number
that a campaign now running will replace; nothing else is marked. The macro is
defined in `paper.tex` as `\providecommand{\InFlight}[1]{#1}`, so it prints its
argument and the paper builds and reads normally.

## The two campaigns

**Campaign A — volumetric parity.** The procedure on six instances at the
compact reference's own recorded budget (3600 s, 20 GB, 10 threads). It exists
because the current `tab:volumetric` compares a search at a *recovered*
1800 s / 4 GB / 12 cores against a compact model at 3600 s / 20 GB / 10 cores,
unequal in both directions, so no row of that table is a timing claim today.
Replaces: every method-side cell of `tab_volumetric.tex`, and the three
accuracy figures the prose quotes from it.

**Campaign B — the 3D ladder, re-run in the Julia implementation** at the
recorded per-rung budgets (900 / 900 / 1800 / 1800 / 3600 s). Replaces: every
cell of `tab_ladder.tex`, the method columns of `tab_head.tex` and
`tab_repack.tex`, and every figure the prose quotes from them.

Numbers **not** in flight, and deliberately unmarked: every compact-model
figure (objective, dual bound, trucks, time, variable and constraint counts,
peak RSS), the placeability census (724 = 501 + 174 + 49 and its by-factor
shares), the fleet-floor certifications (1.5 / 3.9 / 16.3 s), the packing
counts (24 686 / 246 / 11 866 / 4 327 / 397), the theta catalogue, the sweep
range (2.5–111.8 s over 41 runs), the repacked truck counts (74 / 212, 86 /
256) and the over-delivery (80 / 137 units), and the two compact volumetric
dual bounds that appear as an interval's lower end in `tab_head.tex`
(38 851.93 on row 2, 68 139.17 on row 4).

## Sites, file by file

### `tab_ladder.tex` — campaign B (every cell)

The table is set as two blocks, so each row has a line in each: the first block
carries `z`, trucks, verified, LB and gap, the second root, tree, nodes,
dropped, open and wall/budget.

| line | calls | what |
| --- | --- | --- |
| 43 / 57 | 5 / 6 | `I_8_5_2_90` row |
| 44 / 58 | 5 / 6 | `I_8_10_2_90_k25` row |
| 45 / 59 | 5 / 6 | `I_12_12_2_90_k25` row |
| 46 / 60 | 5 / 6 | `I_16_15_2_90_k25` row |
| 47 / 61 | 5 / 6 | `I_24_20_2_90_k50` row |

### `tab_volumetric.tex` — campaign A (method side only)

| line | calls | what |
| --- | --- | --- |
| 44 | 5 | `I_8_10_2_90_k25`: z, bound, trucks, t, dz |
| 45 | 5 | `I_16_15_2_90_k25`: z, bound, trucks, t, dz |
| 46 | 6 | `I_16_15_2_90_k50`: + the "tighter bound" column |
| 47 | 6 | `I_24_20_2_90_k50`: + the "tighter bound" column |
| 48 | 6 | `I_24_20_2_90_k80`: + the "tighter bound" column |

The compact columns of these rows (z, bound, trucks, t) are recorded and are
not marked.

### `tab_head.tex` — campaign B (method side and the interval's upper end)

| line | calls | what |
| --- | --- | --- |
| 40 | 6 | `I_8_5_2_90`: method z, trucks, LB, gap, and both interval ends |
| 41 | 5 | `I_8_10_2_90_k25`: method z, trucks, LB, gap, interval upper end |
| 42 | 6 | `I_12_12_2_90_k25`: method z, trucks, LB, gap, both interval ends |
| 43 | 5 | `I_16_15_2_90_k25`: method z, trucks, LB, gap, interval upper end |
| 44 | 6 | `I_24_20_2_90_k50`: method z, trucks, LB, gap, both interval ends |

Rows 2 and 4 take their interval's **lower** end from a compact volumetric
dual bound, which is recorded and is not marked (the audit's **m7**).

### `tab_repack.tex` — campaign B

| line | calls | what |
| --- | --- | --- |
| 41 | 2 | the method's truck counts, 54 and 160 |
| 43 | 2 | the savings derived from them, 27.0 / 19.1 and 24.5 / 16.2 % |

Rows 1 to 3 of that table (45 / 137, 86 / 256, 74 / 212) are recorded and are
not marked.

### `sec_experiments.tex` — the prose that quotes those tables

| line | calls | campaign | value |
| --- | --- | --- | --- |
| 77 | 1 | A | 68 139.59 |
| 78 | 1 | A | 0.0006 % |
| 79 | 2 | A | 68 131.43, 0.0114 % |
| 94 | 1 | A | 52 105.25 (the certified bound of the comparison) |
| 97 | 1 | A | 26.3 s (when that bound was in hand) |
| 125 | 1 | B | 160 trucks |
| 129 | 2 | B | 0.0076 %, 0.165 % |
| 131 | 1 | B | 13.3 % |
| 141 | 1 | B | 2.00 % (the tree's improvement on rung 3) |
| 142 | 2 | B | 53 195.84, 52 129.45 |
| 143 | 1 | B | 0.03 % |
| 150 | 1 | B | 12 (trucks on rung 1) |
| 151 | 3 | B | 26, 34, 44 |
| 152 | 3 | B | 54, 122, 160 |
| 168 | 2 | B | 864 s of 873 s |
| 188 | 1 | B | 0.102 % (cheaper than the compact 3D model) |
| 260 | 2 | B | 54, 160 |
| 261 | 2 | B | 27.0 %, 24.5 % |
| 262 | 2 | B | 19.1 %, 16.2 % |
| 293 | 1 | B | 13.3 % (the crush-binding rungs' bound gap) |

The line carrying 52 105.25 and the line carrying 26.3 s hold the two numbers the prose argues a *comparison*
from; they were unmarked before this revision while `tab_ladder.tex:36` marked
the identical 52 105.25, which is the one slip the audit's **M15** records.
Both are now marked.

## What is no longer marked

`sec_conclusions.tex` carried one marked value (13.3 %) before the revision.
The conclusions no longer quote it --- it is stated once, in
`sec_experiments.tex` --- so the file has no marked site.

## How to check this file

```
cd /home/jj/3D-RTI/3D_RTI_paper/rewrite
grep -n 'InFlight{' *.tex | awk -F: '{n=gsub(/InFlight\{/,"&"); print $1":"$2" "n}'
```
