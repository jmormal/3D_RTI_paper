# `\InFlight{}` sites in `3D_RTI_paper/rewrite/`

Regenerated 2026-10-27 after the volumetric parity campaign was substituted,
so the counts and line numbers are those of the files as they now stand.

**112 marked values on 32 lines, 4 files.** Every marked value is a number the
3D ladder re-run will replace; nothing else is marked. The macro is defined in
`paper.tex` as `\providecommand{\InFlight}[1]{#1}`, so it prints its argument
and the paper builds and reads normally.

## Campaign A --- volumetric parity: DONE

Substituted on 2026-10-27 from `results/julia/parity_vol.csv`: the procedure's
volumetric mode on six instances at the compact reference's own budget
(3600 s, 20 GB, ten threads, commit `10e633c`), against the recovered
1800 s / 4 GB / 12-core configuration it replaces. Thirty-four values across
`tab_volumetric.tex` (all six rows, both sides) and the prose quoting it.

Three things changed besides the numbers:

* the table is framed as a **check on the procedure, not a contest** --- the
  volumetric problem is the only part of the study with an independent
  optimum, so the question is whether the procedure arrives where it should;
* the claim that the method's plan is cheaper on all four open instances is
  gone: at equal budgets it is cheaper on three and dearer on one, by
  $0.1195\%$ on `I_16_15_2_90_k50`. Where the compact model works it is also
  far the faster, which the section now says;
* the certification timing in the `I_12_12_2_90_k25` bound comparison was
  $26.3$ s, from the reference implementation; this run's is $274$ s.

Every parity row carries `root_proved = true`, so none rests on the
unproved-root bound reported before `e85c70c`, and `pack_calls = 0` on all
six, so none is touched by the `stack_patterns` defect fixed in `b85f174`
(the packing layer never runs in volumetric mode).

## Campaign B --- the 3D ladder, re-run in Julia

Five rungs at the recorded per-rung budgets (900 / 900 / 1800 / 1800 /
3600 s), 20 GB, ten threads. Replaces: every cell of `tab_ladder.tex`, the
method columns of `tab_head.tex` and `tab_repack.tex`, and every figure the
prose quotes from them.

The first pass of this ladder is kept as
`results/julia/ladder_3d.prefix-stackpatterns.csv` with its journal beside it.
It ran before `b85f174`, when a maximality test blind to how many units the
load actually had could refuse a placeable load, and rung 5 crashed outright
when the covering model came back INFEASIBLE for want of a column.

**Correction.** The commit message of `b85f174` and an earlier version of this
file cited rung 4 as the measure of what that defect cost --- 175 of 960 pack
calls refused, and 59 trucks where the reference needs 54. That attribution is
wrong. Re-run under the fix, rung 4 is identical in every counter: same
incumbent 83 645.86, same 59 trucks, the same 175 of 960 refusals, the same
751 shelf and 34 constraint-programme packs, the same 2 nodes and 532 columns.
The fix is inert there, so those refusals are sound ones and rung 4's deficit
has another cause (below).

Where the defect *was* active: rung 1 went from 56 of 977 refusals to 46 of
806, rung 2 from 2 of 197 to 4 of 185, and rung 5 ran to completion instead of
crashing. Its demonstrated cost is the crash and the 18 of 64 sampled classes
whose enumeration came back empty, not rung 4's five trucks.

**The pre-fix rows are valid but pessimistic** --- a refused load is one the
search cannot use, every plan is still verified truck by truck, and the bound
comes from the volumetric relaxation and never from a placeability cut.

### Rung 4's actual deficit, and it is not placeability

From the run's own phase log: a geometric seed pool of **17 columns** for a
16-product, 15-period instance (27 s), then root column generation that gets
`root_cg_share * tl = 243` s and completes **one iteration for 13 columns**
because one geometric sweep is `2|T| = 30` pricing calls, then a construction
over that ~30-column pool that needs **68 trucks**, which the tree pulls back
to 59 before the budget ends. `tree.jl` states the mechanism where it sets the
window: one sweep is "longer than the whole root window on the larger
instances, so this is the bound a 3D run usually reports while the geometric
CG only supplies columns". The reference implementation reached 54 trucks *at
its root*, so this is a difference in how the root budget is spent.

The experiment that follows is rung 4 at `root_cg_share` $0.15$ against $0$ ---
handing the construction those 243 s and skipping a sweep that cannot finish.
`CLAUDE.md` records that configuration as sound, the plan coming from the
seeded pool and the tree and the bound from the volumetric relaxation. It must
be reported as its own labelled measurement: one ladder table built from two
configurations is the defect invariant 10 exists to prevent.

`scripts/ladder_values.py` (session scratchpad) derives every cell below from
`results/julia/ladder_3d.csv`, the run's journal in
`results/julia/ladder_3d.log` and `results/repacked_volumetric.json`. Its
arithmetic is validated against the pre-fix rows, where it reproduces the
published $27.0\%$ / $19.1\%$ savings and picks the tighter interval end.

