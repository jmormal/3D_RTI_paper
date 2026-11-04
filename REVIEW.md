# REVIEW — `3D_RTI_paper/rewrite/paper.pdf`

End-to-end referee pass, 2026-10-27. Nothing outside `rewrite/` was touched.
Two files in `rewrite/` were changed by the referee pass itself, both listed
under **What I fixed**.

**Resolution pass, 2026-10-27 (later the same day).** Every defect D1-D20
except **D14** has now been fixed in `rewrite/`; each entry in section 6 below
carries a `**FIXED**` line saying what was done and what settled it, and the
two entries left standing (D14, and the parts of D11 named there) carry
`**OPEN**` with the reason. Build after the fixes: 32 pages, exit 0, no
undefined reference or citation, 10 020 body words.

---

## 1. Build

```
cd /home/jj/3D-RTI/3D_RTI_paper/rewrite
PATH=/usr/bin:$PATH TEXINPUTS=..: latexmk -pdf paper.tex
```

| | |
| --- | --- |
| exit | `0`, clean from a deleted `.aux`/`.bbl` |
| **pages** | **32** (670 371 bytes) |
| undefined references | **none** |
| undefined citations | **none** (biber: 28 citekeys, both `.bib` files found) |
| multiply-defined labels | **none** |
| missing files | **none** (the two `biblatex Info` lines about `authoryear.dbx` and `biblatex-dm.cfg` are normal probe messages, not errors) |
| overfull boxes **over 10 pt** | **none** |
| overfull boxes under 10 pt | was 4; now **3** (`\hbox` 1.35 pt, 4.40 pt, 4.87 pt -- the `\vbox` 8.64 pt around `Tikz_construct`/`Alg_construct` is gone) |
| underfull boxes | 2, both in `sec_model.tex` |
| LaTeX warnings | one: **`No \author given`** (the `` `!h' float specifier changed `` warning that D19/D20 work surfaced was removed by setting `TikzHullFigure.tex` to `[!ht]`) |

The build is in good shape. `\author{}` is genuinely absent from `paper.tex` —
left for the author to fill with names and affiliations rather than silenced
with an empty argument.

---

## 2. Body word count

Captions, tables, figures, algorithms, display math and the bibliography
excluded; `detex -n` over the prose.

| file | section | words |
| --- | --- | ---: |
| `sec_abstract.tex` | (abstract) | 247 → **251** |
| `sec_intro.tex` | 1 Introduction | 488 → **489** |
| `sec_litreview.tex` | 2 Related work | 324 → **323** |
| `sec_problem.tex` | 3 Problem description | 285 |
| `sec_model.tex` | 4 The 3D-SCPTOP formulation | 1 074 |
| `sec_decomp.tex` | 5 Why branch and price… | 596 |
| `sec_master.tex` | 6 The master problem | 1 480 → **1 496** |
| `sec_pricing.tex` | 7 The pricing problem | 865 → **858** |
| `sec_branching.tex` | 8 Branching… | 1 854 |
| `sec_experiments.tex` | 9 Computational results | 2 315 → **2 295** |
| `sec_conclusions.tex` | 10 Conclusions | 265 → **256** |
| `sec_appendix_scptop.tex` | A The SCPTOP benchmark | 227 |
| `sec_appendix_bench.tex` | B The 36 instances | 16 |
| | **total** | **10 036** → **10 020** |

Against the 9 000–10 000 budget: was **36 words over the ceiling**, now
**20 over** — the resolution pass paid for every clause it added by cutting a
repeated one (see **Repetition**), and the paper is 16 words shorter than
before it. The remaining 20 have to come out of prose the author chooses:
nothing mechanically duplicated is left.

Sensitivity, so the author can pick the convention a journal asks for:

| reading | words |
| --- | ---: |
| body, display math excluded (above) | 10 036 |
| body, display math counted as text | 11 491 |
| body minus abstract | 9 789 |
| body minus abstract and appendices | 9 546 |

Subsection detail is in the per-section breakdown the counter prints; the two
heaviest subsections are §9.5 *Is a volumetric capacity adequate?* (802 words,
including its closing paragraph) and §6.3 *The restricted master and the
reduced cost* (415).

**I cut nothing.** A duplicate-sentence sweep over all 299 body sentences
(Jaccard ≥ 0.55 on content words) found exactly one near-duplicate pair, and it
is the abstract against contribution 2 of the introduction — a legitimate
restatement, not a duplicated sentence. There is nothing mechanical to delete;
the 36 words have to come out of prose the author chooses. §9 and §8 are where
the slack is (see **Repetition**).

---

## 3. Figure, algorithm and table inventory

### Figures — six, not four

| # | page | label | file | state |
| --- | --- | --- | --- | --- |
| 1 | ~~10~~ **9** | `fig:blocks` | `Tikz_blocks.tex` | legible; float drift **fixed** (D20) — now printed p.9, inside §5 |
| 2 | 12 | `fig:loop` | `Tikz_loop.tex` | legible, dense but clean |
| 3 (3a, 3b) | 16 | `fig:truck`, `fig:truck-floor`, `fig:truck-stack` | `Tikz_truck.tex` | legible; subfigures correctly numbered |
| 4 | 16 | `fig:hull` | `TikzHullFigure.tex` | **was broken, now fixed** — see §4 |
| 5 | 18 | `fig:tree` | `Tikz_tree.tex` | legible, best figure in the paper |
| 6 | 20 | `fig:construct` | `Tikz_construct.tex` | legible; the densest, four stacked panels, still readable at print size |

All six were rendered at 110 and 300 dpi and read. Figures 1, 2, 3, 5 and 6 have
no overlap, no clipping and no unreadable type. Figure 6's internal annotations
are the smallest type in any figure and sit at the edge of comfortable, but they
are legible.

### Algorithms — three

| # | page | label | file |
| --- | --- | --- | --- |
| 1 | 13 | `alg:certify` | `Alg_certify.tex` — `Certify(Ω̄,R)` |
| 2 | 19 | `alg:bp3d` | `Alg_tree.tex` — BP-3D-SCPTOP |
| 3 | 21 | `alg:construct` | `Alg_construct.tex` — `Construct(Ω̄,R)` |

All three typeset correctly. In Algorithms 2 and 3 a few `\Comment` markers
(`▷`) are pushed to the right margin of one line while their text wraps to the
next — cosmetic, present on lines 1 and 3 of Algorithm 2.

### Tables — twelve

| # | page | label | file |
| --- | --- | --- | --- |
| 1 | 3 | `table:ComProTra` | `tab_positioning.tex` |
| 2 | 3 | `tab:sb-Nomenclature` | `tab_nomenclature.tex` |
| 3 | 8 | `tab:rowmap` | `tab_rowmap.tex` |
| 4 | 23 | `tab:protocol` | `tab_protocol.tex` |
| 5 | 23 | `tab:quantities` | `tab_quantities.tex` |
| 6 | 24 | `tab:volumetric` | `tab_volumetric.tex` |
| 7 | 25 | `tab:3d-onecommit` | `tab_ladder.tex` — now **two blocks**, full type (D19) |
| 8 | 25 | `tab:fleetfloor` | `tab_fleetfloor.tex` |
| 9 | 26 | `tab:summary` | `tab_summary.tex` |
| 10 | 27 | `tab:3d-head` | `tab_head.tex` |
| 11 | 27 | `tab:repack` | `tab_repack.tex` |
| 12 | 30 | `tab:bench` | `tab_bench.tex` |

Every `rewrite/*.tex` file is `\input` by something; there are no orphans.

**Arithmetic I re-derived and found correct.** (The resolution pass changed no
number except the three readings of the same 0.198 s under D3, and re-checked
the twelve of D9/D10 against their files.) Table 9 reproduces Table 12 cell
for cell — all four factor blocks, all four load totals (363+361, 219+505,
246+235+243, 182+239+303 = 724), the plan counts (31/36), the gap-row counts
(30), the mean gap (8.799 %) and the census split (501 + 174 + 49). Every gap in
Tables 6, 7 and 10 recomputes to the printed figure under the stated
`(z−LB)/z`; every Δz in Table 6 recomputes under the stated
`(z_DW−z_compact)/z_compact`; Table 11's 27.0 % / 24.5 % recompute against the
re-packed plan, and its "worth twelve and forty-four trucks" against 86−74 and
256−212. The 2 296 in §9.5 is 70 435.04 − 68 139.17 and is correctly attributed
to "that model's own bound". I found **no wrong arithmetic anywhere**.

---

## 4. What I fixed

**`TikzHullFigure.tex` — Figure 4 was broken.** Three pieces of text were
struck through by the dashed hull frontier: the red callout
"(74,126): the load a twelve-truck plan needs", the rotated label
"frontier of conv(Ωˢ)", and the annotation "for every π, the maximum of πᵀa is
attained at a vertex of the frontier", which the frontier crossed between
"maximum" and "frontier". The arrowhead of the π vector also overprinted the
"f" of "of" in the rotated label.

Fixed by (i) giving the red callout and the rotated label a white backing
(`fill=white, inner sep=1.5pt` placed **after** the colour key, so the text
colour survives), (ii) moving the "for every π" annotation from (96,14) to
(56,24) with a 36 mm text width so it clears the frontier rather than masking
its last segment, and (iii) moving the π arrow from (150,52) to (108,52), which
keeps it inside the hull and clear of both labels. Re-rendered at 300 dpi and
confirmed clean. No numbers, no caption text and no prose changed.

**`inflight-sites.md` copied into `rewrite/`.** `paper.tex:14` tells the reader
that every `\InFlight` site "is listed with its replacing campaign in
`rewrite/inflight-sites.md`", and that file was not in `rewrite/`. It is now.
(One stale figure in it: it says "38 marked lines, 146 marked values"; the
current files carry **146 marked values on 37 lines**.)

Nothing else was changed by the referee pass. In particular it did not touch a
single number.

**The resolution pass** then fixed D1–D13 and D15–D20, touching
`sec_abstract.tex`, `sec_intro.tex`, `sec_litreview.tex`, `sec_model.tex`,
`sec_decomp.tex`, `sec_master.tex`, `sec_pricing.tex`, `sec_branching.tex`,
`sec_experiments.tex`, `sec_conclusions.tex`, `Alg_construct.tex`,
`Alg_tree.tex`, `Tikz_blocks.tex`, `TikzHullFigure.tex`, `tab_ladder.tex`,
`tab_bench.tex`, `tab_volumetric.tex`, `tab_protocol.tex`,
`tab_quantities.tex`, `tab_nomenclature.tex`, `tab_rowmap.tex` and
`inflight-sites.md` — twenty-two files, all inside `rewrite/`. **No `\InFlight` marker was added or removed**: 146
values, verified by `grep` after the fact. They now sit on 42 lines rather than
37, because Table 7's five rows are set over two blocks;
`inflight-sites.md` has been regenerated with the new line numbers and its
stale "38 marked lines" corrected.

---

## 5. What the author should look at first

*(All six are now fixed except D14, which is a decision and not a defect; the
resolutions are under each entry in section 6.)*

1. **§9.5, "both halves of the evidence point up"** — the one claim in the
   paper that goes past the evidence. Defect D1.
2. **The conclusions' 27.0 % / 24.5 % sentence** — the denominator flips
   between §9.5 and §10 and the conclusion is the one a reader quotes. Defect D2.
3. **`a_{ωc}` on page 13** — an undefined symbol in the paper's central bound
   statement, and the commodity index `c` it uses is not introduced until the
   next section. Defect D4.
4. **`0.18 s` vs the table's `0.2 s`** — the same quantity, two values, three
   places. Defect D3.
5. **`I_8_5_2_90` appears in Table 7 and Table 12 with different node counts**
   (67 vs 68) and different gap precision, from two different runs, and the
   paper never says they are two runs. Defect D6.
6. **146 numbers are still `\InFlight`** — including every cell of Table 7, the
   method half of Table 6, and twenty prose figures in §9. The paper reads as
   finished and is not; decide whether it goes out with provisional numbers
   marked or waits for the two campaigns. Defect D14.

Decisions 14 and 15 are both honoured and need no work — see §8. The
resolution pass added no word that touches either: "matheuristic" still appears
nowhere, the method is still **BP-3D-SCPTOP** everywhere it is named, and the
only sentence about a procedure in industrial use is still the single one in
the conclusions.

---

## 6. Defects, and how each was resolved

### Claims stronger than `paper-results.md` allows

**D1 — `sec_experiments.tex:211-217`, "both halves of the evidence point up".**
The two halves are: re-running the oracle at 2.5× the limit decided twenty more
loads, *all twenty placeable*; and the oracle carries no payload row, so
over-payload loads come back undecided rather than refuted. The second points
up. The first does not — it left the proved count unmoved at 501 and raised the
placeable count. `paper-results.md` is explicit: *"the two pieces of evidence
pull opposite ways and the net is unknown"*. Everything else in the paragraph is
right and well framed (501/724 led as the floor, 91.1 % demoted to "an upper
reading, drifting downward with compute" — exactly the swap the audit asked
for). Only the clause "and both halves of the evidence point up" overstates.
Suggested replacement is one clause, not a rewrite: *and the net of the two is
unknown*.

**FIXED** (`sec_experiments.tex`). The clause now reads *and the net of the two
pieces of evidence below is unknown*. Settled by `paper-results.md:575`, which
states it in those words; the asymmetry is in `julia/docs/what-may-be-claimed.md`
(Python's `pack_load` carries no payload row) against the 20-of-20-placeable
re-run, so the two do pull opposite ways. 501/724 stays the floor.


**D2 — `sec_conclusions.tex:13-17`, the saving's denominator flips.**
§9.5 states it correctly: savings "of 27.0 % and 24.5 % on trucks … each
denominator the re-packed plan's own figure", i.e. 20/74 and 52/212. The
conclusion says re-planning "costs 27.0 % and 24.5 % **of the trucks the method
needs**" — against 54 and 160 that is 37.0 % and 32.5 %, so the sentence names a
denominator the number does not use. This is the review history's signature
defect (a 28 % "premium" that was a 38.9 % saving); fix it in the conclusion,
not in §9.5.

**FIXED** (`sec_conclusions.tex`). The sentence now reads *the method plans the
same quantities with 27.0 % and 24.5 % fewer trucks than re-packing two of its
plans needs*, naming the re-packed plan as the denominator in the sentence a
reader quotes. Checked against `results/repacked_volumetric.json`:
20/74 = 27.03 %, 52/212 = 24.53 %; 20/54 and 52/160 would be 37.0 % and 32.5 %.
§9.5 untouched.


**D3 — `0.18 s` for the compact volumetric optimum on `I_8_5_2_90`.**
The recorded value is **0.198 s**. Table 6 prints `0.2`; `sec_experiments.tex:191`
and `tab_ladder.tex:26` both print `0.18 s`. Two of the three are wrong and the
paper contradicts itself in print. Pick 0.198 or 0.20 and use it in all three.

**FIXED**, as **0.198** in all three (`sec_experiments.tex`, `tab_ladder.tex`
caption, `tab_volumetric.tex` row 1). The stated correction is right, but the
file carries **two fields**:
`results/compact_ref/compact_ref_I_8_5_2_90.json` has `wall = 0.198167` and
`runtime = 0.184549`. 0.198 is `wall`, and it is the value `paper-results.md`
records (lines 154, 328, 396, 523), so that is the one used. Table 6's other
five `t` cells are those files' `runtime` (3242.96, 1349.44, 1112.70, 2087.63,
2399.01 → 3243, 1349, 1113, 2088, 2399), so row 1 is now the one cell in that
column read from `wall`. The two fields differ by under 1 % on every row, and a
header comment in `tab_volumetric.tex` records which is which so the author can
put the whole column on one field if a referee asks.


**D13 — `sec_abstract.tex:21-24`, the 8.8 % is attached to the wrong
population.** The abstract reads "returns a verified plan on thirty-one at a
mean certified gap of 8.8 %". §9.3 states it properly: thirty-one plans,
thirty-five proved bounds, "a mean certified gap of 8.8 % over the thirty rows
with both". The abstract's phrasing invites the reading that the mean is over
the thirty-one. Two words fix it.

**FIXED** (`sec_abstract.tex`): *a verified plan on thirty-one, at a mean
certified gap of 8.8 % **over thirty of them***. 31 plans, 35 proved bounds,
30 with both (`tab_summary.tex`, recomputed from the 36
`results/bench_tree/*.json` and `results/loads_recheck.csv`).

**D15 — `sec_intro.tex:56-59`, "within a hundredth of a percent".** The two ends
are 0.0006 % and 0.0114 %. §9.2 says "within *about* a hundredth of a percent",
which is fair; the introduction drops the hedge and the lower end is 14 % above
the figure claimed. Add the hedge or quote both numbers.

**FIXED** (`sec_intro.tex`): *bracket that optimum within **about** a hundredth
of a percent*, matching §9.2 word for word. +1 body word.

**D16 — `sec_conclusions.tex:26-29`, "The residual gap on the smallest instances
sits in the column variables".** Nothing in the paper supports this and nothing
in `paper-results.md` measures it. §8's own account of the hierarchy says the
delivery rung is entered "only where the first two have nothing left to say",
and §9.4 attributes the `I_8_5_2_90` residual to three unrefuted regions
certified at 18 795.54–18 795.57. Either cite the evidence or state it as a
conjecture.

**FIXED** (`sec_conclusions.tex`), by dropping the unsupported location claim
rather than hedging it: the sentence is now *Closing the regions the search
leaves unrefuted may need a branching rule on **pairs** of columns, untested
here.* Its premise is the preceding sentence of the same paragraph and §9.4, and
nothing is asserted about where the gap lives. −9 body words.

### Figure or table versus text

**D4 — `a_{ωc}` is undefined, and the commodity index arrives a section late.**
`sec_master.tex:206` (printed at the top of p.13) defines the certificate as
`ζ_gt := max_ω Σ_c π_ct a_{ωc}`. The paper's column images are `b_{ωn}` and
`e_{ωr}` (§6.1); the commodity-indexed form is `image_c`, and `c` itself —
"products when g = p, RTI types when g = s" — is not introduced until
`sec_pricing.tex:10-15`, i.e. §7. So the central bound statement of §6.4 uses
two notations that do not yet exist, one of which (`a_{ωc}`) exists nowhere.
`a_{kc}` in §8.1 is a third, different object (per-truck counts in the partition
MIP). Either define `image_c` and `c` in §6.1 and write `image_c(ω)` here, or
introduce `a_{ωc}` explicitly as the column's image.

**FIXED** by the first route, which also removes one of the four meanings of
`a` in D11. §6.1 (`sec_master.tex`) now closes the column definition with
*writing `image_c(ω)` for the entry of `b_ω` or `e_ω` at the **commodity** `c`
--- a product when g = p, an RTI type when g = s*, and §6.4's certificate reads
`ζ_gt := max_ω Σ_c π_ct image_c(ω)`. `sec_pricing.tex` no longer defines `c` a
second time: it points at §6.1 and keeps only the data mapping, which now also
covers `r_gc` (see D12). Net −3 body words.


**D5 — Table 1 has no empty column.** `sec_litreview.tex:21-22` says "the column
that is empty throughout is the one this paper fills". In Table 1 the Capacity
column is populated on all thirteen prior rows (V, UH, V|W) and 3D-SCPTOP's
entry is "3D loading"; the nearest-to-empty column is RTIs ("—" on 11 of 13),
which is not the one the paper fills. A referee will look. Reword to something
the table shows, e.g. that no prior row's capacity is a placement condition.

**FIXED** (`sec_litreview.tex`): *and in no prior row is the capacity a
condition on placement*. Verified against `tab_positioning.tex`: the Capacity
column is V, UH or V|W on all thirteen prior rows and **3D loading** on
3D-SCPTOP's. −1 body word.


**D6 — `I_8_5_2_90` carries two sets of "the" results.** Table 7 row 1:
nodes 67, open 0, gap 0.0076 %, wall 873/900. Table 12 row `I_8_5_2_90`:
nodes 68, gap 0.01 %, same z, trucks and LB. These are two runs on two commits
(the ladder and the 36-instance benchmark) and the paper never says so, so the
printed difference reads as an inconsistency. Say in one clause that the first
ladder rung and one benchmark row are the same instance under two runs — or
drop one. The gap precision also differs (4 dp vs 2 dp) for what looks like one
number.

**FIXED in the captions, not by aligning the numbers.** `tab_ladder.tex`'s
caption now states that row 1's instance is also row `I_8_5_2_90` of Table 12,
**two runs on two commits** (73411b9 at the ladder budgets here,
0c0c16c at 900 s there), agreeing on z, trucks and bound and differing at 68
nodes against 67, with the gap printed there to two decimals rather than four;
`tab_bench.tex`'s caption carries the one-clause pointer back. Verified:
`results/fleet_tree_3d/tree_3d_I_8_5_2_90_table_900.json` `nodes = 67`,
`results/bench_tree/tree_3d_I_8_5_2_90.json` `nodes = 68`, both
`incumbent 18796.96`, `trucks 12`, `global_lb 18795.5396`.


**D7 — Table 7 row 1 reports `open = 0` and `dropped = 0`, and §9.4 reports
three regions the search could not refute.** Under §8's own stated conditions
("optimality is claimed only when the root is certified, no region remains open,
no restart has broken the partition and the incumbent meets the global bound"),
an exhausted stack with a certified bound below the incumbent needs an
explanation the paper does not give. Separately, "certifying the three regions
its search could not refute at 18 795.57, 18 795.54 and 18 795.54, **none
proved**" reads most naturally as "none of those certifications was proved",
which would contradict Table 7's `LB (cert.)` column and its header note that
`vol_lb_proved` is true on all five rows. If "none proved" means "no region was
refuted", say that; as written it undercuts the bound the table prints.

**FIXED, both halves — and the review's reading of "none proved" was the right
one.**

*The `open = 0` half.* `open_regions` counts the stack and the deferred list and
is honestly 0; the regions that remain unrefuted are **frontier** entries,
closed with a certified bound *below* the incumbent
(`results/fleet_tree_3d/STATE.md:520-545`: three of the thirty-three
`LB < incumbent, open` exits fix all ten (g,t) pairs, and inside them the
construction is a heuristic over a restricted pool). An exhausted stack is not
an exhausted space. `tab_quantities.tex` (`nodes / open`) and `tab_ladder.tex`'s
caption now say so --- *a run with none has not refuted it either, a region
closed with a bound below the incumbent being bounded and not excluded*. The
conclusions' *"the search ends with regions open"*, which contradicted Table 7
the same way and the review did not flag, now reads *ends with regions
unrefuted*.

*The "none proved" half.* It does mean *none of those three certifications came
back proved*, and it does **not** touch the bound the table prints, because they
are a different object: a **geometric** leaf certification under the leaves' own
duals, in a **separate run** —
`results/fleet_tree_3d/tree_3d_I_8_5_2_90_leafcert_900.json`
(`leaves_certified 3`), log lines 151–153, *leaf certification 18795.57 /
18795.54 / 18795.54 proved=False; not refuted* — while Table 7's `LB (cert.)` is
the **volumetric** certificate (`vol_lb_proved: true`). `sec_experiments.tex`
now reads *geometric certification of the three regions its search could not
refute returned …, none of the three proved*, with a source comment naming both
files.


**D8 — Table 4 declares one placeability budget for two censuses.** The caption
correctly says the two censuses differ in oracle *and* in limit, but the table
has a single row, "Placeability census | CP-SAT | 300 per load". The ladder
census ran `pack_witness` at 60 s. Add the second line or the limit is
undeclared for half the census — in the table whose whole job is declaring

**FIXED** (`tab_protocol.tex`): the one line became two --- *Placeability
census, 36 instances | CP-SAT | 300 per load* and *Placeability census, size
ladder | shelf + CP-SAT | 60 per load*. The 60 s is from `tab_repack.tex`'s own
header (`results/volumetric_loads_recheck.json`, `pack_tl 60 s`, oracle
`pack_witness`).

budgets.

**D9 — numbers in §9.3 with no table and no entry in `paper-results.md`.**
"the shelf design placed 24 686 loads and the constraint programme 246, which
proved 4 327 of the 11 866 distinct compositions proposed unplaceable and left
397 undecided" (`sec_experiments.tex:162-165`); the three peak resident sets
"6.1, 2.7 and 4.5 GB" (`:183-184`, repeated in Table 10's caption); "column
generation takes 864 s of the 873 s first rung" (`:160`); and "on one of the
five, no time limit" (`:17-18`, repeated in Table 4 and Table 6). Each is
plausible and none contradicts anything, but none is in the audit's inventory of
checked values, so each needs its source file named before submission.
`inflight-sites.md` lists the packing counts among the numbers "deliberately
unmarked", which confirms they are held to be final — so they need a citation,
not a campaign.

**FIXED: every one re-derived from the files, and the file named at the site in
a source comment** --- the paper's existing convention for tables, and it adds
no visible word.

* packing counts, summed over the 36 `results/bench_tree/tree_3d_*.json`:
  `stats.pack_ok_shelf` **24 686**, `stats.pack_ok_cpsat` **246**,
  `stats.distinct_parts` **11 866**, `stats.distinct_parts_unplaceable`
  **4 327**, `stats.distinct_parts_undecided` **397**. All five exact.
* peak resident sets, `results/compact_3d/compact3d_*.json`, `peak_rss_gb`:
  6.1205 (`I_8_10_2_90_k25`), 2.6528 (`I_12_12_2_90_k25`), 4.4501
  (`I_16_15_2_90_k25`) → **6.1, 2.7, 4.5** in Table 10's row order. The same
  files give the 67 674 / 92 092 and the 119 424–322 734 the prose quotes.
* 864 s of 873 s: `tree_3d_I_8_5_2_90_table_900.json`, `t_cg 864.163`,
  `seconds 873.099`.
* "on one of the five, no time limit": `results/hp/provenance.json`, `tree`
  block --- `tree_I_24_20_2_90_k80.json` has `"time_limit": null`, the other
  four 1800.0.


**D10 — `catalogue_realism.json` computes "volumetric credit / placeable count";
§9.5 defines `θ` with an *analytic* denominator.** `sec_experiments.tex:294-296`
leans on that distinction — "The denominator is a grid count with no packing
oracle run, hence an *upper* estimate of what fits, so θ > 1 is a conservative
diagnosis". If the file's denominator is an oracle-derived placeable count, the
conservatism argument does not hold and the median 1.000 / max 2.000 / "no type
has θ < 1" sentence changes meaning. Check `experiments/catalogue_realism.py`
against \eqref{eq:theta} before this goes out; it is the paper's only
practitioner-facing test.

**CHECKED --- no defect; the paper is right and nothing was changed.** The
script's denominator is `placeable = min(grid, payload_layers)` with
`grid = slots * min(Lh, Lc)` and `slots` the better of the two
single-orientation floor grids: **purely analytic, no oracle is run**
(`experiments/catalogue_realism.py:31-56`, and its docstring says so in terms).
That is \eqref{eq:kappa} term for term, so *"an upper estimate of what fits,
hence θ > 1 is a conservative diagnosis"* holds. The variable is merely *named*
`placeable`, which is what made the file look oracle-derived.
`results/catalogue_realism.json` confirms the quoted figures: 15 types, median
1.000, max 2.000, four at ≥ 1.2, `n_under = 0`.


### Symbols

**D11 — symbols defined twice with different meanings.**

- **`θ`**: the full-truckload resource selector `θ_gkt ∈ {0,1}`
  (`sec_model.tex:188`, and again as `θ` in the pricing problem and the
  appendix) **and** the analytic credit ratio `θ_r`
  (`sec_experiments.tex:283`). Two unrelated objects, one letter, both central.
  Rename the ratio.
- **`λ`**: the objective weights `λ^K, λ^I` (`sec_model.tex:59`) and the master
  multipliers `λ^g_{ωt}` (`sec_master.tex:64`).
- **`ω`**: the column index and the family-maximum weight `ω_{gr}`
  (`sec_model.tex:218`, `sec_pricing.tex:77`).
- **`a`**: RTI footprint area `a_r` (`sec_model.tex:325`), column image
  `a_{ωc}` (D4), partition counts `a_{kc}` (`sec_branching.tex:142`), plant
  arrivals `a_nt` (`sec_model.tex:71`).
- **`f`**: the row function "has `f` constant within a class"
  (`sec_master.tex:184`) and the footprint class
  (`sec_branching.tex:244`).
- Minor: `s` as the supplier direction, `s_r` in \eqref{eq:theta} and `s_{kf}`
  the stack counts; `r` as RTI type, `rK` the trailer height and `r_{gr}` the
  RTI height; `v` as the volumetric granularity, `v_{in}` the per-slot loads and
  `v` the load in the ray bisection (`sec_branching.tex:228`); `z` as objective,
  `z_k` as the partition's floor selector.

**FIXED for the four central collisions, plus two of the minor ones; the rest
left, with the reason.**

| was | now | sites |
| --- | --- | --- |
| `θ_r`, the credit ratio | **`κ_r`** (label `eq:theta` → `eq:kappa`) | `sec_experiments.tex` §9.5, 7 places |
| `λ^K, λ^I`, the objective weights | **`η^K, η^I`** | `sec_model.tex`, `sec_master.tex`, `sec_pricing.tex`, `tab_rowmap.tex`, `tab_nomenclature.tex` |
| `ω_{gr}`, the family-maximum weight | **`ŵ_{gr}`** | `sec_model.tex`, `sec_pricing.tex`, `tab_nomenclature.tex` |
| `f`, the row function | **`φ`** | `sec_master.tex`, `sec_branching.tex` ×2 |
| `a_{ωc}`, the column image | **`image_c(ω)`** (D4) | `sec_master.tex` |
| `v`, the load in the ray bisection | **`a`**, matching Algorithm 3's `⌊σa_k⌋` | `sec_branching.tex` |
| `z_k`, the partition's floor selector | **`θ_k`** | `sec_branching.tex`, \eqref{eq:part-floor} ×4 |

`z_k → θ_k` is a merge rather than a rename: that binary **is** the
full-truckload resource selector of \eqref{sb:eq:util-p}, same convention
(1 = volume), so the paper now has one symbol for one object instead of two.

**OPEN, deliberately:**

* **the remaining three `a`s** — footprint area `a_r`, partition counts
  `a_{kc}`, pipeline arrivals `a_{nt}`. The one that did harm was `a_{ωc}`,
  because it was *undefined* in the paper's central bound statement; it is gone.
  Each of the other three is defined at first use and distinguished by its index
  signature (one RTI-type index, truck × commodity, product × period), and
  `a_k` for a part is what Algorithm 3 already uses. Renaming any of them churns
  without removing an ambiguity.
* **`r` / `rK` / `r_{gr}`** — `pK, qK, rK` is a declared three-symbol family for
  the trailer's dimensions (`tab_nomenclature.tex`), carried from the original
  paper; renaming it touches roughly fifty sites and loses the mnemonic.
* **the `s` family** — `g = s` is a *value* of `g` and never appears
  subscripted, so it cannot be read as `s_r` or `s_{kf}`, each of which is
  defined in the sentence that introduces it.

**D12 — symbols used and never defined.** `ε₂` and `tol` in Algorithm 3
(lines 5 and 6); `X₂` in Algorithm 2 line 27 (`Alg_tree.tex:35`, where context
says it is the newly constructed plan's fleet total — `X̂` would be consistent
with the `n̂` beside it); `ρ(c)` in `sec_branching.tex:252`, where `ρ` is defined
only on products but `c` ranges over RTI types when `g = s`.

**FIXED, all four.**

* `ε₂` and `tol` are now defined in Algorithm 3's caption: *ε₂ is step 2's
  relative MIP gap and `tol` the tolerance above which artificial slack means
  the region admits no plan over this pool*. (A caption, so no body words.)
* `X₂` is **deleted** rather than renamed — the line now reads *push the three
  regions of level 1 around `Σ_{g,t} n̂_{gt}`*. `X̂` would have collided with
  `X̂_{gikt}`, the slot-used indicator of \eqref{sb:eq:agg-X}, which is why the
  review's suggested `X̂` was not used; the symbol had exactly one occurrence, so
  writing the sum out costs nothing.
* `ρ(c)` is gone: \eqref{eq:stackrows} now writes `r_{gc}`, and the commodity
  convention in `sec_pricing.tex` was extended to carry `r_{gc}` alongside
  `image_c`, `w_c` and `γ_{gc}`. This is the exact reading — for `g = s` the
  commodity *is* the RTI type and `ρ` would have had to be the identity.

### Structure and presentation

**D17 — §4's subsections are unnumbered except one.** `sec_model.tex` uses
`\subsection*` seven times and `\subsection` once (line 290, "Symmetry,
slot-set sizing and data assumptions"), which prints as the document's only
§4.x. It cannot simply be starred: `sec_pricing.tex:89` refers to it as
"Section 4.1". §6, §8 and §9 number their subsections, §4 and §7 do not, so the
document is inconsistent at the level a copy-editor will query. Author's call,
which is why I left it.

**FIXED** by numbering, which is the direction the rest of the document already
goes: the seven `\subsection*` in `sec_model.tex` and the one in
`sec_pricing.tex` are now `\subsection`. §4 prints 4.1–4.8 and §7 prints 7.1;
`sec_pricing.tex`'s reference to the symmetry subsection resolves to **4.7**,
which is what settles D18's second half as well.

**D18 — labels on unnumbered units resolve to their enclosing section.**
`sec:initcols` → "8" (same as `sec:branching`), so `sec_pricing.tex:145` prints
"Sections 8 and 8.2"; `sec:pp-dir` → "7" (same as `sec:pricing`), so
`tab_rowmap.tex:6` points the reader at the whole of §7. Both resolve, neither
is wrong, both are imprecise.

**FIXED.** "The initial pool" is now `\subsection{The initial pool}`, so
`sec:initcols` → **8.1**, `sec:relaxfix-flow` → **8.2**, `sec:geo3d` → **8.3**,
and `sec_pricing.tex` prints "Sections 8.1 and 8.3". `sec:pp-dir` → **7.1**
(D17), so `tab_rowmap.tex` now points at a subsection. Both verified in
`paper.aux` after a clean build.

**D19 — Table 7 is the smallest type in the paper.** Twelve columns inside
`\resizebox{\textwidth}` shrink to roughly 5 pt, visibly below Table 8 on the
same page. It is still readable at 300 dpi but it is the one table a referee
will complain about. Consider dropping `dropped` (0 on all five rows) and
folding `nodes`/`open` into one column.

**FIXED, but not that way: dropping `dropped` would have removed five
`\InFlight` markers, which is not allowed.** The twelve columns are instead set
as **two blocks of the same five rows in the same order**, so the whole table
runs at full `\small` with **no `\resizebox` at all** — from ≈ 5 pt to 9 pt,
now the *largest* type among the results tables rather than the smallest, and
on the same page as Table 8 as before. Measured: the single tabular is
678.3 pt wide against a 433.6 pt text block (scale 0.64); the two blocks are
319.9 pt and 398.4 pt, both of which fit unshrunk. All 55 `\InFlight` calls of
that table are preserved — 146 in the paper, unchanged. A `sidewaystable` was
tried first and rejected: it reads well but costs a page.

**D20 — Figure 1 drifts out of its section.** Referenced on p.8 in §5, printed
on p.10 between §6.1 and §6.2. A `[t]` on the float or moving the `\input`
earlier would hold it; I left the placement alone because the fix interacts with
Table 3 on p.8.

**FIXED.** `\input{Tikz_blocks}` was moved to immediately after the sentence
that references it, the float set to `[!t]`, `\input{tab_rowmap}` moved to the
end of §5 (it is a `longtable`, placed where it sits in the text, and it was
occupying the top of the page the figure wanted), and a `\FloatBarrier`
(`placeins` is already loaded) closes the section. Figure 1 now prints on
**p.9, inside §5**, one page after its reference, with Table 3 following it;
§6 still opens on p.10, the paper is still 32 pages and no white gap was
created. The `\vbox` overfull of 8.64 pt went away with it.

### Repetition

The paper is noticeably tighter than the 59-page original and the audit's
"demasiadas vueltas" is largely gone, but four caveats were still stated three
or more times. None is wrong; together they were most of the 36 words over
budget.

**The resolution pass cut the two body duplicates the review called avoidable**,
which is what paid for the clauses D1–D16 added:

* `sec_experiments.tex` §9.2 no longer restates the unequal-budget numbers that
  `tab_volumetric.tex`'s caption gives verbatim; it points at the caption
  (−20 words);
* §9.3's aside *"the only proofs of minimality in this paper, valid in 3D by
  Proposition 2"* is gone, both halves of it being in `tab_fleetfloor.tex`'s
  caption (−15 words).

The other two items below are caption-to-caption or figure-to-text repetition,
where captions must stand alone, so they were left.

- **"valid for 3D by inclusion"** — six places: abstract:14, intro:52,
  `sec_experiments.tex:93` and `:126`, `tab_ladder.tex:17`, `tab_bench.tex:18`,
  plus `tab_fleetfloor.tex:12-14` and the conclusions. Captions must stand
  alone, so some is unavoidable; the two in §9 within thirty lines are not —
  **the second is now cut**.
- **The unequal-budget disclaimer** — `sec_experiments.tex:83-86`,
  `tab_volumetric.tex:22-29`, `tab_protocol.tex` caption, and again in
  `tab_head.tex:17-20` for the other table. Four statements of one idea —
  **the prose one is now a pointer at the caption**, leaving three captions.
- **"one optimum, and the proof is not the method's"** —
  `sec_experiments.tex:188-189`, `tab_ladder.tex:24-27`, `tab_head.tex:30-31`,
  `tab_volumetric.tex:16`; plus "the only proofs of minimality in this paper" in
  both `sec_experiments.tex:125` and `tab_fleetfloor.tex:16` — **the prose copy
  of the second is now cut**. The first stays: the prose statement is the
  primary one and the three captions must stand alone.
- **"full on volume or payload, never the sum of the two ratios"** —
  `sec_model.tex:185-187`, `Tikz_truck.tex:86`, `Tikz_construct.tex:169` and
  `:244`. The rule deserves saying once in §4 and once where the partition
  re-imposes it; four times is three.

---

## 7. What reads as sound

Worth recording, because a review that lists only defects misleads:

- the decomposition argument in §5 — "deleting the quantifier *is* the pricing
  problem" — is carried all the way through §7 and Table 3 without a gap, and
  the equivalence proposition correctly notes it needs no hypothesis;
- §6.4 is the strongest section: the RMP value refused as a bound, the
  certificate's full-sweep requirement, the cutoff-without-a-point case
  distinguished from a time limit, the branching fixes released before the
  sweep, and the Lagrangian bound dismissed with a number;
- the bound hygiene holds everywhere it is tested: the compact 3D dual bound is
  quarantined in its own column in Table 10 and never used as an interval end;
  the one unproved bound prints *unproved* and is excluded from every mean; the
  `(z−LB)/z` denominator is stated once, in §9.1, as the audit asked;
- Table 11's two caveats (the memory-stop row overstated, "at least the
  volumetric quantities, by 80 and 137 units") are both present and correct, and
  the stack-aware baseline's saving is refused outright — the paper's best
  credibility move, kept;
- the 69.2 % floor leads and the 91.1 % "of decided" figure is demoted and
  labelled as drifting, which is the exact reversal the audit demanded.

---

## 8. Decisions 14 and 15

**Decision 14 — exact given compute, not a matheuristic: honoured.** The word
"matheuristic" appears nowhere. The abstract says "the procedure is exact given
compute; what it reports here is an incumbent verified truck by truck against its
own layout, a certified bound, and no proof of optimality"; the introduction
closes with "an exact procedure run under a budget rather than a heuristic …
and proves no optimum"; §8 states the four conditions for claiming optimality
and adds "no run here meets them"; the conclusions repeat "The method certifies
no optimum here". The method is named **BP-3D-SCPTOP** at all 18 places it is named — no
`MATH-3D-SCPTOP`, no `Math-3D-SCPTOP`, no second name for one algorithm. The only loose thread is
D7, which is a reporting ambiguity rather than a framing one.

**Decision 15 — the company comparison is not in this paper: honoured, with no
residue.** No occurrence of "company", "PLC", "case study", "current practice" or
"practitioner" anywhere in the body or any table; the single hit on "baseline"
(`sec_experiments.tex:262`) is the stack-aware volumetric model, not a company
procedure, and it is used to *refuse* a saving rather than to claim one. The
~45 lines of refusal the decision asked to be shrunk are now **one sentence**,
in the conclusions: "And the comparison against a procedure in industrial use is
to be run on instances a plant solves itself, against a plan supplied to it."
That is the single place the decision called for. Nothing is quoted against any
baseline anywhere in the paper.

---

## 9. One thing that is not a defect but needs a decision — **STILL OPEN**

**D14 — the paper is 146 `\InFlight` values away from final.** They sit on 37
lines across five files: every cell of Table 7, the whole method half of
Table 6, the method rows of Tables 10 and 11, and twenty figures in §9's prose.
`\providecommand{\InFlight}[1]{#1}` prints the argument, so the paper reads as
though it were finished — including the abstract's "thirty-one", "8.8 %" and the
conclusions' "27.0 % and 24.5 %", all of which are marked in their source
tables. `inflight-sites.md` (now in `rewrite/`) names the two campaigns that
replace them and lists what is deliberately *not* in flight. Before this goes to
anyone, decide whether the marks render visibly, or the paper waits.

**Left open by the resolution pass, by instruction and on purpose.** Not one of
the 146 was touched, added or removed; they now sit on 42 lines rather than 37
only because Table 7's rows are set over two blocks (D19), and
`inflight-sites.md` has been regenerated to match. The decision is unchanged and
is the author's.
