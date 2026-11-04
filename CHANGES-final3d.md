# Changes for the final 3D results (2026-11-04)

Branch `final-3d-paper` of the paper repository, from `100c362`
(head of `method-update-3d`). Line numbers are those of the files **after**
this change. "Old" is the text at `100c362`.

## Sources

| tag | file | where |
| --- | --- | --- |
| **CSV** | `results/julia/final/ladder_3d_final.csv` | outer repo, branch `final-3d`, committed `4c7471e` |
| **LOG** | `results/julia/final/ladder_3d_final.log` | same |
| **B** | `results/julia/ladder_3d.csv` (campaign B, `src_tree` 246b883, ip_tl 120, no rows) | outer repo, `3d-port` |
| **600** | `results/julia/ip_tl_600.csv` (same code as B, ip_tl 600) | outer repo, `3d-port` |
| **PYL** | `results/fleet_tree_3d/tree_3d_<inst>_table_<tl>.json` (reference ladder, commit 73411b9) | outer repo, `3d-port` |
| **BENCH** | `results/bench_tree/tree_3d_*.json` (36 rows, commit 0c0c16c) | outer repo, `3d-port` |
| **REPACK** | `results/repacked_volumetric.json` | outer repo, `3d-port` |
| **CODE** | `julia/src/{cuts,placeability,geometry,tree,construct}.jl` at `src_tree` e4830fe; `julia/scripts/ladder_3d_final.sh` at f4c3b18; `julia/Manifest.toml` | outer repo, `final-3d` |

Run provenance (CSV/LOG): launcher `ladder_3d_final.sh` at `f4c3b18`
(tag `final-3d-2026-11-04`); `src_tree` e4830fe and `load_path` include on
every row; unit `rti-final`, alone, 2026-11-04 14:22-16:32; 10 threads,
`mem_gb` 20, `certify_regions` ratchet, `geom_rows` both, `ip_tl` =
time_limit/6; `proved` false, `root_proved` true, `complaints` 0 and
`pack_undecided` 0 on every row.

## 1. `tab_ladder.tex` (Table 7) --- every cell, caption and source comment

Source comment (lines 1-22): now names CSV, LOG, `4c7471e`, `f4c3b18`,
`e4830fe`, the unit, the settings, and which CSV column or log line each
table column comes from.

Caption (lines 26-64): was "every row on one commit (73411b9) ... seed 0, run
sequentially"; now "the second implementation ... one source tree (e4830fe)
run alone on one machine, ten threads, a 20 GB per-model cap and no seed set,
with the rows (eq:geomrows)-(eq:fibrerows) in steps 2 and 3 and step 4
limited to one sixth of the budget per call". The `open`/`dropped` sentence
was written for a row 1 with both at 0; now: no row ends with both at 0, row 4
has four open regions with no bound of their own (CSV `regions_unbounded` 4),
row 1 ends with three bounded-unrefuted regions (CSV `leaves_unrefuted` 3).
The cross-reference to Table 12 was "two runs, on two commits ... agreeing on
z, trucks and bound and differing at 68 nodes against 67"; now "two runs of
two implementations ... agreeing on z and trucks, with certified bounds of
18 795.54 there and 18 795.56 here, and 68 nodes there against 301 here"
(BENCH `global_lb` 18795.5396, `nodes` 68; CSV `global_lb` 18795.5578,
`nodes` 301). Added: the wall stops short of the budget because 15 % is
reserved for the frontier sweep (`frontier_share` default 0.15,
`tree.jl:620`).

First block (lines 68-72), old -> new, CSV `incumbent` / `trucks` /
`complaints` / `global_lb` / `gap_pct`:

| row | z | trucks | verified | LB | gap |
| --- | --- | --- | --- | --- | --- |
| I_8_5_2_90 | 18 796.96 -> 18 796.96 | 12 -> 12 | 12/12 -> 12/12 | 18 795.54 -> **18 795.56** | 0.0076 % -> **0.0075 %** |
| I_8_10_2_90_k25 | 38 855.34 -> **38 854.44** | 26 -> 26 | 26/26 | 38 791.14 -> **38 791.19** | 0.165 % -> **0.163 %** |
| I_12_12_2_90_k25 | 52 129.45 -> **52 116.76** | 34 -> 34 | 34/34 | 52 105.25 -> **52 105.31** | 0.046 % -> **0.022 %** |
| I_16_15_2_90_k25 | 78 549.14 -> **79 703.31** | 54 -> **55** | 54/54 -> **55/55** | 68 131.43 -> **68 131.48** | 13.3 % -> **14.5 %** |
| I_24_20_2_90_k50 | 230 594.36 -> **223 944.20** | 160 -> **156** | 160/160 -> **156/156** | 200 013.41 -> **200 013.57** | 13.3 % -> **10.7 %** |

`verified` = `trucks` because `complaints` = 0 (verify_plan plus verify_column
on every dispatched truck, `tree.jl:1493-1520`). Gaps: CSV `gap_pct`
0.0075 / 0.1628 / 0.022 / 14.5186 / 10.686, recomputed as (z-LB)/z.

Second block (lines 82-86). `root (t)` = LOG "root plan" line; `tree` =
(root - z)/root; `nodes`, `regions_dropped`, `open_regions`, `seconds` from CSV:

| row | root (t) | tree | nodes | dropped | open | wall/budget |
| --- | --- | --- | --- | --- | --- | --- |
| I_8_5_2_90 | 18 796.96 (140 s) -> **18 797.59 (142 s)** | 0.00 % -> **0.003 %** | 67 -> **301** | 0 -> **1** | 0 -> **6** | 873 -> **765** / 900 |
| I_8_10_2_90_k25 | 38 865.30 (136 s) -> **38 867.74 (154 s)** | 0.03 % -> **0.034 %** | 68 -> **15** | 0 -> **2** | 35 -> **9** | 883 -> **770** / 900 |
| I_12_12_2_90_k25 | 53 195.84 (459 s) -> **52 215.42 (298 s)** | **2.00 %** -> 0.19 % | 11 -> **24** | 0 -> **5** | 10 -> **6** | 1 761 -> **1 548** / 1 800 |
| I_16_15_2_90_k25 | 78 549.14 (1 181 s) -> **92 251.99 (948 s)** | 0.00 % -> **13.6 %** (bold) | 1 -> **2** | 0 -> 0 | 2 -> **7** | 1 746 -> **1 534** / 1 800 |
| I_24_20_2_90_k50 | 230 594.36 (1 418 s) -> **223 944.20 (2 772 s)** | 0.00 % -> **0 %** | 2 -> **1** | 0 -> **1** | 4 -> **2** | 3 492 -> **3 094** / 3 600 |

LOG lines: 18797.59 at 142.0 s; 38867.74 at 154.4 s; 52215.42 at 298.4 s;
92251.99 (X=67) at 948.4 s; 223944.2 (X=156) at 2771.5 s.
Tree: 0.0034 %, 0.0342 %, 0.1889 %, 13.6026 %, 0.

## 2. `tab_head.tex` (Table 10) --- method columns and interval

Source comment (lines 1-18): names CSV, and records which bound is each
interval's lower end and why (rows 3 and 5 take the method's certificate
because the compact volumetric bounds 51 944.22 and 199 518.04 are lower).

Rows (lines 48-52), method z / trucks / LB / gap, CSV:
18 796.96 / 12 / 18 795.54 -> **18 795.56** / 0.0076 -> **0.0075 %**;
38 855.34 -> **38 854.44** / 26 / 38 791.14 -> **38 791.19** / 0.165 -> **0.163 %**;
52 129.45 -> **52 116.76** / 34 / 52 105.25 -> **52 105.31** / 0.046 -> **0.022 %**;
78 549.14 -> **79 703.31** / 54 -> **55** / 68 131.43 -> **68 131.48** / 13.3 -> **14.5 %**;
230 594.36 -> **223 944.20** / 160 -> **156** / 200 013.41 -> **200 013.57** / 13.3 -> **10.7 %**.
Intervals: [18 796.96, 18 796.96] (unchanged); [38 851.93, 38 855.34 -> **38 854.44**];
[52 105.25 -> **52 105.31**, 52 129.45 -> **52 116.76**]; [68 139.17, 78 549.14 -> **79 703.31**];
[200 013.41 -> **200 013.57**, 230 594.36 -> **223 944.20**]. Caption unchanged.

