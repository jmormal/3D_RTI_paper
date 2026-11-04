# REVIEW: the final-3D revision (`100c362..d2cd6c0`, `rewrite/`)

Referee pass, 2026-11-04. I checked the revision against the data files and the
code, not against `CHANGES-final3d.md`. I edited nothing in the manuscript.

Sources used:

- **CSV**: `/home/jj/3D-RTI-final/results/julia/final/ladder_3d_final.csv`
- **LOG**: `.../final/ladder_3d_final.log`
- **600**: `/home/jj/3D-RTI/results/julia/ip_tl_600.csv` and `.log`
- **B**: `/home/jj/3D-RTI/results/julia/ladder_3d.csv` and `.log`
- **PYL**: `/home/jj/3D-RTI/results/fleet_tree_3d/tree_3d_*_table_*.json`
- **BENCH**: `/home/jj/3D-RTI/results/bench_tree/tree_3d_*.json`
- **REPACK**: `/home/jj/3D-RTI/results/repacked_volumetric.json`
- **ARMS**: `/home/jj/3D-RTI-final/results/julia/server_arms/README.md`
- **code**: `/home/jj/3D-RTI-final/julia/src` at `src_tree` e4830fe

Line numbers are those of the `.tex` files at `d2cd6c0`.

**Counts: 1 blocking, 3 major, 19 minor**, plus 7 items for the author's
decision.

**Resolution pass (2026-11-04, later the same day).** Each finding below now
carries a resolution line: FIXED (what was done), NOT CHANGED (why) or AUTHOR
(B1 and D1-D7, left by instruction). M1-M3 and all nineteen minors are fixed,
two of them partly (m4, m16, with the reason given). Line numbers in the
findings are those of `d2cd6c0`, not of the fixed files.

**Build:** a clean rebuild of a scratch copy (`latexmk -C`-equivalent, then
`PATH=/usr/bin:$PATH TEXINPUTS=..: latexmk -pdf paper.tex`) exits 0.

- It gives 34 pages, with no undefined references or citations.
- The `pdftotext` output is identical to the committed `paper.pdf`.
- There are four overfull boxes. All four are present, unchanged, in a build
  of `100c362`, so the revision did not introduce them.
- `paper-diff.pdf` exists (35 pages).

---

## Blocking

### B1. The abstract reports, as BP-3D-SCPTOP, a run the paper now says is not BP-3D-SCPTOP

- **Where:** `sec_abstract.tex:11-20`, `tab_summary.tex:9`, against
  `tab_bench.tex:19-29`.
- **As printed:**
  - The abstract: "We solve it with BP-3D-SCPTOP: branch and price over the
    fleet vector ... On thirty-six generated instances it returns a verified
    plan on thirty-one, at a mean certified gap of 8.8% over thirty of them."
  - The Table 9 caption: "plans counts the instances on which BP-3D-SCPTOP
    returned a verified plan".
  - The revised Table 12 caption, in bold: "**Its configuration is not the one
    Sections 7 and 8 describe**: a heuristic pricing rung ran ahead of the
    exact oracle ... the rows (88)-(89) were absent ...".
- **Evidence:** the config block of all 36 BENCH files is identical:
  - `ladder True`, `stop_early True`, `ladder_margin 0.005`;
  - `region_cert_policy "always"`, `region_cert_share 0.15`,
    `frontier_share 0.03`;
  - `env.commit 0c0c16c`, `env.gurobi 12.0.1`. This is the first (Python)
    implementation.

  Section 9.1 (`sec_experiments.tex:119-130`) now says the same thing. So the
  abstract's only quantitative statement about the method is a different
  configuration, on a different implementation, under the paper's name. This
  is the "two algorithms under one name" defect that past rounds rated fatal.

  It also conflicts with the standing decision that the Julia implementation
  is what the paper reports. The revision created the contradiction: it
  disclosed the configuration in Table 12 and Section 9.1 but left the
  abstract and the Table 9 caption as they were.