### Numbers **not** in flight, and deliberately unmarked

Every compact-model figure (objective, dual bound, trucks, time, variable and
constraint counts, peak RSS), the placeability census (724 = 501 + 174 + 49
and its by-factor shares), the fleet-floor certifications (1.5 / 3.9 /
16.3 s), the packing counts (24 686 / 246 / 11 866 / 4 327 / 397), the theta
catalogue, the sweep range (2.5--111.8 s over 41 runs), the repacked truck
counts (74 / 212, 86 / 256) and the over-delivery (80 / 137 units), and the
two compact volumetric dual bounds that appear as an interval's lower end in
`tab_head.tex` (38 851.93 on row 2, 68 139.17 on row 4).

The placeability census and the 36-instance benchmark come from the **Python
reference**, which does not have the `stack_patterns` defect: its
`stack_columns` bounds layers by the truck's own caps rather than by an
available count, so its enumeration and its maximality test agree. Those
numbers, and the abstract's 501-of-724, are unaffected.

### `tab_ladder.tex` --- every cell

Set as two blocks, so each row has a line in each: the first carries `z`,
trucks, verified, LB and gap, the second root, tree, nodes, dropped, open and
wall/budget.

| line | calls | what |
| --- | --- | --- |
| 43 / 57 | 5 / 6 | `I_8_5_2_90` row |
| 44 / 58 | 5 / 6 | `I_8_10_2_90_k25` row |
| 45 / 59 | 5 / 6 | `I_12_12_2_90_k25` row |
| 46 / 60 | 5 / 6 | `I_16_15_2_90_k25` row |
| 47 / 61 | 5 / 6 | `I_24_20_2_90_k50` row |

Its caption needs more than numbers. It attributes every row to Python commit
`73411b9` and compares row 1 against `tab:bench` as "two runs, on two
commits"; after substitution the ladder is Julia and `tab:bench` is still
Python, so that sentence becomes a statement about two *implementations*. The
provenance of each table must say which implementation produced it --- two
tables describing two algorithms under one name is the defect invariant 10 of
`CLAUDE.md` exists to prevent.

### `tab_head.tex` --- method side and the interval's upper end

| line | calls | what |
| --- | --- | --- |
| 40 | 6 | `I_8_5_2_90`: method z, trucks, LB, gap, and both interval ends |
| 41 | 5 | `I_8_10_2_90_k25`: method z, trucks, LB, gap, interval upper end |
| 42 | 6 | `I_12_12_2_90_k25`: method z, trucks, LB, gap, both interval ends |
| 43 | 5 | `I_16_15_2_90_k25`: method z, trucks, LB, gap, interval upper end |
| 44 | 6 | `I_24_20_2_90_k50`: method z, trucks, LB, gap, both interval ends |

Rows 2 and 4 take their interval's **lower** end from a compact volumetric
dual bound, which is recorded and is not marked (the audit's **m7**).

### `tab_repack.tex`

| line | calls | what |
| --- | --- | --- |
| 41 | 2 | the method's truck counts, 54 and 160 |
| 43 | 2 | the savings derived from them, 27.0 / 19.1 and 24.5 / 16.2 % |

Rows 1 to 3 of that table (45 / 137, 86 / 256, 74 / 212) are recorded and are
not marked. The cost saving's denominator is `repacked_cost` from
`results/repacked_volumetric.json` (97 139.17 and 275 266.21); the
`method_trucks` field of that file is the reference implementation's count and
goes stale on substitution --- the method column comes from the ladder CSV.

### `sec_experiments.tex` --- the prose that quotes those tables

| line | calls | value |
| --- | --- | --- |
| 151 | 1 | 160 trucks |
| 155 | 2 | 0.0076 %, 0.165 % |
| 157 | 1 | 13.3 % |
| 167 | 1 | 2.00 % (the tree's improvement on rung 3) |
| 168 | 2 | 53 195.84, 52 129.45 |
| 169 | 1 | 0.03 % |
| 176 | 1 | 12 (trucks on rung 1) |
| 177 | 3 | 26, 34, 44 |
| 178 | 3 | 54, 122, 160 |
| 194 | 2 | 864 s of 873 s |
| 214 | 1 | 0.102 % (cheaper than the compact 3D model) |
| 286 | 2 | 54, 160 |
| 287 | 2 | 27.0 %, 24.5 % |
| 288 | 2 | 19.1 %, 16.2 % |
| 319 | 1 | 13.3 % (the crush-binding rungs' bound gap) |

## How to check this file

```
cd /home/jj/3D-RTI/3D_RTI_paper/rewrite
grep -n 'InFlight{' *.tex | awk -F: '{n=gsub(/InFlight\{/,"&"); print $1":"$2" "n}'
```
