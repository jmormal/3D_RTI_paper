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