- **Fix, one of:**
  - (a) Re-run the 36 instances with the final configuration, then regenerate
    Tables 12 and 9, the Section 9.3 paragraph and the abstract sentence. The
    launcher would be `ladder_3d_final.sh`'s call: `--geom-rows both --ip-tl
    150`, 900 s, 10 threads, 20 GB.
  - (b) Take the 36-instance figures out of the abstract, and label Table 9 and
    every other use as "an earlier configuration of the first implementation".

  This is the author's call (D1). The abstract cannot stay as it is.

**AUTHOR (D1).** Not acted on, by instruction: the abstract's 31-of-36 / 8.8 % sentence and Table 9's "BP-3D-SCPTOP" are unchanged. Table 9's caption now names its implementation (m12), nothing more.

---

## Major

### M1. "Attributed to the bound and not to the plan" and "the best verified plan", one paragraph after printing a cheaper verified plan

- **Where:** `sec_experiments.tex:224-229`, `233-235` and `439-441`;
  `tab_head.tex:51`.
- **As printed:**
  - Section 9.3: "The jump to 14.5% and 10.7% on the last two rungs is
    attributed to the bound and not to the plan".
  - Then, eight lines on: "on the fourth this run needs 55 trucks where that
    one needed 54, at 79 703.31 against 78 549.14".
  - Section 9.5: "the certified bound on the two crush-binding rungs sits 14.5%
    and 10.7% below the best verified plan".
  - Table 10, row 4: `z* in [68 139.17, 79 703.31]`.
- **Evidence:** PYL `tree_3d_I_16_15_2_90_k25_table_1800.json` has
  `plan_verified True`, `trucks 54`, `incumbent 78549.14`. The paper itself
  cites this plan. Therefore:
  - the best verified plan the paper reports on rung 4 is 78 549.14, not
    79 703.31;
  - the bound sits (78549.14-68131.48)/78549.14 = **13.26%** below it, not
    14.5%;
  - at least (79703.31-78549.14)/79703.31 = **1.45 points** of rung 4's 14.5%
    gap is demonstrably the plan's;
  - Table 10's interval has a known tighter upper end.
- **Fix:**
  - Change "the best verified plan" to "the method's plan".
  - Say in Section 9.3 that a verified plan 1.45% cheaper exists on rung 4, so
    part of that gap is the plan's.
  - Either use 78 549.14 as Table 10's upper end, with its source, or keep
    79 703.31 and say in the caption why the known better plan is excluded.
  - If the author decides not to cite the Python ladder at all (D2), the
    "best verified plan" wording still has to go.

**FIXED.** Section 9.3 now attributes the last two gaps "mainly" to the bound, "but not wholly", and states that the first implementation's 54-truck verified plan at 78 549.14 (PYL, commit 73411b9) makes at least 1.45 of rung 4's 14.5 points the plan's (gap's own denominator: (79 703.31 - 78 549.14)/79 703.31). Section 9.5's "the best verified plan" is now "the method's plan". Table 10 keeps the method's plan as upper end, its caption saying that on row 4 the first implementation's verified 78 549.14 tightens it. Whether to cite the Python ladder at all stays D2.

### M2. "They go nowhere else": the reason given is wrong for pricing and silent on the master that supplies every bound

- **Where:** `sec_branching.tex:378-385`, against `sec_pricing.tex:69-90`,
  `sec_pricing.tex:146-151`, `sec_branching.tex:354-355` and
  `sec_conclusions.tex:22-27`.
- **As printed:** "They go nowhere else. Every point of the pricing problem's
  feasible set is a layout, so they are implied by its rows; and every column
  is a placeable truck that satisfies them, so in the master ... they would be
  implied too and could raise no bound."
- **Evidence:**
  1. *Pricing.* The rows hold at every integer point, but the pricing problem
     is a MIP. In the second implementation it is a Gurobi big-M model
     (`pricing.jl`). The paper itself says its "linear relaxation is weak"
     (`sec_pricing.tex:148-149`).

     The paper also already adds an implied valid inequality to pricing
     *because* it helps: the area row (bp:PP-areacut), `sec_pricing.tex:69-90`.
     That row is the identity member of the new family (88)-(89), as
     `sec_branching.tex:354-355` says.

     So "implied by its rows" is not a reason they cannot help there. Within
     the paper it is a contradiction: one member of the family sits in
     pricing, and the family is excluded from pricing as redundant.
  2. *The bounding master.* The argument covers the geometric master, where it
     is correct (each column satisfies the rows). But no bound in the paper
     comes from that master. Every certificate comes from the volumetric
     relaxation (Proposition 2; `tree.jl` `rcert` applies the region to `vlp`
     and prices with the knapsack oracle).

     Its columns are volumetric loads, not layouts, so the rows are *not*
     implied there. Imposed per column in the volumetric pricing problem, they
     stay valid by inclusion, because every placeable load satisfies them. They
     could therefore raise the certified bound exactly where the gaps are
     14.5% and 10.7%.

     The conclusions (`sec_conclusions.tex:22-27`) call the stack rows "the
     candidate for tightening the bound inside the search". As written, that
     reads as contradicted by "could raise no bound".
- **Fix:**
  - Restrict the redundancy statement to the geometric master.
  - For pricing, give the real reason for omitting them (untested; or measured
    and not worth it). Do not say "implied".
  - Add one sentence saying that in the volumetric relaxation, which supplies
    every bound, they are not implied, and that this is where Section 10
    proposes to put them.

**FIXED.** `sec_branching.tex`: "They go nowhere else ... could raise no bound" replaced. The rows are implied in the pricing problem (its integer points are layouts; whether they would tighten its weak relaxation, as (bp:PP-areacut) does, is stated as untested) and in the master over placeable columns. They are *not* implied in the volumetric relaxation that supplies every bound, and the text points to the conclusions. The conclusions now name (88)-(89) with the stack rows as candidates for the bound, "which the volumetric relaxation does not imply". No row was added to the bound and no stronger bound is claimed (D6).

### M3. The conclusions' headline saving compares the method with a re-pack made by weaker machinery, and misdescribes it

- **Where:** `sec_conclusions.tex:13-17`, `sec_experiments.tex:402-410`,
  `tab_repack.tex:29-30` and `:47-49`.
- **As printed:** "the method plans the same quantities with 25.7% and 26.4%
  fewer trucks than re-packing two of its plans needs".
- **Evidence:**
  1. "The same quantities" is false. Table 11, row 4: BP-3D-SCPTOP "also
     chooses the quantities". Only the re-pack keeps the volumetric plan's
     quantities.
  2. The comparator is REPACK (written 2026-09-16, Python; config `pack_tl 20`,
     `part_tl 90`, `rounds 3`). It was re-packed with "the partition-and-pack
     machinery of the first implementation", without rows (88)-(89).

     The method's column now uses those rows, and the paper's own ablation
     credits them with 4 trucks on `I_24_20_2_90_k50`: 160 without, 156 with.
     Before the revision, the text could truthfully say "the same machinery
     the method uses". It no longer can.

     A re-pack with the method's current partition might need fewer than
     74/212 trucks. This is the review-paper skill's straw-man test.
  3. The cost savings (17.9% and 18.6%) use `repacked_cost`, which is exactly
     `volumetric_obj + 1000 x (extra trucks)`:
     - 68139.17 + 29x1000 = 97139.17;
     - 200266.21 + 75x1000 = 275266.21.

     It is not a cost re-derived from the re-packed dispatch, so the holding
     cost of the 80 and 137 over-delivered units is not counted.
- **Fix:**
  - Re-pack both volumetric plans with the Julia partition-and-pack under
    `--geom-rows both`, and re-derive each plan's cost from its own dispatch.
  - Until then, the conclusions should say "than re-packing the volumetric
    plans' quantities, with the earlier partition, needs", and drop "the same
    quantities".
  - Table 11's caption should state how its cost figure was computed.

**FIXED (wording); rebuild is D4.** Conclusions: "plans the same quantities" is gone; now "needs 25.7 % and 26.4 % fewer trucks than two volumetric plans' quantities need once re-packed into loadable trucks, with the first implementation's earlier partition". Table 11's caption says the re-pack partition lacks (88)-(89), that a re-pack with the current partition might need fewer trucks on either instance, and that row 3's cost is the volumetric plan's plus cK per extra truck, omitting the over-delivered units' holding cost, which could only raise it. The re-pack itself was not rebuilt.

---

## Minor

### m1. The volumetric certificate's time range ends on the run with no certificate

- **Where:** `sec_experiments.tex:290-295`.
- **As printed:** "the volumetric certificate is in hand 2.5 to 90.2 s into
  the run, with every pair proved on thirty-five".
- **Evidence:** in BENCH's trace, 90.2 s is `I_8_10_3_60`, whose line reads
  `proved=False`. The maximum over the 35 proved rows is 87.7 s
  (`I_8_10_2_60`).
- **Fix:** "2.5 to 87.7 s on the thirty-five where every pair was proved".

**FIXED.** "2.5 to 87.7 s ... over the thirty-five runs whose sweep proved every pair"; the thirty-sixth is named as unproved.

### m2. Table 7's definition of *dropped* does not match the code

- **Where:** `tab_ladder.tex:37-39`.
- **As printed:** row 4's unbounded open regions have "no bound of their own,
  the root's covering them", while *dropped* regions are "those left with no
  bound covering them".
- **Evidence:** `tree.jl:1433-1437` says "A dropped region and an unbounded
  open one are covered by the root bound alone". A region is dropped when it is
  popped and closed with no certificate of its own:
  - a failed construction ("no plan in region", `tree.jl:1129-1135`);
  - an abandoned LP;
  - or an unproved certification.
- **Fix:** define *dropped* that way, and add it to Table 5.

**FIXED.** *dropped* is now defined in Table 5 (popped and abandoned without a certificate of their own, e.g. no plan in them; only the root's bound covers them) and Table 7's caption refers to it, which also shortens that caption.

### m3. The DFF definition does not admit the threshold member as written

- **Where:** `sec_branching.tex:350-358`.
- **As printed:** "u is one when Σx_i <= pK implies Σu(x_i) <= u(pK)", then
  thresholds [x >= tau] "with u(pK) = floor(pK/tau)".
- **Evidence:** for u(x) = [x >= tau], u(pK) = 1, and Σu(x_i) can reach
  floor(pK/tau) > 1. The statement holds only if u(pK) is a separate bound and
  not the function's value. `placeability.jl` `dffs` does carry it separately,
  as a second tuple element.
- **Fix:** define a DFF as a pair (u, U_u) with Σx_i <= pK implying
  Σu(x_i) <= U_u, and write U_u qK on the right-hand side of (89).

**FIXED.** A dual-feasible function is now a pair (u, U): sum x_i <= C implies sum u(x_i) <= U; (89) has U_1 qK and pK U_2, and the family lists U = C, floor(C/l), j.

### m4. Symbol and word collisions introduced by the new text

- `tau` is the threshold here, but a time index at `sec_master.tex:175` and
  `sec_model.tex:119-123`.
- `v` is a DFF here, but the instance granularity at
  `sec_experiments.tex:72`.
- "class f" (`sec_branching.tex:320`, "commodities of class f") is not "class
  (g,t)" (`sec_decomp.tex:39-76`).
- f is never tied to the stack type h of the model. The nomenclature has
  p_h, q_h (`tab_nomenclature.tex:36`); the rows use p_f, q_f.
- "Rung" now has three senses:
  - branching levels (`sec_branching.tex:22-28`, Figure 5);
  - size-ladder instances;
  - the "heuristic pricing rung" (`sec_experiments.tex:121-126`,
    `tab_bench.tex:23-28`), which the paper never defines.
- **Fix:** rename (for example sigma for the thresholds, u_1/u_2 for the DFFs),
  say "footprint class f, i.e. stack type h", and define or rename the pricing
  rung ("a greedy pricing heuristic").

**FIXED, partly.** Weight and height thresholds are now xi, the DFF length threshold l, the two DFFs u_1 and u_2. Footprint class f is tied to stack type h (p_f = p_h, q_f = q_h) where it is introduced, and "commodities of footprint class f" is used in the rows paragraph. The pricing "rung" is renamed "a randomized greedy (GRASP) pricing heuristic" in Section 9.1 and Table 12. NOT CHANGED: the branching-level and size-ladder senses of "rung" are older than this revision, each is clear from its context, and renaming either touches many sites for no added clarity.

### m5. Figure 6, band 2, contradicts the arrow above it

- **Where:** `Tikz_construct.tex:101`, rendered on p. 20.
- **As printed:** "no pricing call here: the rows are the master's own".
- **Evidence:** the 1->2 arrow (line 70) now adds (87)-(89). The text
  (`sec_branching.tex:152-154`) says "those rows aside, the row set is the
  master's own".
- **Fix:** "no pricing call here: the master's rows (plus, in 3D, (87)-(89))".

**FIXED.** Band 2 now reads "no pricing call here: the master's rows, plus (87)-(89) in 3D, and the objective is decided now". Rendered: fits the band.

### m6. Algorithm 3's caption names the wrong lines

- **Where:** `Alg_construct.tex:3`.
- **As printed:** "Lines 9-14 run under 3D loading only".
- **Evidence:** in the rendered algorithm (p. 21), lines 9-14 are the
  deliveries and the partition loop, which run in both modes. The 3D-only
  material is line 4, line 12's new clause, and lines 15-27. This predates the
  revision, but the revision added 3D-only clauses to lines 4 and 12 without
  noticing.
- **Fix:** "Line 4's rows, line 12's (87)-(89) and lines 15-27 apply under 3D
  loading only".

**FIXED.** Verified against the rendered algorithm. Caption: "Line 4, the rows of line 12 and lines 15-27 apply under 3D loading only". epsilon_2's value is now printed in Table 4 (m7).

### m7. The protocol declares a memory cap and a provenance that the bounding oracle and the files do not have

- **Where:** `tab_protocol.tex:16`, `:21` and `:42-43`; `tab_ladder.tex:28`.
- **As printed:** *mem* is "the per-model soft cap", 20 GB on the ladder;
  "Every line is read from the run files themselves."
- **Evidence:**
  1. `colgen.jl:156-157`: `price_volumetric` builds every volumetric pricing
     model with `mem_gb=1.0`. That `SoftMemLimit` is compared with the shared
     environment's growing `MemUsed`. ARMS ("The volumetric bound on rung 5
     lost to a memory cap") shows it returning `MEMORY_LIMIT` on every call at
     12-32 threads.

     It is sound: the call counts as unproved. No certification in LOG came
     back unproved (`grep -c unproved` = 0), so no number here changes. But
     the oracle behind every bound did not run under the declared 20 GB.
  2. CSV records `time_limit`, `threads`, `mem_gb` and `ip_tl` only. The other
     per-call limits are code defaults (`tree.jl:594-611`):
     - step 2: 300 s at the root, 120 s at a node;
     - epsilon_2 = 1e-4;
     - partition 60 s, pricing 20 s, pack 20 s;
     - root column generation 15% of the budget, frontier reserve 15%.

     epsilon_2's value is printed nowhere (`Alg_construct.tex:3-5` names the
     symbol only).
  3. "Gurobi 13" comes from `julia/Manifest.toml` (`Gurobi_jll 13.0.3`), not
     from the run (provenance: recovered, not recorded).
- **Fix:** declare the 1 GB volumetric-pricing cap, or fix the code (D7). Add a
  footnote row of the per-call defaults, including epsilon_2. Say the Julia
  solver version is from the Manifest.

**FIXED.** Table 4's caption now declares the 1 GB soft cap of the second implementation's volumetric pricing models (`colgen.jl:156-157`), checked against the shared environment, "not the 20 GB declared", and that every bound it reports came back proved; Table 7's caption says "(1 GB on volumetric pricing, Table 4)". It lists the ladder's per-call defaults, epsilon_2 = 1e-4 included, and says the Julia solver version is read from the package manifest. The cap itself is not changed (D7).

### m8. "Essentially every positive answer" is the first implementation's figure

- **Where:** `sec_branching.tex:233-236`.
- **As printed:** the shelf design "supplies essentially every positive
  answer".
- **Evidence:** CSV, summed over the ladder: `pack_ok_shelf` 6749 and
  `pack_ok_mip` 485, so the shelf gave 93.3% of positives. On rung 3 it was
  386 shelf and 60 exact (13.5% exact). "1642 of 1642" is the Python
  reference's number (the `geometry.jl` `pack_witness` docstring).
- **Fix:** "most positive answers (93% on the second implementation's
  ladder)".

**FIXED.** "supplies most positive answers (93 % on the ladder of Section 9.3)" (6749 of 7234).

### m9. The decoupling "if and only if" omits the payload

- **Where:** `sec_branching.tex:243-249`.
- **As printed:** "a load is placeable if and only if the multiset of s_f
  rectangles of each class f can be placed on the floor".
- **Evidence:** an over-payload load whose rectangles fit is not placeable.
  The code checks payload first (T2 in `prove_unplaceable`, called at the top
  of `decoupled_pack`).
- **Fix:** "a load within the payload is placeable iff ...".

**FIXED.** "a load within the payload is placeable if and only if ...".

### m10. The condition on the height rows is stated differently from the code

- **Where:** `sec_branching.tex:338-339`.
- **As printed:** "the height thresholds being written only where a class
  mixes RTI types of different heights".
- **Evidence:** `cuts.jl` `_strong_class_rows!` writes them when
  `length(hs.rtis) > 1`, i.e. whenever the class has more than one RTI type.
  This is harmless, since an equal-height class gives a valid row.
- **Fix:** "only where a class carries more than one RTI type".

**FIXED.** "only where a class carries more than one RTI type".

### m11. "In the configuration of Table 12" is not what the reference ladder's files say

- **Where:** `sec_experiments.tex:231-233`.
- **Evidence:** the PYL files differ from BENCH on three counts:
  - commit `73411b9`, not `0c0c16c`;
  - no `region_cert_policy` key (BENCH has `"always"`);
  - `step2_gap` 5e-4.
- **Fix:** "in an earlier configuration of the first implementation (commit
  73411b9, heuristic pricing rung and early stop on)".

**FIXED.** Now "an earlier configuration of the first implementation (commit 73411b9, greedy pricing heuristic and early stop on)". One correction to the finding: `step2_gap` is 5e-4 in BENCH as well as in PYL, so the files differ in commit and in the absent `region_cert_policy` key, not in `step2_gap`.

### m12. Section 9.1 promises each table's caption names its implementation; four do not

- **Where:** `sec_experiments.tex:112-115`.
- **Evidence:** these never say the first implementation produced them:
  - Table 8 (`fleet_floor_*`, commit `dd5f039`, Python);
  - Table 9 (aggregated from BENCH);
  - Table 10's compact columns (Python, Gurobi 12);
  - the `leafcert` run cited in Section 9.4 (the prose there does say it).
- **Fix:** add the implementation to each of those captions, and complete the
  list in Section 9.1.

**FIXED.** Section 9.1 now lists what the first implementation produced (Tables 8, 9 and 12, the censuses and re-packs of Section 9.5, the leaf certification of Section 9.4). Table 8's caption names it (commit dd5f039), Table 9's says "(first implementation)", Table 10's says the compact model ran on Gurobi 12.

### m13. Table 8's "the only proofs of minimality" is no longer true, and in the method's favour

- **Where:** `tab_fleetfloor.tex:16`, against `sec_experiments.tex:220-223`.
- **Evidence:** LOG lines 15, 651 and 705, for example "X<=11: requirement: X
  <= 11 but at least 12 trucks are needed -> prune (proved)", at 142.0, 154.4
  and 298.5 s. `_cover_rhs` (`master.jl:256-300`) is a volume, payload and
  count argument, so it is valid under 3D loading. The method's own runs prove
  the fleet minimum on rungs 1-3, arithmetically, inside the run.
- **Fix:** say so in Section 9.3 and drop "only" from Table 8.

**FIXED.** Table 8's "These are the only proofs of minimality in this paper" is replaced by "The runs of Table 7 prune the same regions by arithmetic (Section 9.3)". Section 9.3 says each run prunes the one-truck-fewer region by the requirement floor (LOG lines 15, 651, 705), which is valid under 3D loading.

### m14. Section 9.4 gives one of three reasons rung 1 is not proved

- **Where:** `sec_experiments.tex:354-357`.
- **Evidence:** CSV `proof_reason` reads "1 regions dropped; 6 regions open; 3
  unrefuted leaves; 3 bounded unrefuted". The text names only the three
  bounded leaves.
- **Fix:** add "and with one region dropped and six still open".

**FIXED.** "ends with one region dropped, six open and three covered by certified volumetric bounds ...".

### m15. The rows-ablation sentence is garbled and its counts lack denominators

- **Where:** `sec_experiments.tex:306-311`.
- **As printed:** "one run each, so the counts are what it shows, not the cost,
  2.6% below the run without the rows".
- **Evidence:** `pack_calls` is memoised per run on the exact load
  (`tree.jl:660-663`), so the counts are distinct compositions per run:
  - with the rows, 913 of 5700 (16.0%);
  - without, 1440 of 6812 (21.1%) (600).

  The raw 913 against 1440 mixes in the 16% fewer proposals made. Otherwise
  the pair is well matched. Both runs:
  - differ only by the rows code plus the `refuted_by` instrumentation
    (`git diff 246b883 e4830fe` touches `construct.jl`, `cuts.jl` and `tree.jl`
    only);
  - ran alone on the same host (the journal shows `rti-iptl600` 21:41-22:33 and
    `rti-final` 14:22-16:32);
  - failed construction attempt 1 on step 4's 600 s limit and produced their
    plan on attempt 2, at 2766.8 s and 2771.5 s.
- **Fix:** rewrite as: "One run each: 156 trucks against 160, and 913 of 5 700
  proposed compositions proved unplaceable against 1 440 of 6 812. The cost,
  2.6% lower relative to the run without the rows, is not claimed." Also see
  D3.

**FIXED** with the suggested structure: "156 verified trucks against 160, and 913 of 5 700 compositions submitted to the oracle proved unplaceable against 1 440 of 6 812. One run each; the cost, 2.6 % lower relative to the run without the rows, is not claimed." "Submitted" rather than "proposed": `pack_calls` also counts the ray-bisection probes of the dominance cuts, which the partition did not propose.

### m16. Section 9.1's certification policy contradicts two other statements

- **Where:** `sec_experiments.tex:47-57`.
- **As printed:** column generation runs to a certificate at the root, the
  one-truck-fewer region and a full-depth leaf "and nowhere else". Certifying
  every node "is not affordable under 3D loading, where one geometric sweep
  costs minutes, and the two modes are reported under one policy".
- **Evidence:**
  1. Under 3D loading every region certificate is volumetric
     (`sec_branching.tex`, "What the tree reports"; `tree.jl` `rcert`). It
     costs seconds: rung 1 made 78 region certificates in `t_rcert` 15.93 s.
  2. Table 12's run certified every popped region (its caption). So 3D
     certification of every node was affordable, and the 3D tables are not
     under one policy.
  3. "Nowhere else" omits the end-of-search frontier sweep, which Table 6's
     caption and Section 8 describe, and the in-region re-pricing of a failed
     level-1 region (`tree.jl:1087-1100`; not exercised on these runs).
- **Fix:** state the second implementation's policy fully, drop the
  affordability claim for 3D, and say that Table 12 used a different policy.

**FIXED.** Section 9.1 now lists every place the second implementation certifies (root, one-truck-fewer region, full-depth leaf, frontier sweep), says every 3D certificate is volumetric, and says Table 12's runs certified every popped region. The 3D affordability claim is dropped. NOT ADDED: the in-region re-pricing of a failed level-1 region, which these runs never exercised.

### m17. "Nor does the geometry take the time" compares placeability with geometric pricing

- **Where:** `sec_experiments.tex:296-298`.
- **As printed:** "placeability calls cost 0.2 to 14.2 s per run, against 120
  to 872 s of column generation".
- **Evidence:**
  - Under 3D loading, root column generation prices geometric columns, so it
    *is* geometry.
  - `root_cg_stop` is `deadline` on all five rows, so 120-872 s is the 15%
    budget share plus rung 5's 360 s re-pricing (LOG lines 1021-1022). That is
    an allocation, not a measured cost.
  - The geometric starting pool took a further 26-29 s per rung (LOG "geometric
    pool" lines).
- **Fix:** "placeability tests cost 0.2 to 14.2 s per run", and say that column
  generation's time is its budget share.

**FIXED.** "Nor do the placeability tests take the time ...: they cost 0.2 to 14.2 s per run, while column generation, geometric pricing included, spends the budget share it is given". The source comment records `root_cg_stop` deadline and rung 5's 360 s re-pricing.

### m18. Older sentences a referee will still hit (not introduced by the revision)

- `sec_intro.tex:68-71`: "on the sizes reported here it returns a verified
  plan and a certified bound". Table 12 has 5 rows with no plan and 1
  unproved.
- `sec_abstract.tex:21-23`: "on those instances". The 724 loads are over the
  36 instances, but the preceding clause is about four ladder instances.
- `tab_protocol.tex:28-30`: "no number reported here rests on a seed, every
  stage being ... stopped on a declared budget". A solve stopped on a budget
  does depend on the seed.

**FIXED, all three.** Introduction: "on the sizes reported here it proves no optimum" (the claim that every run returns a plan and a bound is cut). Abstract: "dispatches on those instances" -> "on the thirty-six"; the 31-of-36 sentence is untouched. Table 4: the clause "no number reported here rests on a seed ..." is cut, and the matching "nothing reported rests on one" in Section 9.1 becomes "the solvers' defaults apply".

### m19. Table 6's "like with like" spans two solver versions

- **Where:** `tab_protocol.tex:23`.
- **Evidence:** the method's columns are Gurobi 13 (Julia); the compact
  model's are Gurobi 12.0.1 (`results/compact_ref/*.json`, `env.gurobi`). Now
  that the revision prints versions, the claim needs the qualifier.
- **Fix:** "same budget, threads and cap; Gurobi 13 against 12".

**FIXED.** Table 4: "compare like with like in budget, threads and cap, under Gurobi 13 against 12".

---

## Needs the author's decision, not a fix

- **D1. The 36-instance benchmark** (B1). Either re-run it in the final Julia
  configuration (about 9 h at 900 s per instance, run alone), or demote it to
  "an earlier configuration" and remove it from the abstract. Table 9, the
  Section 9.3 paragraph and the placeability census sentence follow whichever
  is chosen.

  **AUTHOR.** Not acted on.

- **D2. Citing the Python reference ladder in prose** (`sec_experiments.tex:
  231-237`). It reports a 54-truck verified plan on rung 4 and 160 on rung 5,
  which sits awkwardly with "the Julia implementation is what the paper
  reports".
  - If kept, the 54-truck plan must enter the interval and the gap attribution
    (M1).
  - If dropped, rung 4's comparison disappears; nothing else depends on it.

  **AUTHOR.** The citation is kept; M1's wording fix makes the paper consistent with it either way.

- **D3. The server arms.** The rows were chosen from five arms over two rounds
  (ARMS) and then confirmed by one run. The arms both corroborate and
  complicate the paper's single-pair ablation:
  - on rung 5, geomboth 156 (r1) and geompart 157/158, against base 159 and
    "no plan";
  - on rung 4, geomboth gave 54 on r1, which the final run did not reproduce
    (55);
  - B's rung 4 (79 608.45, reproduced to the cent three times) is cheaper than
    the final 79 703.31. That is sub-percent, so not a claim either way.

  Disclosing the selection, and citing the arms as corroboration with their
  caveats (shared 96-CPU host, unpinned pricing in r1), would pre-empt a
  referee's "how was this component chosen?".

  **AUTHOR.** The server arms are not mentioned in the paper.

- **D4. The re-pack baseline** (M3). Rebuild it with the method's own current
  partition, or say in the conclusions that the comparator uses the earlier
  machinery.

  **AUTHOR.** Not rebuilt; M3's wording now says what the baseline is.

- **D5. "Exact given compute"** (`sec_abstract.tex:14-16`,
  `sec_intro.tex:68-70`, and Section 8 "reachable in principle"). In the code,
  a region where the construction returns no plan, and that holds no
  incumbent to partition around, takes the `continue` at `tree.jl:1129-1135`.
  That skips the last-resort certificate, and the region is counted dropped
  (`regions_dropped` 1/2/5/0/1 on the ladder). With unlimited compute such a
  region is neither split nor refuted, so completeness as implemented rests
  on the primal heuristic.
  - This is not an argument against the tree, which stays.
  - It is a gap a referee who reads the code will name. Either qualify the
    claim ("given compute and a construction that finds a plan in every
    non-empty region"), or have the tree certify or split such regions.

  **AUTHOR.** "Exact given compute" (abstract, introduction, Section 8) is unchanged.

- **D6. A stack-aware volumetric baseline and bound.** The revision adds a far
  stronger set of necessary conditions, (88)-(89), but uses them only as a
  primal device.
  - The skill's anticipated next straw-man criticism is a volumetric model
    deflated by exactly such rows.
  - The 14.5% and 10.7% gaps are where M2 says those rows could act on the
    bound.
  - Whether to run either before submission is a scope decision.

  **AUTHOR.** No stack-aware baseline or bound was run; M2 now says only where the rows are and are not implied.

- **D7. The 1 GB `SoftMemLimit` in `price_volumetric`** (m7). Fix it and
  re-run, or declare it. ARMS already proposes the fix: free each pricing
  model after the call, or drop the cap, and set pricing threads explicitly.

  **AUTHOR.** The cap is unchanged in the code; m7 declares what ran.

---

## Checked and found correct

Absence of a finding below means the item was checked.

**Every number the revision added or changed**, recomputed from CSV, LOG, 600,
REPACK, BENCH and PYL. Rounding is correct throughout.

- **Table 7, both blocks:**
  - z, trucks, verified, LB and gap on all five rows (`global_lb` = `root_lb`,
    `root_proved` true, gap = `gap_pct`);
  - root (t) from LOG's "root plan" lines: 18797.59 at 142.0 s, 38867.74 at
    154.4 s, 52215.42 at 298.4 s, 92251.99 at 948.4 s, 223944.2 at 2771.5 s;
  - tree % = (root - z)/root: 0.0034, 0.0342, 0.1889, 13.6026, 0;
  - nodes, dropped, open and wall.
- **Table 10:** the method columns and all five intervals. Rows 3 and 5 take
  the method's bound, because the compact volumetric bounds 51 944.22 and
  199 518.04 are lower.
- **Table 11:** 55 and 156, and the savings: 25.68%, 26.42%, 17.95%, 18.64%.
- **Section 9.3:**
  - 0.0075-0.163% and 14.5/10.7%;
  - "two level-1 nodes", 67 -> 65 -> 55 (LOG 812, 850, 898);
  - eleven NEW INCUMBENT lines on rung 3;
  - 3060 - 2771.5 = 288.5 s;
  - volumetric certificate 1.3/3.1/3.9/9.6/13.8 s after root column
    generation;
  - `t_cg` 120-872 s and `t_pack` 0.2-14.2 s;
  - 668.7, 919.2 and 938.3 s;
  - 8335 = 6749 + 485 + 1101, `pack_undecided` 0 on every row;
  - `ref_2D` = `pack_unplaceable` with every `ref_T*` = 0, so "none by the
    arithmetic conditions alone" is right;
  - 160 / 230 020.08 / 1440 against 156 / 913, and 2.64%.
- **Section 9.4:** three bounded leaves at 18 795.56/.54/.54 (LOG lines 30, 46,
  120) and 0.102%.
- **Conclusions:** 25.7% and 26.4% are correct arithmetic.
- **"Two to fifteen regions"** (`sec_branching.tex:110-113`): `parity_vol.csv`
  7/7/8/8/7/15 and CSV 6/9/6/7/2.
- **The 274 s on I_12_12** (`sec_experiments.tex:183`): LOG line 699.

**Provenance and comparability**

- Every CSV row has `src_tree e4830fe`, `geom_rows both`, `load_path include`,
  10 threads, `mem_gb` 20 and `ip_tl` = tl/6. `complaints` is 0, `root_proved`
  true and `proved` false on all five rows. No row exceeds its budget.
- The launcher at `f4c3b18` matches the flags.
- The user journal shows `rti-final` alone, 14:22:31-16:32:55, and
  `rti-iptl600` alone, 21:41:33-22:33:27, on the same host `jj-MS-7B86`.
- The ablation pair differs only in the rows code (`git diff 246b883 e4830fe`).
  `placeability.jl` and `geometry.jl` are identical in the two trees.
- The withdrawn 155-truck row (`rung5_repeats.csv`) is cited nowhere in the
  paper.
- The 120 s and 600 s sentence in Table 4 matches B (rung 5 `has_plan false`)
  and 600 (160 trucks).
- Table 12's configuration statement matches all 36 BENCH config blocks. Row
  1's cross-reference holds: 68 nodes, 18 795.54.
- Gurobi 13: `Manifest.toml` has `Gurobi_jll 13.0.3`, and the artifact on disk
  is `gurobi1303`.

**The method text against the code**

- *Which rows go where.* Rows (88) and (89) are added per truck in step 3's
  partition (`cuts.jl` `add_stack_relaxation!(strong=true)`). Their sum goes
  into step 2, with integer stack and orientation counts and the fibre
  right-hand sides times `n_gt` (`_add_aggregate_stack_area!(strong=true)`).
  They appear in neither pricing nor the master. That matches the text,
  Algorithm 3 and Figure 6's arrow.
- *Validity arguments.* These are correct:
  - weight threshold: a stack's total weight is at most W^max (nomenclature);
  - height threshold;
  - L^max as a two-resource knapsack over all of C_f, which bounds any subset
    (the code caches over every commodity of the class);
  - the fibre lemma, which matches the `dff_rows` docstring and is Fekete and
    Schepers' 2004 conservative scales with v = id;
  - superadditivity of floor, as the paper says. The `dffs` docstring's
    "subadditive" is a code-comment error only.
- *Step 2's aggregation.* Valid even though step 3 is a cover: Q* <= Σ_k a_k,
  so the aggregate row is weaker than the sum of the per-truck rows.
- *Redundancy in the geometric master.* Correct as stated (only the pricing
  and volumetric parts are wrong; see M2).
- *The decoupled oracle* (`placeability.jl` `decoupled_pack`, `pack_rects`):
  - it refutes at a lower bound on the stack count;
  - it answers yes only at an exact stack count and after the verifier;
  - normal-pattern positions make the cell-packing model exact, since edges
    land on grid lines.
- *Timers.* Leaving `t_build` unused is right: closures `pack` and `build`
  share `_t` (`tree.jl:666`, `702`, `713`, `768`). `t_pack` is sound, because
  nothing resets `_t` between its two reads.

**References.** All five new entries match Crossref on authors, title, venue,
volume, issue, pages and DOI. Clautiaux et al. was online in 2008 and is in
print as 2010, volume 179. Each is cited for what it contains:

- DFFs: Fekete and Schepers (2001); Clautiaux, Alves and Valério de Carvalho
  (2010);
- conservative scales: Fekete and Schepers (2004);
- normal patterns: Christofides and Whitlock (1977);
- the position-indexed non-guillotine model: Beasley (1985).

**Standing decisions in the revised text**

- The fleet tree is nowhere framed as optional or as an ablation. The rows
  ablation is of the rows, not the tree.
- No hand-seeded pool appears.
- "No plan" is used for X <= 155. "Infeasible" appears only for the fleet
  floors, which are proofs.
- "Bound" is used only for certified values.
- No optimality is claimed where `proved` is false. Row 1's optimum is
  attributed to the compact volumetric proof.

The one standing-decision violation found is B1, together with D2.

**Build.** Covered in the header: exit 0, 34 pages, overfull boxes unchanged
from `100c362`.