## 3. `tab_repack.tex` (Table 11)

Source comment (lines 6-15): REPACK `method_trucks` (54/160) marked as the
reference's and not used; row 4 from CSV; savings written out.
Line 47: BP-3D-SCPTOP 54 -> **55**, 160 -> **156** (CSV `trucks`).
Line 49: savings 27.0 % / 19.1 % -> **25.7 % / 17.9 %** and 24.5 % / 16.2 % ->
**26.4 % / 18.6 %**: (74-55)/74, (212-156)/212 on trucks; (97 139.17 - 79 703.31)/97 139.17
and (275 266.21 - 223 944.20)/275 266.21 on cost, REPACK `repacked_trucks`,
`repacked_cost`, CSV `incumbent`.
Caption line 29: "re-packed with the same partition-and-pack machinery the
method uses" -> "with the partition-and-pack machinery of the first
implementation" (the re-pack is a Python script without the new rows).

## 4. `sec_experiments.tex`

| line | old | new | source |
| --- | --- | --- | --- |
| 7-17 | "masters ... compact models are solved with Gurobi 12; the pricing problems and the placeability tests with CP-SAT" | compact models and the first implementation: Gurobi 12 + CP-SAT; the second implementation solves every model with **Gurobi 13** | BENCH/compact JSON `env.gurobi` 12.0.1; `julia/Manifest.toml` Gurobi_jll 13.0.3, Gurobi.jl uses the artifact; `solver.jl:32` |
| 43-52 | CG certified "at the root and at the one region ... and nowhere else, which is the policy every run reported here used" | adds "and at a full-depth leaf, which cannot be split"; policy attributed to the second implementation's runs, Table 12's caption states the first's | `tree.jl:1230-1275`; CSV `region_certs` 78/4/6/0/0; BENCH `region_cert_policy` "always" |
| 108-131 | "Both implement the one procedure of Section 7: a single pricing oracle, exact ..." | says which tables each implementation produced; the first ran **with the heuristic pricing rung and an early stop, without the new rows**, and refutes placeability by a different exact test; no certificate depends on it, the plans of Table 12 do | BENCH `config.ladder` true, `config.stop_early` true on 36/36 |
| 198-200 | "the method returns a verified plan on thirty-one" | "the first implementation, in the configuration Table 12 states, returns ..." | BENCH |
| 211-237 | "same method to twenty periods and **160** trucks"; gaps "0.0076 %--0.165 %"; "jump to 13.3 %" | "the method, in the configuration of Sections 7 and 8, to twenty periods and **156** trucks"; **0.0075 %--0.163 %**; **14.5 % and 10.7 %**; new sentence: fleets agree with the reference ladder on rungs 1-3, **55 vs 54** (79 703.31 vs 78 549.14) on rung 4, **156 vs 160** on rung 5, neither attributed | CSV `gap_pct`, `trucks`, `incumbent`; PYL `trucks`, `incumbent` |
| 239-261 | tree improves "by 2.00 % on the third rung, five node improvements taking 53 195.84 to 52 129.45, and by 0.03 % or nothing on the other four" | **13.6 %** on rung 4 (two level-1 nodes, 67-truck root at 92 251.99 -> 65 -> **55** at 79 703.31), **0.19 %** on rung 3 (eleven improvements), **0.034 %** / **0.003 %** on rungs 2 / 1, **none** on rung 5 (root plan after one failed construction, under 290 s before the search window closed, one node, X <= 155 no plan) | LOG "root plan", "NEW INCUMBENT", "root construction failed (attempt 1)", "X<=155: no plan in region"; CSV `nodes`; window 0.85 x 3600 = 3060 s |
| 263-314 | sweep "2.5 to 111.8 s over the forty-one runs, every pair proved on forty"; "column generation takes **864 s of the 873 s** first rung ... on the two crush-binding rungs the partition and its placeability calls dominate" | sweep **2.5-90.2 s** into the run over the 36 of Table 12 (proved on 35), **1.3-13.8 s** after root CG on the five ladder rungs; placeability **0.2-14.2 s** per rung against **120-872 s** of CG; root constructions **669 s** (rung 4), **919 / 938 s** (rung 5, the first ending on step 4's 600 s); ladder placeability **8 335 calls, 6 749 shelf, 485 exact test, 1 101 proved unplaceable, 0 undecided**, none refuted by the arithmetic tests alone; rows ablation on rung 5: without the rows **160 trucks at 230 020.08, 1 440 loads proved unplaceable**, with them 156 and 913 (one run each), reported as counts, the cost difference named as 2.6 % below the run without the rows, (230 020.08 - 223 944.20)/230 020.08 | BENCH trace "volumetric relaxation LB" line; LOG timestamps; CSV `t_cg`, `t_pack`, `pack_*`, `ref_*`; 600 `trucks`, `incumbent`, `pack_unplaceable`. `t_build` deliberately not used (see section 9) |
| 329 | `\InFlight{0.102\%}` | 0.102 % (unchanged value: method z on rung 1 is unchanged) | (18 816.13 - 18 796.96)/18 796.96 |
| 339-360 | "the method's own attempt failed: geometric certification of the three regions ... 18 795.57, 18 795.54, 18 795.54, none proved" | the run of Table 7 ends with three regions covered by certified volumetric bounds **18 795.56, 18 795.54, 18 795.54**, bounded not refuted; the geometric certification is now attributed to a separate run of the first implementation | LOG lines 30, 46, 120; CSV `leaves_unrefuted` 3; `tree_3d_I_8_5_2_90_leafcert_900.json` |
| 393-394 | "the stronger oracle of Section 8.3" | "the first implementation's stronger oracle" (the census is Python) | `volumetric_loads_recheck.json` |
| 404-410 | re-packed "with the same machinery the method uses"; method plans **54** and **160**; savings **27.0 / 24.5 %** trucks, **19.1 / 16.2 %** cost | machinery "of the first implementation"; **55** and **156**; **25.7 / 26.4 %**; **17.9 / 18.6 %** | as section 3 |
| 440 | bound "sits 13.3 % below" | "sits **14.5 % and 10.7 %** below" | CSV `gap_pct` |

## 5. `sec_branching.tex` --- the geometric rows, and who supplies the proofs

| line | change |
| --- | --- |
| 106-113 | "these runs, which ended with between six and fifteen regions still on the stack" -> "the runs of Tables 6 and 7, which ended with between **two and fifteen**" (`parity_vol.csv` open 7/7/8/8/7/15; CSV 6/9/6/7/2). The old range matched no file I could find. |
| 149-153 | step 2: "the stack-count rows (stackrows) added ... the row set is the master's own" -> "the aggregate forms of the rows (stackrows)-(fibrerows) added (Section 8.3) ... those rows aside, the row set is the master's own" |
| 219-223 | "the objective is still decided before any geometry is examined" -> "before any part is put to the placeability oracle; what step 2 sees of the geometry is the necessary rows at the end of this section" |
| 232-258 | **"The pricing problem, in feasibility mode with the true slot bound, supplies the infeasibility proofs"** was the reference's oracle only. Now: the first implementation uses the pricing problem in feasibility mode; the second **decouples** (least stack count per footprint class by cutting stock, then an exact position-indexed placement over normal patterns, after the arithmetic tests), with the validity argument (any layout has at least that many stacks; discarding rectangles keeps a placement; an infeasible placement at a lower bound refutes; a yes counts only when the stacking is exact and the lifted layout passes the verifier). `geometry.jl` `pack_witness`/`pack_load` (default `backend=:decoupled`), `placeability.jl` `decoupled_pack`, `pack_rects`. Cites Christofides & Whitlock (1977), Beasley (1985). |
| 307-382 | **new paragraph** "Necessary conditions of placeability as rows": (eq:geomrows) weight thresholds, height thresholds, `L^max_f` units per stack; (eq:fibrerows) the dual-feasible-function fibre rows over an orientation split, with the family (identity, thresholds, floor scalings j <= 12); validity argument for each; step 2 carries the sum over the period's trucks with the fibre right-hand sides times `n_gt`; why they are **not** in pricing or the master (implied: every pricing point is a layout; every column is a placeable truck and a row linear in the columns holds for every non-negative combination); where they cut. Cites Fekete & Schepers (2001, 2004), Clautiaux, Alves & Valério de Carvalho (2010). Source comment names `cuts.jl` `add_stack_relaxation!(strong=true)`, `_strong_class_rows!`, `_fibre_rows!`, `_add_aggregate_stack_area!(strong=true)`, `placeability.jl` `min_stacks`, `max_units_per_stack`, `dffs`, `dff_rows`. |

The `dffs` docstring calls `floor` "subadditive"; the inequality it uses,
floor(a) + floor(b) <= floor(a + b), is **superadditivity**. Verified; the
paper says superadditive. The code was not touched.

## 6. Other method-side edits

| file | line | old -> new |
| --- | --- | --- |
| `Alg_construct.tex` | 14 | "add the aggregate form of (stackrows)" -> "add the aggregate forms of (stackrows)-(fibrerows)" |
| `Alg_construct.tex` | 25-27 | partition "with ū_gc as variable bounds" -> "... and, in 3D, (stackrows)-(fibrerows) per part" |
| `Tikz_construct.tex` | 70 | 1->2 annotation "the same rows; only the variable types change" -> "the same rows, plus (stackrows)-(fibrerows) summed over the period's trucks under 3D loading; the variable types change" (rendered and checked: fits) |
| `sec_pricing.tex` | 143-145 | the exact-image request is "what Sections 8.1 and 8.3 ask of it" -> "what Section 8.1 and the first implementation's refutations in Section 8.3 ask of it" |
| `sec_intro.tex` | 49-52 | contribution 2: the construction's "integer programmes carry necessary conditions of placeability as rows" |
| `sec_conclusions.tex` | 14-16 | "27.0 % and 24.5 % fewer trucks" -> **"25.7 % and 26.4 %"** (denominator the re-packed plan, as in Section 9.5; REPACK and CSV) |
| `sec_conclusions.tex` | 22-26 | the stack-count rows "tighten it measurably" -> "which the construction already carries, tighten it measurably when added to the compact volumetric relaxation" |
| `method.bib` | end | five entries, each checked on Crossref 2026-11-04: FeketeSchepers2001 (10.1007/s101070100243), FeketeSchepers2004 (10.1007/s001860400376), Clautiaux2010Survey (10.1007/s10479-008-0453-8), ChristofidesWhitlock1977 (10.1287/opre.25.1.30), Beasley1985 (10.1287/opre.33.1.49) |

## 7. `tab_protocol.tex` (Table 4)

Source comment (lines 1-10) added: launcher's `--ip-tl $(( $2 / 6 ))`, the
other per-call defaults (`tree.jl:583-611`), and the files behind the 120 s
sentence (B rung 5 `has_plan` false; 600 rung 5 160 trucks).
Rows (41-49): 36-instance line labelled "(first impl.)", solver "Gurobi 12 +
CP-SAT"; size ladder "Gurobi + CP-SAT ... seed 0" -> **"Gurobi 13 ... not
set"**; **new row** "step 4, per call | Gurobi 13 | budget/6: 150-600";
compact lines "Gurobi" -> "Gurobi 12"; volumetric parity "Gurobi" ->
"Gurobi 13". Fleet-floor line left as "Gurobi" (its files record no solver
version).
Caption (26-35): "No seed is set by that implementation" -> "by the second
implementation, which produced those runs and the size ladder"; added the
rows in steps 2 and 3, step 4 at one sixth of the budget per call, and "at a
constant 120 s, without those rows, the largest rung ended with no plan, and
at 600 s with one" (B, 600).

## 8. `tab_bench.tex` (Table 12) --- configuration disclosed

Source comment (lines 4-12): the config block of all 36 BENCH files
(`ladder` true, `stop_early` true, `frontier_share` 0.03,
`region_cert_policy` "always", `region_cert_share` 0.15, gurobi 12.0.1) and
the row-1 cross-check numbers.
Caption (18-46): now says it is the first implementation at `0c0c16c` and
that **its configuration is not the one Sections 7 and 8 describe** (heuristic
pricing rung with early stop, no rows, every popped region volumetrically
certified while 15 % of the budget lasted); bounds unaffected, plans are that
configuration's. Cross-reference to Table 7 was "agree on z, trucks and bound
and differ by one node" -> "agree on z and trucks, the bounds are 18 795.54
here and 18 795.56 there, and the node counts 68 and 301".

## 9. Housekeeping

* `paper.tex`: the `\InFlight` macro and its comment removed; no site remains.
* `inflight-sites.md`: header says none remain; Campaign B recorded as DONE
  with source, provenance and what changed; the old plan kept below it.
* Numbers **not** taken from the run, deliberately: `t_build`. In
  `julia/src/tree.jl` the closures `pack` (line 666) and `build` (line 768)
  both assign `_t`, which is also a local of `solve_fleet_tree` (lines 702,
  713), so they share one variable and `t_build` measures from the last pack
  call's start. On rung 4 it reports 576.91 s while the log shows the root
  construction alone running 668.7 s. Phase times in the paper come from
  `t_cg`, `t_pack` and log timestamps instead.
* `paper.pdf` rebuilt (34 pages); `paper-diff.pdf` built with
  `latexdiff-vc --git --flatten -r 100c362 paper.tex` (35 pages; the diff file
  defines `\InFlight` so that the deleted text typesets).

## 10. Referee pass (`REVIEW-final3d.md`, findings at `7b98fca`)

Each finding carries its resolution line in `REVIEW-final3d.md`. Line numbers
below are those of the files after this pass. B1 and D1-D7 are the author's
and were not acted on; the abstract's 31-of-36 / 8.8 % sentence is unchanged.

| finding | file:line | old -> new | source |
| --- | --- | --- | --- |
| M1 | `sec_experiments.tex:220-240` | "attributed to the bound and not to the plan"; reference ladder "in the configuration of Table 12" -> "attributed mainly to the bound ... but not wholly": the first implementation's verified 54-truck plan at 78 549.14 makes at least 1.45 of rung 4's 14.5 points the plan's; requirement-floor prunes added (m13); "an earlier configuration of the first implementation (commit 73411b9, ...)" (m11) | PYL `plan_verified`, `trucks`, `incumbent`; (79703.31-78549.14)/79703.31 |
| M1 | `sec_experiments.tex:448-449` | "below the best verified plan" -> "below the method's plan" | -- |
| M1 | `tab_head.tex:40-42` | caption adds: on row 4 a verified plan of the first implementation at 78 549.14 tightens the upper end | PYL |
| M2 | `sec_branching.tex:375-385` | "They go nowhere else ... implied by its rows ... could raise no bound" -> implied in pricing (integer points are layouts; tightening its relaxation untested) and in the master over placeable columns; **not** implied in the volumetric relaxation that supplies every bound | -- |
| M2 | `sec_conclusions.tex:24-30` | the stack rows "and the finer rows (88)-(89), which the volumetric relaxation does not imply, are the candidates" for the bound | -- |
| M3 | `sec_conclusions.tex:14-18` | "plans the same quantities with ... fewer trucks than re-packing two of its plans needs" -> "needs ... fewer trucks than two volumetric plans' quantities need once re-packed into loadable trucks, with the first implementation's earlier partition" | REPACK |
| M3 | `tab_repack.tex:31-46` | re-pack partition "lacks the rows (88)-(89)"; a re-pack with the current partition might need fewer trucks; row 3's cost = volumetric plan's + cK per extra truck, omitting the surplus's holding cost, which could only raise it | REPACK `repacked_cost` = `volumetric_obj` + 1000 x extra trucks |
| m1 | `sec_experiments.tex:296-299` | "2.5 to 90.2 s ... proved on thirty-five" -> "over the thirty-five runs ... whose sweep proved every pair ... 2.5 to 87.7 s" | BENCH trace; 87.7 s = I_8_10_2_60 |
| m2 | `tab_quantities.tex:30-32`, `tab_ladder.tex:37-39` | *dropped* defined in Table 5; Table 7 refers to it (shorter caption) | `tree.jl` `regions_dropped` (popped, not closed) |
| m3, m4, m10 | `sec_branching.tex:317-360` | thresholds tau -> xi; DFFs (u, v) -> pairs (u_1, U_1), (u_2, U_2) with the bound separate from the value; length threshold l; "mixes RTI types of different heights" -> "carries more than one RTI type"; "commodities of footprint class f" | `cuts.jl` `_strong_class_rows!`, `placeability.jl` `dffs` |
| m4 | `sec_branching.tex:282-284` | footprint class f tied to stack type h (p_f = p_h, q_f = q_h) | -- |
| m4 | `sec_experiments.tex:112-128`, `tab_bench.tex:23-29` | "heuristic pricing rung" -> "randomized greedy (GRASP) pricing heuristic" | BENCH `config.ladder` |
| m5 | `Tikz_construct.tex:101-103` | "the rows are the master's own" -> "the master's rows, plus (87)-(89) in 3D" | -- |
| m6 | `Alg_construct.tex:3-4` | "Lines 9-14 run under 3D loading only" -> "Line 4, the rows of line 12 and lines 15-27 apply under 3D loading only" | rendered algorithm |
| m7 | `tab_protocol.tex:27-47`, `tab_ladder.tex:29-30` | solver version "read from its package manifest"; per-call defaults incl. epsilon_2 = 1e-4; 1 GB soft cap on the second implementation's volumetric pricing, "not the 20 GB declared; every bound it reports came back proved"; Table 7 "(1 GB on volumetric pricing, Table 4)" | `colgen.jl:156-157`; `tree.jl:583-611`; LOG has no "unproved"; `parity_vol.csv` `root_proved` true |
| m8, m9 | `sec_branching.tex:237-244` | "essentially every positive answer" -> "most positive answers (93 % on the ladder)"; "a load within the payload is placeable iff" | CSV `pack_ok_shelf` 6749 of `pack_ok` 7234 |
| m12 | `sec_experiments.tex:112-128`, `tab_fleetfloor.tex:9-10`, `tab_summary.tex:8`, `tab_head.tex:26` | 9.1 lists what the first implementation produced; Table 8 "(commit dd5f039)", Table 9 "(first implementation)", Table 10 "Gurobi 12" | `fleet_floor_*.json` commit; compact `env.gurobi` |
| m13 | `tab_fleetfloor.tex:17-18`, `sec_experiments.tex:221-226` | "These are the only proofs of minimality in this paper" -> "The runs of Table 7 prune the same regions by arithmetic"; 9.3 cites the requirement floor | LOG lines 15, 651, 705; `master.jl` `_cover_rhs` |
| m14 | `sec_experiments.tex:363` | "ends with one region dropped, six open and three covered ..." | CSV `proof_reason` |
| m15 | `sec_experiments.tex:312-318` | ablation sentence -> "156 ... against 160, and 913 of 5 700 compositions submitted to the oracle proved unplaceable against 1 440 of 6 812. One run each; the cost, 2.6 % lower relative to the run without the rows, is not claimed." | CSV and 600 `pack_calls`, `pack_unplaceable` |
| m16 | `sec_experiments.tex:48-57` | certification policy stated fully (root, one-truck-fewer region, full-depth leaf, frontier sweep; volumetric in 3D; Table 12 certified every popped region); 3D affordability claim dropped | `tree.jl` `rcert` 752-762, frontier 1376-1430 |
| m17 | `sec_experiments.tex:302-305` | "placeability calls cost ... against 120 to 872 s of column generation" -> placeability tests cost 0.2-14.2 s "while column generation, geometric pricing included, spends the budget share it is given" | CSV `root_cg_stop` deadline |
| m18 | `sec_intro.tex:70`, `sec_abstract.tex:22`, `tab_protocol.tex:36` (clause cut), `sec_experiments.tex:62` | "it returns a verified plan and a certified bound, and proves no optimum" -> "it proves no optimum"; "on those instances" -> "on the thirty-six"; seed clause cut; "nothing reported rests on one" -> "the solvers' defaults apply" | -- |
| m19 | `tab_protocol.tex:30-31` | "like with like in budget, threads and cap, under Gurobi 13 against 12" | -- |
| trims | `sec_experiments.tex:255-260`, `sec_branching.tex:239`, `:322`, `:370` | tree paragraph shortened (repeated step-4 detail, "over eleven improvements"); placeability-proofs sentence shortened; two redundant validity clauses cut | -- |

Body words (`detex -n` over the thirteen `sec_*.tex` files, inline maths
counted): 12 531 before this pass, 12 483 after (11 382 at `100c362`).

## 11. Old runs dropped (author's decision, 2026-11-04)

"Old runs": any result of the first (Python) implementation of the method,
at any commit, and any configuration of the method other than the final one
(`final-3d`, `ladder_3d_final.csv`). Kept: the compact SCPTOP and compact 3D
comparators, the volumetric parity table (Julia), and the rows ablation
(`ip_tl_600.csv`). Table 11 is held (section 12).

| site | removed or changed | old source |
| --- | --- | --- |
| `tab_bench.tex` (Table 12), `sec_appendix_bench.tex` (Appendix B), `paper.tex` input | deleted (`git rm`) | `results/bench_tree/*.json`, commit 0c0c16c |
| `tab_summary.tex` (Table 9) | deleted | same, and `results/loads_recheck.csv` |
| `tab_fleetfloor.tex` (Table 8) | deleted; minimality now rests on each final run's requirement prune (LOG lines 15, 651, 705), already cited in Section 9.3 | `results/fleet_tree_3d/fleet_floor_*.json`, commit dd5f039 |
| `sec_abstract.tex:18-25` | "thirty-one of thirty-six at 8.8 %" and "501 of 724 proved impossible" -> the final ladder: 12 to 156 trucks, every truck verified, budgets 900-3 600 s, gaps 0.0075-0.163 % and 14.5 / 10.7 %; the compact 3D sentence kept | CSV `trucks`, `complaints`, `time_limit`, `gap_pct` |
| `sec_experiments.tex` 9.1 solver sentence | first/second implementation -> "the method is implemented in Julia ... Gurobi 13; the compact models ... Gurobi 12"; one sentence keeps the provenance of the held Table 11 | -- |
| `sec_experiments.tex` 9.1 certification policy | "Table 12's runs certified every popped region" and "second implementation's" removed | BENCH |
| `sec_experiments.tex` 9.1 Instances | the 36-instance factorial removed; the paragraph now describes only the generator rules and the instances used (I_8_5_2_90 and the k-suffixed ladder family of Tables 6-8) | -- |
| `sec_experiments.tex` 9.1 Verification | "re-implemented in a second language ... agreeing on S_2_2_2 and I_4_5_2_60 ..." removed; title "Verification." | Python cross-check |
| `sec_experiments.tex` 9.1 provenance paragraph | removed (only one implementation of the method is reported) | -- |
| `sec_experiments.tex` 9.3 | the 36-instance paragraph (31/36, 8.8 %, v = 60 failures) removed; Table 8 citation removed; the reference ladder comparison (54 at 78 549.14, 160 trucks) removed and the gap attribution restated without it ("chiefly to the bound ... nothing here proves that attribution or measures its share"); "under the same guarantee" given its own content | PYL, BENCH, fleet floors |
| `sec_experiments.tex` 9.3 bound paragraph | Table 12's sweep range (2.5-87.7 s over 35 runs) and its packing census (24 686 / 246 / 4 327 of 11 866 / 397) removed; the ladder's 1.3-13.8 s kept | BENCH |
| `sec_experiments.tex` 9.4 | "a geometric certification of three such regions, in a separate run of the first implementation, proved none of them" removed | `tree_3d_I_8_5_2_90_leafcert_900.json` |
| `sec_experiments.tex` 9.5 | the 36-instance placeability census (501 / 174 / 49 of 724, 94.5 / 91.1 / 91.8 %) and the ladder census paragraph (41/45, 119/137, "the first implementation's stronger oracle") removed; "What that costs" -> "What a volumetric capacity costs"; "All of it has one cause" -> "The over-credit has one cause"; the 32-unit cap re-sourced to the final run's bisected placeability cap; "the census instances" -> "the instances here" | `loads_recheck.csv`, `volumetric_loads_recheck.json`; new source LOG rung 4 "placeability caps ... p|P1=32" |
| `sec_experiments.tex` 9.2 | source comment added for the 274 s certificate (LOG line 699) | -- |
| `sec_branching.tex:16-19` | "measured at 3 000 branches with no integral relaxation reached" removed; the argument stays | early Python run |
| `sec_branching.tex` Deciding placeability | the first implementation's route (pricing problem in feasibility mode) removed; "Infeasibility proofs come from decoupling" | -- |
| `sec_branching.tex` rows paragraph | "the second implementation's oracle" -> "the oracle" | -- |
| `sec_pricing.tex:142-144` | "and an exact image, which is what Section 8.1 and the first implementation's refutations in Section 8.3 ask of it" -> "which is what Section 8.1 asks of it" | -- |
| `sec_model.tex:318` | the slot-bound warning now points to Section 7 (where the true slot bound is required), not Section 8 | -- |
| `Tikz_tree.tex:115-119` | "3 000 branches, no integral relaxation reached" removed | early Python run |
| `tab_ladder.tex` caption | "the second implementation of Section 9.1" and the Table 12 cross-reference (two implementations, 68 vs 301 nodes) removed | BENCH |
| `tab_head.tex` caption and comment | the 54-truck plan at 78 549.14 removed | PYL |
| `tab_protocol.tex` | rows removed: 36 instances (first impl.), fleet-floor certification, both placeability censuses (one row kept, renamed "Placeability check, Table 11", for the held table); caption: census sentence, the replaced parity runs' history and the constant-120 s / campaign-B sentence removed (a non-final configuration); "second implementation" -> "the method" | BENCH, fleet floors, B |
| `tab_quantities.tex` | the "unpl. / und." row (Tables 9 and 12's columns) and "the row says unproved" removed | -- |
| `tab_volumetric.tex` caption | "the recorded runs this table replaces finished at 1 817 to 1 897 s ..." removed | replaced Python parity runs |

Body words (`detex -n`, the `sec_*.tex` files): **12 483 -> 11 574**
(Appendix B's 13 words included before). Tables, figures and algorithms:
9 067 -> 7 143. 31 pages (35 before).

### Not removed, for the author

* **Table 11** is held (section 12); its caption still carries the Python
  placeability check (41/45, 119/137) and re-packs.
* **Figure 4** (`TikzHullFigure.tex`) plots supplier-bound loads "from a sweep
  of demand-derived price vectors" and "(74,126): the load a twelve-truck plan
  needs" -- data from an old Python sweep. The argument it illustrates is
  proved in Section 7; the figure would need regenerating from the Julia
  pricing or redrawing as a schematic.
* **`{R1: 200, R2: 30}`** in Section 8.2 and Figure 6: a step-2 vector from an
  old run, used as an illustration. Its claim (1.83 floors of volume, 35.5 m2
  of stacks on a 32.64 m2 floor) is arithmetic on instance data and holds
  whatever produced the vector.

## 12. Pending (purple): text that depends on Table 11

Table 11 is being re-run in Julia (placeability check with the Julia oracle;
row 3 with the Julia partition, the geometric rows and packing; row 2 to be
dropped). `paper.tex` defines `\Pending{...}` (`\textcolor{purple}`; xcolor is
loaded by `../necessarypackages.tex`). Content unchanged; wrapped sites:

| file | line | what |
| --- | --- | --- |
| `tab_repack.tex` | 21-46 | the whole caption |
| `tab_repack.tex` | 51-53, 56 | every cell of rows 1, 2 and 3 and of the savings line |
| `tab_protocol.tex` | 47 | every cell of the "Placeability check, Table 11" row |
| `sec_experiments.tex` | 14-19 | the sentence giving the check's and re-pack's Python provenance |
| `sec_experiments.tex` | 297-312 | Section 9.5's re-pack paragraph (74/212, 55/156, 25.7/26.4 %, 17.9/18.6 %, 80/137 units) |
| ~~`sec_intro.tex` 30-31~~ | -- | removed with the rewrite's introduction (section 13) |
| ~~`sec_intro.tex` 62-65~~ | -- | removed with the rewrite's introduction (section 13) |
| `sec_intro.tex` | 14 (end of the paragraph) | the restored introduction's contribution (iv), "We show by independent placeability proof ... and we quantify what repairing them costs" |
| `sec_abstract.tex` | 10 | the abstract's two sentences on the volumetric plan's unplaceable loads and the re-plan, through "discovered on the loading dock and not in the model" (qualitative, no Table 11 number) |
| `sec_conclusions.tex` | 13-18 | "a volumetric transport capacity is not a conservative simplification: ... 25.7 % and 26.4 % ..." |


## 13. Introduction and literature review restored from the initial manuscript

The author's decision: the rewrite's introduction and literature review used
notation before it was introduced. Both are now the initial manuscript's,
`old/main.tex` lines 4-22 (Introduction) and 23-442 (Literature Review, with
its four subsections, both comparison tables, the Research Methodology figure
and the closing `\FloatBarrier`). The paper is at the repository root since
4010ce9 (`Renderer.tex`, `main.tex`); `sec_problem.tex` and `sec_model.tex`
are untouched. Every citation in the restored text resolves against
`references.bib` (47 keys checked).

Verbatim except for the following, each against `old/main.tex`.

### (a) Labels and cross-references

| site | old | new |
| --- | --- | --- |
| `sec_intro.tex:2` | (no label) | `\label{sec:intro}` |
| `sec_litreview.tex:2` | (no label) | `\label{sec:lit}` |
| `sec_intro.tex:19` | "Section 2 ... Section 3 ... Section 4 ... Section 5 ... Section 6 ... Section 7 ... Section 8" (literal numbers) | `\ref{sec:lit}`, `sec:problem`, `sec:model`, `sec:decomp`--`sec:branching`, `sec:results`, `sec:conclusions` (see also (b)) |
| `sec_intro.tex:14` | `Section~\ref{sec:res-case}` (undefined: there is no case study) | removed with its sentence, (b) |

No later section referred to anything the rewrite's introduction or literature
review defined (`sec:intro`, `sec:lit`, `table:ComProTra` and the numbered
contribution list had no uses outside those files), so no later section needed
repair. `table:ComProTra` is now the restored procurement comparison table.

### (b) Sentences describing a method or result this paper no longer contains

| site | old | new |
| --- | --- | --- |
| `sec_intro.tex:9` | "Additionally, a matheuristic procedure (MATH-3D-SCPTOP) is researched." | "Additionally, a branch-and-price procedure (BP-3D-SCPTOP) is developed to solve it." |
| `sec_intro.tex:14` | "... a relax-and-fix construction as primal heuristic, giving MATH-3D-SCPTOP, and we show ..." | "... giving BP-3D-SCPTOP, and we show ..." |
| `sec_intro.tex:14` | "A fifth item is set up but *not* delivered in this revision: the comparison of the method against the company's own spreadsheet procedure on the case-study instances, for the reasons given in Section~\ref{sec:res-case}." | removed (the paper has no case study) |
| `sec_intro.tex:19` | "Section 5 develops the branch and price solution approach, MATH-3D-SCPTOP, ... Section 6 reports the computational experiments and Section 7 the case study. Finally, Section 8 concludes ..." | "Sections~\ref{sec:decomp}--\ref{sec:branching} develop the branch and price solution approach, BP-3D-SCPTOP, ... Section~\ref{sec:results} reports the computational experiments. Finally, Section~\ref{sec:conclusions} concludes ..." |
| `sec_litreview.tex:64` | "... Table 1 links the standard $C\&P$ nomenclature and the criteria of the typology according to \autocite{Wascher2007AnProblems}." | sentence removed: no such table exists (in the initial manuscript Table 1 was already the procurement comparison) |
| `sec_litreview.tex:284` (C&P comparison table, row 3D-SCPTOP) | resolution procedure "MH" | "BP"; the table notes gain "**BP** (Branch and Price)" (MH stays for Tresca 2022) |
| `sec_litreview.tex:327` (Figure 1) | node "MH-3D-SCPTOP" | "BP-3D-SCPTOP" |
| `sec_litreview.tex:347` (Figure 1) | side node "Metaheuristic" | "Column Generation" |
| `sec_litreview.tex:333` (Figure 1) | node "Case Study & Managerial Implications" | "Managerial Implications" |

Figure 1's "Resolution Procedure Comparison" (the method against the compact
models, Sections 9.2 and 9.4) and "Model Comparison" with "With respect
(Díaz-Madroñero et al. 2014)" (the 3D model against the volumetric one,
Section 9.5) still describe the paper and are unchanged.

### (c) Numbers from the old runs

| site | old | new |
| --- | --- | --- |
| `sec_intro.tex:14`, contribution (iii) | "which it matches to $0.0006\%$ of a proved optimum, under the provenance qualification stated in Section~\ref{sec:res-vol}: that run recorded no configuration of its own and ran an earlier revision of the scheme" | "which it matches to $0.0001\%$ of a proved optimum (Section~\ref{sec:res-vol})": the volumetric parity table's value, 68 139.21 against 68 139.17 (`results/julia/parity_vol.csv`), already quoted in Section 9.2; the provenance clause described the old run |

### Pending

Contribution (iv) rests on Table 11 and is wrapped in `\Pending` (section 12).

### Removed

* `tab_positioning.tex` (`git rm`): the rewrite's positioning table. Nothing
  else used it; the restored literature review carries its own procurement
  comparison (`table:ComProTra`) and C&P comparison tables.
* The rewrite's numbered contribution list: replaced by the initial
  manuscript's inline (i)-(iv). No other section referred to it.

### Build and counts

`Renderer.pdf` (with the abstract of section 14): exit 0, 38 pages (31
before), no undefined reference or citation. New warnings, both inherited
from the initial manuscript: an overfull `\hbox` of 6.75 pt in the Container
loading subsection (the same box at the same text in `old/`'s build), and a
biber warning that the ISBN of `Zhao2022LearningPacking` in `references.bib`
is invalid (the entry is cited only by the restored literature review).

`Renderer-diff.pdf` (built after section 14): both versions flattened with `latexpand` (100c362 from a
temporary `git worktree add`, removed afterwards), compared with `latexdiff
--config="PICTUREENV=(?:picture|DIFnomarkup|tikzpicture|tabular|longtable)[\w\d*@]*"`
(tables and TikZ marked as whole blocks; markup inside a tabular breaks
`\hline`), with `\InFlight` defined and the three removed tables' labels given
their 100c362 numbers. 40 pages, exit 0, no undefined reference.

Words (`detex -n`):

| | before | after |
| --- | --- | --- |
| Introduction | 477 | 793 |
| Literature review, prose | 306 | 2 612 |
| Literature review, with its two tables and figure | 306 (+402 in `tab_positioning.tex`) | 3 436 |
| Abstract (section 14) | 253 | 622 |
| Body (`sec_*.tex`, abstract included) | 11 574 | 15 389 (14 565 without the literature review's tables and figure) |


## 14. Abstract restored from the initial manuscript

The author's decision: `sec_abstract.tex` is again the initial manuscript's
abstract and keywords line (`old/main.tex` lines 1-3). `\keywords` is already
defined in `necessarypackages.tex:40`, as in `old/necessarypackages.tex:40`.
Changed only where the method or the results changed; no sentence was added.

| old | new |
| --- | --- |
| The master is solved by a two-level branch and bound over the fleet vector with a relax-and-fix primal heuristic, and because | The master is solved by branch and price, with a two-level branch and bound over the fleet vector and a flow-first relax-and-fix construction as primal heuristic whose integer programmes carry necessary conditions of placeability as rows, and because |
| it returns a plan $0.0006\%$ above a proved optimum at the same fleet of forty-five trucks with a certified bound $0.0114\%$ below that optimum, and it beats the compact model's incumbent on four further instances on which that model exhausts its memory budget; the columns of that stage carry no layout, so its plans are \emph{not} executable, and the runs behind it recorded no configuration of their own and ran an earlier revision of the scheme. | it returns a plan $0.0001\%$ above a proved optimum at the same fleet of forty-five trucks with a certified bound $0.0113\%$ below that optimum, and it agrees with the compact model's incumbent within $0.31\%$ on four further instances on which that model exhausts its memory budget; the columns of that stage carry no layout, so its plans are \emph{not} executable. |
| the method returns verified plans on thirty-one of thirty-six generated instances and on all five rungs of a ladder reaching twenty-four products and twenty periods, with provably minimum fleets through twelve periods. Certified gaps run from $0.0076\%$ to $13.3\%$ along the ladder, and from $0.01\%$ to $30.72\%$ with a mean of $8.8\%$ over the thirty generated instances carrying both a plan and a proved bound: small where | the method returns verified plans on all five rungs of a ladder reaching twenty-four products and twenty periods, from $12$ to $156$ trucks within budgets of $900$ to $3\,600$ s, with provably minimum fleets through twelve periods. Certified gaps run from $0.0075\%$ to $0.163\%$ on the first three rungs and are $14.5\%$ and $10.7\%$ on the last two: small where |
| Of the $724$ truck loads a volumetric model prescribes over the generated instances, $501$ are \emph{proved} impossible to place in a trailer and $174$ are left undecided; on the ladder instances $I\_16\_15\_2\_90\_k25$ and $I\_24\_20\_2\_90\_k50$, re-planning such a plan's trucks while keeping its delivery quantities needs $74$ and $212$ trucks where the method plans $54$ and $160$ directly. A volumetric transport capacity, which most of the procurement transport literature assumes, is therefore not a conservative simplification --- where it errs it errs by prescribing loads that do not fit, discovered on the loading dock and not in the model --- and on | \Pending{On the ladder instances $I\_16\_15\_2\_90\_k25$ and $I\_24\_20\_2\_90\_k50$, a volumetric model's plan prescribes loads that are \emph{proved} impossible to place in a trailer, and re-planning its trucks while keeping its delivery quantities needs more trucks than the method plans directly. A volumetric transport capacity, which most of the procurement transport literature assumes, is therefore not a conservative simplification --- where it errs it errs by prescribing loads that do not fit, discovered on the loading dock and not in the model ---} and on |

Sources (also in the file's comment): `ladder_3d_final.csv` for 12-156 trucks,
900-3 600 s and the gaps; LOG lines 15, 651, 705 for minimum fleets through
twelve periods; Table 6 (`parity_vol.csv`, `compact_ref`) for 0.0001 %,
0.0113 % and 0.31 %; `results/catalogue_realism.json` for the catalogue
sentence (nine types at 1.000, four at 1.231 or more, none below 1), kept
verbatim. Removed old-run numbers: 0.0006 %, 0.0114 %, 31 of 36, 0.0076-13.3 %,
0.01-30.72 %, 8.8 %, 724 / 501 / 174, 74 / 212, 54 / 160.

Words (`detex -n`, paragraph plus keywords): old **635**, new **622**; the
rewrite's abstract it replaces was 253.

### The old abstract (`old/main.tex` lines 1-3)

```
This paper proposes a mixed integer linear programming model, dubbed 3D-SCPTOP, for procurement transport planning in manufacturing supply chains, integrating the procurement, inventory and transport decisions of an assembly plant and its supplier with three-dimensional full-truckload constraints and the closed-loop management of returnable transport items. Because the placement subproblem is NP-hard and the compact model carries one copy of it per truck, direction and period, we reformulate the problem by Dantzig--Wolfe decomposition, taking the complete loaded truck as the unit of decision. The decomposition is derived structurally from a model stated over trucks in which \emph{no set of loading configurations appears at all}: deleting the quantifier over (direction, truck, period) from its block rows leaves the pricing problem, the trucks of a direction and period are anonymous and are aggregated into counts, and the master couples to the geometry through nothing but two aggregate-flow definitions and one truck count per direction and period. Every geometric, stack-height, crush, payload and full-truckload constraint lives inside one pricing problem per direction. A column is a geometrically realisable truck load with its layout retained, so the per-stack crush cap is evaluated on the product mix actually loaded rather than on a worst-case bound; a plan assembled from such columns is executable by construction, and each dispatched truck is re-verified independently of the search and of the oracle that built the column. The master is solved by a two-level branch and bound over the fleet vector with a relax-and-fix primal heuristic, and because every placeable load is volumetrically feasible, a certified bound of the volumetric relaxation is a valid lower bound for the 3D problem. The method is assessed in two stages. Against the compact formulation on the volumetric problem, where a reference optimum exists, it returns a plan $0.0006\%$ above a proved optimum at the same fleet of forty-five trucks with a certified bound $0.0114\%$ below that optimum, and it beats the compact model's incumbent on four further instances on which that model exhausts its memory budget; the columns of that stage carry no layout, so its plans are \emph{not} executable, and the runs behind it recorded no configuration of their own and ran an earlier revision of the scheme. On the 3D problem, where the compact model returns no incumbent beyond five periods, the method returns verified plans on thirty-one of thirty-six generated instances and on all five rungs of a ladder reaching twenty-four products and twenty periods, with provably minimum fleets through twelve periods. Certified gaps run from $0.0076\%$ to $13.3\%$ along the ladder, and from $0.01\%$ to $30.72\%$ with a mean of $8.8\%$ over the thirty generated instances carrying both a plan and a proved bound: small where the volumetric relaxation is nearly exact, large where a per-stack crush limit halves a container family's layer count, and which of the two regimes an instance falls in is computable from the container catalogue before any model is solved. Of the $724$ truck loads a volumetric model prescribes over the generated instances, $501$ are \emph{proved} impossible to place in a trailer and $174$ are left undecided; on the ladder instances $I\_16\_15\_2\_90\_k25$ and $I\_24\_20\_2\_90\_k50$, re-planning such a plan's trucks while keeping its delivery quantities needs $74$ and $212$ trucks where the method plans $54$ and $160$ directly. A volumetric transport capacity, which most of the procurement transport literature assumes, is therefore not a conservative simplification --- where it errs it errs by prescribing loads that do not fit, discovered on the loading dock and not in the model --- and on the industrial container catalogue the error runs one way: of its fifteen types \emph{none} is credited below an analytic count of what a trailer can hold, nine sit exactly at it, and four are over-credited by a fifth or more. That test needs only the catalogue and the trailer, so it is a property of the catalogue to be checked rather than assumed.

\keywords{ Procurement, inventory, transport planning,  full-truckload, 3D container loading }
```

### The new abstract

```
This paper proposes a mixed integer linear programming model, dubbed 3D-SCPTOP, for procurement transport planning in manufacturing supply chains, integrating the procurement, inventory and transport decisions of an assembly plant and its supplier with three-dimensional full-truckload constraints and the closed-loop management of returnable transport items. Because the placement subproblem is NP-hard and the compact model carries one copy of it per truck, direction and period, we reformulate the problem by Dantzig--Wolfe decomposition, taking the complete loaded truck as the unit of decision. The decomposition is derived structurally from a model stated over trucks in which \emph{no set of loading configurations appears at all}: deleting the quantifier over (direction, truck, period) from its block rows leaves the pricing problem, the trucks of a direction and period are anonymous and are aggregated into counts, and the master couples to the geometry through nothing but two aggregate-flow definitions and one truck count per direction and period. Every geometric, stack-height, crush, payload and full-truckload constraint lives inside one pricing problem per direction. A column is a geometrically realisable truck load with its layout retained, so the per-stack crush cap is evaluated on the product mix actually loaded rather than on a worst-case bound; a plan assembled from such columns is executable by construction, and each dispatched truck is re-verified independently of the search and of the oracle that built the column. The master is solved by branch and price, with a two-level branch and bound over the fleet vector and a flow-first relax-and-fix construction as primal heuristic whose integer programmes carry necessary conditions of placeability as rows, and because every placeable load is volumetrically feasible, a certified bound of the volumetric relaxation is a valid lower bound for the 3D problem. The method is assessed in two stages. Against the compact formulation on the volumetric problem, where a reference optimum exists, it returns a plan $0.0001\%$ above a proved optimum at the same fleet of forty-five trucks with a certified bound $0.0113\%$ below that optimum, and it agrees with the compact model's incumbent within $0.31\%$ on four further instances on which that model exhausts its memory budget; the columns of that stage carry no layout, so its plans are \emph{not} executable. On the 3D problem, where the compact model returns no incumbent beyond five periods, the method returns verified plans on all five rungs of a ladder reaching twenty-four products and twenty periods, from $12$ to $156$ trucks within budgets of $900$ to $3\,600$ s, with provably minimum fleets through twelve periods. Certified gaps run from $0.0075\%$ to $0.163\%$ on the first three rungs and are $14.5\%$ and $10.7\%$ on the last two: small where the volumetric relaxation is nearly exact, large where a per-stack crush limit halves a container family's layer count, and which of the two regimes an instance falls in is computable from the container catalogue before any model is solved. \Pending{On the ladder instances $I\_16\_15\_2\_90\_k25$ and $I\_24\_20\_2\_90\_k50$, a volumetric model's plan prescribes loads that are \emph{proved} impossible to place in a trailer, and re-planning its trucks while keeping its delivery quantities needs more trucks than the method plans directly. A volumetric transport capacity, which most of the procurement transport literature assumes, is therefore not a conservative simplification --- where it errs it errs by prescribing loads that do not fit, discovered on the loading dock and not in the model ---} and on the industrial container catalogue the error runs one way: of its fifteen types \emph{none} is credited below an analytic count of what a trailer can hold, nine sit exactly at it, and four are over-credited by a fifth or more. That test needs only the catalogue and the trailer, so it is a property of the catalogue to be checked rather than assumed.

\keywords{ Procurement, inventory, transport planning,  full-truckload, 3D container loading }
```

## 15. Restructured for Applied Mathematical Modelling

The author's brief (2026-11-04): restructure for *Applied Mathematical
Modelling*, keep the model and the two propositions, move implementation and
provenance to one appendix, cut the abstract, one paragraph of intuition per
method step followed by its algorithm or figure, bring the paper towards 30
pages, change no number, leave Table 11 and every `\Pending` site as they are.
Two later instructions: no commit hashes or source-tree ids in the printed text
(they stay in `%` comments), and describe only the final method, with no
comparison against variants of itself and no history.

**Journal requirements** (Guide for Authors,
https://www.sciencedirect.com/journal/applied-mathematical-modelling/publish/guide-for-authors;
the page returns 403 to direct fetches, so these were read from search-engine
extracts of that page and should be confirmed): abstract at most 250 words;
highlights required, 3-5 bullets of at most 85 characters including spaces, in
a separate editable file with "highlights" in its name; 1-7 keywords, single
words preferred; the LaTeX template is encouraged, not required; no page or
word limit and no mandatory section structure found.

**Pages:** 38 -> 30. **Words** (`detex -n`): body 15 389 -> 11 904; tables,
figures and algorithms 6 741 -> 3 375.

| file | before | after |
| --- | ---: | ---: |
| `sec_abstract.tex` (paragraph: 250) | 622 | 250 |
| `sec_intro.tex` | 793 | 793 |
| `sec_litreview.tex` | 3 436 | 3 436 |
| `sec_problem.tex` | 286 | 286 |
| `sec_model.tex` | 1 378 | 1 378 |
| `sec_decomp.tex` | 593 | 290 |
| `sec_master.tex` | 1 577 | 1 106 |
| `sec_pricing.tex` | 1 109 | 716 |
| `sec_branching.tex` | 2 643 | 1 298 |
| `sec_experiments.tex` | 2 415 | 1 442 |
| `sec_conclusions.tex` | 287 | 274 |
| `sec_appendix_scptop.tex` | 250 | 250 |
| `sec_appendix_rowmap.tex` (new) | -- | 31 |
| `sec_appendix_impl.tex` (new) | -- | 354 |

**Unchanged:** `sec_problem.tex`, `sec_model.tex`, both propositions and their
proofs (verbatim), the introduction and literature review (except the two float
specifiers `[h!]` -> `[!ht]` in the literature review, which removed a LaTeX
warning), Table 11 and every `\Pending` text. No number changed: every number in
the edited files was already in the previous version (checked by script; the only
new tokens are a column width and "Gurobi 13.0").

### Structure

* Section 5 is now "Decomposition and master problem": `sec_decomp.tex` (intuition,
  Figure 2, the block structure) followed by `sec_master.tex` as its subsections
  5.1 Columns, 5.2 The master (Proposition 1), 5.3 The restricted master,
  5.4 Lower bounds (Algorithm 1, Proposition 2).
* Section 6 pricing (Figure 3, formerly 4), Section 7 branching with the
  construction (Algorithms 2 and 3), Section 8 results, Section 9 conclusions.
* New Appendix B, "The block rows and the pricing problem": the row map
  (`tab_rowmap.tex`), moved from Section 5.
* New Appendix C, "Implementation and reproducibility" (`sec_appendix_impl.tex`):
  Julia/JuMP, Gurobi 13.0 (compact models Gurobi 12.0), how a run is organised,
  placeability order, plans verified truck by truck, every certified bound proved,
  one run per instance and what that licenses, and Table 10 with every setting:
  budgets, root column generation share, frontier reserve, initial-pool pricing
  limit, step 2/3/4 limits (step 4 budget/6 for the 3D runs, 120 s for the
  volumetric runs), the rows, pricing and placeability limits, threads, memory
  cap (with the 1 GB soft cap on volumetric pricing), seeds, the compact models'
  settings, and the pending placeability-check row. Hashes and source trees are
  in its `%` comment only. `tab_protocol.tex` is retired into it.
* Section 8.1 points to Appendix C and carries `\Pending{[repository URL]}`
  (also in Appendix C).
* `highlights.txt` (not input into the paper).

### Removed figures

Each method step now has one visual: Figure 2 (decomposition), Algorithm 1
(certification), Figure 3 (pricing), Algorithm 2 (the fleet tree), Algorithm 3
(the construction). Dropped (`git rm`, recoverable from history):
`Tikz_loop.tex` (the restricted master's matrix), `Tikz_tree.tex` (the branching
levels; Algorithm 2 carries the tree), `Tikz_construct.tex` (the construction on
one period, with the old-run vector {R1:200, R2:30}; Algorithm 3 carries the
steps) and `TikzHullFigure.tex` (old-run pricing data; the argument stays in
Section 6, "What pricing cannot return").

### Comparisons and history removed from the printed text

| where | what |
| --- | --- |
| Section 9.3 (old) | the rows ablation: "On the fifth rung one run isolates those rows ... 156 verified trucks against 160, and 913 of 5 700 compositions ... against 1 440 of 6 812. One run each; the cost, 2.6 % lower relative to the run without the rows, is not claimed." |
| Section 9.1 (old) | "Certifying every node instead is affordable on the volumetric model and closes far more of the space there, so the open counts below are a property of that choice ...; the two modes are reported under one policy ..." (policy comparison) |
| Section 9.1 (old) | the determinism paragraph ("Every row is one run ... not deterministic across those ...") -> one sentence in Appendix C |
| Section 6.3 (old) | "Rounding to nearest invalidates that certificate and ... convergence is never declared." |
| Section 6.4 (old) | "Memoising duals, and pricing only the pairs whose aggregates are fractional, are legitimate while columns are still arriving and forfeit the right to certify" |
| Section 8 (old) | "Two levels of the three ran ... with the delivery level on, such a leaf is split again ... It is also not what stopped the runs ..." -> one sentence: the delivery level is disabled in every run, so a full-depth leaf is covered by a certificate |
| Section 8.3 (old) | "the smaller of the two counts is also valid, and strictly tighter" (an alternative row) |
| Algorithm 2 | the comment "no demand mixes, no reduced variants" |
| Table 4 (old protocol) | "volumetric parity" (the name compared two implementations) -> "volumetric runs"; the protocol's provenance sentences -> Appendix C |
| Table 6 caption (old Table 7) | "one source tree (e4830fe) run alone on one machine ..." -> Appendix C |
| Table 5 caption (old Table 6) | the frontier-reserve explanation ("$(1-0.15)\times3\,600=3\,060$ s ... returns here in 2 to 73 s") |

Still printed, because they are `\Pending` and will be rewritten with the Julia
re-pack: the Section 8.1 sentence on the Python provenance of Table 8's checks
and re-packs, Table 8's caption ("first implementation"), the Section 8.5 re-pack
paragraph and the conclusions' 25.7 % / 26.4 % sentence.

### Cut, by kind

* **Abstract:** 622 -> 250 words, same order (problem, model, method, results),
  the `\Pending` volumetric sentence shortened as allowed.
* **Redundancy:** the decomposition's five paragraphs (blocks, delete the
  quantifier, identical blocks, classes, discretisation) -> two; the master's
  restated column definition and the duplicated "a plan is a set of drawings";
  pricing's "deleting a variable is a claim" paragraph -> one sentence; the
  "improving or optimal" and "volumetric oracle" paragraphs merged into "The
  oracle"; the volumetric results' four paragraphs -> two.
* **Validity arguments -> one sentence each:** the implied rows of pricing; the
  geometric rows (thresholds, unit count, DFF fibre rows: one sentence with the
  Fekete-Schepers pointer); where the rows are implied (pricing and master) and
  where not (the volumetric relaxation); dominance cut versus exact exclusion;
  undecided is never a proof; why the partition must be a cover.
* **Qualifiers kept** because they change a claim's truth: no optimality where
  `proved` is false; "no plan", never "infeasible", for X <= 155; "bound" only
  for a certified one; the RMP value is not a bound; the compact 3D dual bound
  bounds a restriction; the attribution of the last two gaps to the bound is not
  proved; volumetric plans are not verified geometrically.
* **Captions:** Figures 2 and 3, Tables 4 (quantities), 5, 6 and 7 cut to what a
  reader needs to read them.
* **Bibliography:** `\AtBeginBibliography{\emergencystretch=2em}` in
  `Renderer.tex`, so a long title in the new layout does not overflow.

### Build

`Renderer.pdf`: 30 pages, exit 0, no undefined reference or citation; warnings
as in the previous commit (`No \author`, overfull 6.75 pt and 1.35 pt, the
biber ISBN warning for `Zhao2022LearningPacking`). `Renderer-diff.pdf` against
100c362 (latexpand of both, latexdiff with tables and TikZ as blocks, `\InFlight`
defined and the removed floats' labels given their old numbers): 39 pages,
exit 0, no undefined reference.

### Pending (purple) sites after this pass

Section 12's list, with today's files and lines (content unchanged; the Table 11
sites will be rewritten when the Julia re-pack arrives):

| file | line | what |
| --- | --- | --- |
| `tab_repack.tex` | 21-46 | the whole caption of Table 8 (formerly 11) |
| `tab_repack.tex` | 51-53, 56 | every cell of rows 1, 2 and 3 and of the savings line |
| `sec_appendix_impl.tex` | 62 | the "placeability check" row of Table 10 (moved from `tab_protocol.tex`) |
| `sec_experiments.tex` | 10-14 | the sentence giving the check's and re-pack's Python provenance |
| `sec_experiments.tex` | 166-181 | Section 8.5's re-pack paragraph |
| `sec_intro.tex` | 14 | contribution (iv) |
| `sec_abstract.tex` | 10 | the shortened sentence "A volumetric model's plan prescribes loads proved impossible to place, so a volumetric transport capacity is not a conservative simplification;" |
| `sec_conclusions.tex` | 13-18 | "a volumetric transport capacity is not a conservative simplification: ... 25.7 % and 26.4 % ..." |
| `sec_experiments.tex` | 10; `sec_appendix_impl.tex` | 35 | new: `[repository URL]`, for the author to decide |
