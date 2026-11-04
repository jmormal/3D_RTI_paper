# REVIEW: the AMM restructure (`7b98fca..7327156`) and Table 8 (`10ed61c`)

Second referee pass, 2026-11-04. I checked the paper against the result
files and the code, not against `CHANGES-final3d.md`. I edited nothing in the
manuscript.

Line numbers are those of the `.tex` files at `7327156` unless a finding says
`10ed61c`.

Sources used:

- **CSV**: `/home/jj/3D-RTI-final/results/julia/final/ladder_3d_final.csv`
- **LOG**: `.../final/ladder_3d_final.log`
- **R40**: `.../final/repack_volumetric_r40.json` (also `_r3`, `_r12`)
- **PAR**: `/home/jj/3D-RTI/results/julia/parity_vol.csv`
- **CREF**: `/home/jj/3D-RTI/results/compact_ref/*.json`
- **C3D**: `/home/jj/3D-RTI/results/compact_3d/*.json`
- **CAT**: `/home/jj/3D-RTI/results/catalogue_realism.json`
- **code**: `/home/jj/3D-RTI-final/julia/src` (`src_tree` e4830fe) and
  `julia/scripts/{ladder_3d_final.sh,run_benchmark.jl,repack_volumetric.jl}`

**Counts: 0 blocking, 5 major, 17 minor**, plus 9 items for the author. The
notation list (N1–N22) has 22 entries.

**Build:** clean builds of `7327156` and `10ed61c` (`git archive` into a
scratch directory, then `PATH=/usr/bin:$PATH latexmk -pdf Renderer.tex`) both
exit 0.

- Both give 30 pages, with no undefined reference or citation.
- Each build's text is identical to the `Renderer.pdf` committed with it.
- There are two overfull boxes:
  - 6.75 pt in `sec_litreview.tex:106-107`, inherited from `old/`;
  - 1.35 pt in `tab_nomenclature.tex:60`.
- There is one biber warning: the ISBN of `Zhao2022LearningPacking` is invalid.
- There is one LaTeX warning: "No \author given".

---

## Major

### A1. Algorithm 1 prunes a region on an infeasible LP, which Section 7 and the code both say is not a proof

- **Where:** `Alg_certify.tex:10` and `:23-24`, against `sec_branching.tex:49`.
- **As printed:**
  - Algorithm 1, lines 4–5: "if infeasible then return (∞, true) ▷ region
    refuted".
  - Lines 17–18: "if artificials > 0 then return (∞, true)".
  - Section 7, two pages later: "An infeasible node relaxation is not a proof
    that a region is empty, stock non-negativity being rigid".
- **Evidence:** `colgen.jl` `certified_lb` (lines 465-467 and 485-487) does
  neither of these:
  - an LP that is not `OPTIMAL` (infeasible included) returns
    `CGResult(0.0, false, …, :lp_not_optimal)`, which is no bound and no
    refutation;
  - positive artificials at the end of a sweep return `CGResult(Inf, proved,
    …)`, where `proved` is the sweep's own flag and not `true`.

  As printed, the algorithm would prune any region whose pool LP is infeasible,
  and would claim a refutation even after an unproved sweep. The certificate
  every bound in the paper rests on is described with two unsound returns. The
  file predates this pass (unchanged since `020ca25`), but nothing in Section
  5.4 now overrides it.
- **Fix:**
  - Line 5: "return (0, false) ▷ no verdict: the pool may be unable to serve
    R".
  - Line 18: "return (∞, proved)".
  - If wanted, add "regions are refuted only by arithmetic (Section 7)".

- **Resolution (FIXED).** Algorithm 1 now matches `certified_lb`: an LP not
  solved to optimality returns (0, false), "no verdict: the pool may be unable
  to serve R"; positive artificials after the sweep return (∞, proved), "if
  proved, no plan over Ω^g lies in R"; a deadline returns (raw LP value, false).
  §5.4 says the same. The suggested "regions are refuted only by arithmetic" was
  not added, because `tree.jl` does prune on `proved && isinf(bound)`; instead
  §7's "only arithmetic is" now reads "only a proved sweep that leaves
  artificials positive is, or arithmetic".

### A2. The restored literature review credits 3D-SCPTOP with constraints the paper says it does not have, and omits the RTIs it does have

- **Where:** `sec_litreview.tex:116`, `:244-252` (Table 1, 3D-SCPTOP row) and
  `:284` (Table 2, 3D-SCPTOP row), against `sec_problem.tex:28-30` and Section
  4.
- **As printed, with the evidence for each:**
  1. "The key distinction of the 3D-SCPTOP model … is the incorporation of 3D
     constraints and **vertical stability constraints**."
     - Section 3 (`sec_problem.tex:30`) says the stacks are self-supporting,
       "so no vertical-stability constraint is needed".
     - Section 4 has none.
  2. Table 2 marks 3D-SCPTOP with **Load Balance X**.
     - The model has payload (41), crush (42)–(43) and the full-truckload rows.
       It has no weight-distribution, axle-load or centre-of-gravity row
       (`grep -i "balance\|axle\|distribution"` over `sec_model.tex` and
       `sec_pricing.tex` finds only "flow balance").
  3. Table 1 leaves the **RTIs** column of the 3D-SCPTOP row blank (source:
     the empty cell after the second "X" at line 250, `X & &`).
     - RTI management is the paper's subject, and Guo 2024 and Zhang 2024 carry
       "S|NF" and "M|NF" there.

  These are statements about this paper in restored text, which the brief says
  to flag. A packing referee will check Table 2's row against Section 4 first.
- **Fix (minimal edits):**
  - "3D loading constraints, with vertical stability ensured by construction
    (Section 3)";
  - remove the Load Balance X, or add a load-balance row to the model;
  - fill the RTIs cell with the code for multiple foldable types, defining the
    code in the table note.

- **Resolution (FIXED, three minimal edits).** "3D constraints and vertical
  stability constraints" -> "3D loading constraints, with vertical stability
  ensured by construction (Section 3)"; Table 2's Load Balance X removed;
  Table 1's RTIs cell filled with "M|F", defined in the note. Not changed and
  listed for the author: Table 1 also marks 3D-SCPTOP with "Inventory Capacity
  Restriction X", and Section 4 has no such row.

### A3. A review paper is cited as the fuzzy procurement model, as the coverage rule's source, and as the model the benchmark extends

- **Where:**
  - `sec_litreview.tex:20` ("present a fuzzy multi-objective integer linear
    programming (FMOILP) model …");
  - `:173` (Table 1 row);
  - `:404` (Figure 1, "With respect …");
  - `sec_model.tex:128` (the coverage wrap rule "following …");
  - `sec_appendix_scptop.tex:5` ("extends the procurement transport model of
    …").
- **Evidence:** every one of these uses key `Diaz-Madronero2014APlanning`.
  - That key is `@misc` "A review of discrete-time optimization models for
    tactical production planning" (Int. J. Prod. Res., `references.bib:593`).
  - The fuzzy procurement-transport model is `Diaz-Madronero2014AChain`, "A
    fuzzy optimization approach for procurement transport operational planning
    in an automobile supply chain", *Applied Mathematical Modelling* 38(23)
    (`references.bib:486`), cited correctly in `sec_intro.tex:8-9`.
  - The printed reference list shows both, as "Díaz-Madroñero, Mula, and Peidro
    2014" (the review) and "Díaz-Madroñero, Peidro, and Mula 2014" (the AMM
    model).
  - The error is inherited from `old/main.tex:41`. In a submission to AMM, it
    misattributes the journal's own paper in the model section and the
    benchmark definition.
- **Fix:** replace the key at those five sites. If the review is cited
  anywhere on purpose, give it a journal field.

- **Resolution (FIXED).** All five sites now cite `Diaz-Madronero2014AChain`.
  The review is cited nowhere else, so it leaves the reference list; its entry
  is an `@article` with a journal anyway. Table 1's codes for the AMM paper are
  listed for the author under D9.

### A4. 22 symbols and abbreviations are used before they are defined, or collide (list N1–N22 below)

- **Where:** throughout. The worst cases are Section 5.3–5.4 and the
  literature review.
- **Evidence:** read in order. The author's stated concern is readers new to
  the notation; the restored introduction does not help with Sections 5–7. The
  densest problems:
  - `w_c`, `γ_gc`, `π_ct` are used in (59) and Section 5.4 but defined at the
    head of Section 6;
  - `z_RMP`, `z_MP`, `z̄`, `c̄*_gt` are never defined, and `z̄` later means the
    incumbent;
  - the region `R` is used in Algorithm 1 two sections before regions are
    introduced;
  - three different caps are written `ū`;
  - two different things are written `ρ`;
  - the dispatch total is written `λ̄_gt` in 5.2 and `n_gt` everywhere else;
  - the literature review uses about a dozen C&P typology codes (SLOPP,
    SBSBPP, MSSCSP, MILOPP, …) that it never defines, since the sentence
    pointing to a defining table was removed with the non-existent table.
- **Fix:** see the list. Most items are a definition moved forward or a
  rename.

- **Resolution (FIXED, except N14).** See the resolution under the notation
  table below.

### T1 is the fifth major finding: see "Table 8 (10ed61c)".

- **Resolution (PENDING re-pack).** Not touched, as instructed.

---

## Minor

### a1. The optimality rule omits dropped regions

- **Where:** `sec_branching.tex:57-60`; `Alg_tree.tex:44-50`.
- **As printed:**
  - "Optimality is claimed only when the root is certified, no region remains
    open or merely bounded, and the incumbent meets the global bound".
  - Algorithm 2, line 35: "LB ← max{LB₀, min over the open and frontier
    bounds}".
  - Line 37: "… only if LB₀ was proved, S = ∅, no restart occurred and
    z̄ = LB".
- **Evidence:** `tree.jl:1431-1458`:
  - a dropped or unbounded region pushes the root bound into the minimum ("A
    dropped region and an unbounded open one are covered by the root bound
    alone");
  - `proved` requires `regions_dropped == 0 && unbounded_open == 0`.

  As printed, a region abandoned with no bound recorded (line 21's "continue
  if still none") drops out of the minimum, and line 37 can then claim
  optimality. Table 4 defines *dropped*, so the reader has the concept but not
  the rule. No run claims optimality, so no reported number is wrong.
- **Fix:** add "and no region was dropped" to both places, and "(a dropped
  region contributes its covering bound)" to line 35.

- **Resolution (FIXED).** §7: "a dropped region contributing the root's bound"
  and "no region was dropped" added to the optimality rule; Algorithm 2 line 35
  "▷ a dropped region contributes LB₀", line 37 "no region was dropped".

### a2. The Lagrangian bound lost its validity condition

- **Where:** `sec_master.tex:179-181`.
- **As printed:** "A sweep that still finds columns yields only the Lagrangian
  bound z̄ + Σ|K| min(0, c̄*_gt)".
- **Evidence:** the previous version (`7b98fca`, `rewrite/sec_master.tex:
  232-236`) said "a *complete* sweep …, provided each c̄*_gt bounds the pricing
  maximum from above". The code builds `rc` "from an upper bound on the pricing
  optimum — never from a load that was found" (`colgen.jl` `price_sweep`
  docstring), and only when every pair has an entry. Without the condition,
  the expression is not a bound. The symbols are also undefined (N16).
- **Fix:** restore "complete" and the "provided …" clause.

- **Resolution (FIXED).** "A complete sweep ... yields only the Lagrangian bound
  z_RMP + Σ|K| min(0, c̲_gt), with the artificials at zero and c̲_gt =
  η^K cK − μ_gt − ζ̄_gt formed from an upper bound ζ̄_gt ≥ ζ_gt on the pricing
  maximum, never from a load that was found".

### a3. The certification policy and the Murty depth cap are no longer in the printed text

- **Where:** `sec_branching.tex:46` ("where the filter allows and the policy
  admits the node"); `Alg_tree.tex:19` ("the policy certifies R") and `:35`
  ("below the cap"); Table 10.
- **Evidence:**
  - Which regions are certified (root, the one-truck-fewer chain, a full-depth
    leaf, the frontier sweep), and that the Murty depth cap is 6, appear only
    in `sec_appendix_impl.tex:13-14` as a `%` comment.
  - The m16 resolution of the previous pass ("Section 9.1 now lists every place
    the method certifies") did not survive the restructure.
  - Table 6's *open*, *dropped* and bounded counts cannot be read without the
    policy.
- **Fix:** one sentence in Section 7, or two rows in Table 10:
  - "certified regions: root, X ≤ X*−1, full-depth leaves, frontier";
  - "Murty depth cap: 6".

- **Resolution (FIXED).** One sentence in §7 (root, X ≤ X*−1, a region that
  can be split no further, the frontier; Murty depth 6, raised by 2 when the
  stack empties) and two rows in Table 10, both columns "same" (identical at
  10e633c). Algorithm 2's "below the cap" points to Table 10.

### a4. On I_8_5_2_90 the layer counts do not coincide

- **Where:** `sec_experiments.tex:157`.
- **As printed:** "The proof is available because this instance's container
  families have coinciding height- and crush-limited layer counts".
- **Evidence:** `instances/I_8_5_2_90.json`:
  - R2 is 1000 mm tall, so 3 layers by height. P6 (424.5 kg) and P8
    (477.4 kg) give floor(1200/w) = 2: crush-limited at 2, not 3.
  - R1 (1150 mm, 2 layers) has P5 and P7 at 3 by crush.
  - So the counts coincide for neither family across all its products. The
    crush limit binds on half of R2's products and the relaxation is still
    exact.
- **Fix:** state what does hold, or drop the causal "because". One version
  that holds: "the crush limit never halves a family's layer count".

- **Resolution (FIXED).** Verified: R1 2 layers by height, crush 2-3; R2 3 by
  height, crush 2 on P6 and P8. The causal sentence is replaced by "On this
  instance the crush limit never halves a container family's layer count, as it
  does on I_16_15_2_90_k25, where Section 8.5 measures the over-credit that
  causes."

### a5. Appendix A says products may use several RTI types, and its own rows do not allow it

- **Where:** `sec_appendix_scptop.tex:9`.
- **As printed:** "… and a product may travel in more than one RTI type".
- **Evidence:** the same paragraph says SCPTOP is "identical to the transport
  and inventory block of 3D-SCPTOP in every other respect". Section 4.7
  assumes "ρ is a partition", and (92) charges γ_{pρ(n)}, a single type per
  product.
- **Fix:** delete the clause, or say that it applies to the original model and
  not to the benchmark used here.

- **Resolution (FIXED).** The clause is deleted.

### a6. Figure 1 promises two things the paper no longer contains

- **Where:** `sec_litreview.tex:333` and `:354-361`.
- **As printed:** "Small Instances / Medium Instances / Large Instances", and a
  final "Managerial Implications" node.
- **Evidence:** Section 8 has the volumetric six and the five-instance ladder,
  with no small/medium/large split, and the paper has no managerial-
  implications section. Only the 36-instance benchmark now being re-run could
  support the first box.
- **Fix:** relabel the boxes to what Section 8 contains ("volumetric check",
  "3D size ladder"), and point "Managerial Implications" at Section 8.5's
  catalogue test, or remove it.

- **Resolution (FIXED in part, as instructed).** "Managerial Implications" ->
  "Is a Volumetric Capacity Adequate?" (Section 8.5's title). Small/Medium/Large
  Instances: AUTHOR (D5), left for the 36-instance re-run.

### a7. "Changes nothing where the crush limit does not bind" is stronger than the file

- **Where:** `sec_experiments.tex:187`.
- **Evidence:** on I_12_12_2_90_k25 at 600 s:
  - `compact_ab_I_12_12_2_90_k25.json` (with the stack rows): 34 trucks,
    52 117.80, bound 51 785.62;
  - `compact_ref_I_12_12_2_90_k25.json` (without): 34 trucks, 52 116.69, bound
    51 944.22.

  The fleet is unchanged, but the incumbent and bound are not.
- **Fix:** "leaves the fleet unchanged (34 trucks on I_12_12_2_90_k25 at
  600 s)".

- **Resolution (FIXED).** Verified both JSONs (34 trucks each, 600 s); "leaves
  the fleet unchanged where the crush limit does not bind (34 trucks on
  I_12_12_2_90_k25 at 600 s)".

### a8. A percentage with an unnamed denominator

- **Where:** `sec_experiments.tex:68`.
- **As printed:** "the decomposition's bound is 0.310% tighter".
- **Evidence:** (52105.31 − 51944.22)/51944.22 = 0.3101%. Over the method's
  bound it is 0.309%. Table 4 promises "the denominator named where it
  appears".
- **Fix:** add "relative to the compact bound".

- **Resolution (FIXED).** "..., relative to the compact bound."

### a9. The instances sentence reads as twelve instances

- **Where:** `sec_experiments.tex:32-34`.
- **As printed:** "the others cross (|N|,|T|) ∈ {…four pairs…} with 2.5, 5 or 8
  truckloads per period".
- **Evidence:** that reads as 4 × 3 = 12 instances. The tables use six:
  - k25 at 8_10, 12_12 and 16_15;
  - k50 at 16_15 and 24_20;
  - k80 at 24_20.
- **Fix:** name the six, or say "of which Tables 5–8 use …".

- **Resolution (FIXED).** The six are named by (|N|,|T|) and load, and the
  size ladder and its rungs are defined in the same place (N22).

### a10. A non sequitur on reproducibility in Appendix C

- **Where:** `sec_appendix_impl.tex:31-33`.
- **As printed:** "since the phases run on wall-clock budgets, a run is
  reproducible given a build and a machine".
- **Evidence:** wall-clock budgets are what make a run depend on execution
  speed. That is the old Section 9.1's point: "not deterministic across those".
  No final-configuration run was repeated.
- **Fix:** "since the phases run on wall-clock budgets, a run depends on the
  build, the machine and its load".

- **Resolution (FIXED).** "a run depends on the build, the machine and its
  load".

### a11. The bibliography carries malformed entries, inherited from the old references.bib

- Chen, Lee and Shen 1995 is printed as "EUROPEAN JOURNAL OF OPERATIONAL
  RESEARCH … Tech. rep., pp. 68–76".
- Dyckhoff 1990 is printed as "Tech. rep." (EJOR 44(2)).
- Paquay 2016 and X. Zhao 2016 have the journal name inside the title.
- Alonso et al. 2022 duplicates its title and adds "Second revision".
- H. Zhao et al. 2022 has no journal.
- The Díaz-Madroñero 2014 and 2015 reviews and Mosca 2019 are `@misc`, with no
  journal.
- Stadtler et al. carries "Vol. 5th Edition".
- Guo 2024 and Soysal 2023 have "doi: https://doi.org/…".
- **Fix:** correct the entries. This is not a rewrite of the restored text.

- **Resolution (FIXED).** Every listed entry corrected against Crossref, plus
  Zhao 2016's author list and Zhao 2022's two missing authors; URLs that
  duplicate a DOI dropped. The biber ISBN warning is gone.

### a12. Typos in the restored literature review

- **Where:** `sec_litreview.tex`.
- **Evidence:**
  - "3D-SCTOP" (line 265);
  - "Time-Depenent" (271);
  - "Biased Randomized Jey Genetic Algorithm", "Exact Metods", "HCLP (Hybrid
    Adaptive Large Neighborhood Search)" against "HALNs" in the table (289);
  - "thee stage" (99), "adaptaive … algoritm … Therfore … supsension" (104),
    "Thefore" (2.4).
- **Fix:** correct the spelling only.

- **Resolution (FIXED).** Spelling only, plus "Appoach" in Figure 1 and three
  misspelled typology codes (SSLOP, SBSPP, SLOP), counted under N3/N4.

### a13. The abstract sits exactly at the 250-word limit

- **Where:** `sec_abstract.tex`.
- **Evidence:** 250 words from the built PDF with hyphenation joined, 251 if
  "3 600" counts as two.
- **Fix:** trim one or two words for margin.

- **Resolution (FIXED).** "dubbed", "the problem" -> "it" and "container" cut:
  247 words from the PDF ("3 600" as one), 248 with it as two.

### a14–a17 are in the Table 8 section (T2–T5).

---

## Table 8 (10ed61c)

Line numbers in this section are those of `10ed61c`.

### T1 (major). The re-packed row is not a feasible plan, and the qualifier about its cost is false

- **Where:**
  - `tab_repack.tex:29-35` (caption): "the partition being a cover, it
    delivers 24 and 131 units more, and its cost charges cK per extra truck but
    not the holding cost of those units, which understates it";
  - `sec_experiments.tex:169-178` (§8.5): "… the re-packed cost omits the
    holding cost of the 24 and 131 units the cover over-delivers, which
    understates it";
  - and the "saving on cost" cells.
- **Evidence:**
  1. **All over-delivered units are folded RTIs.** In R40 `per_pair`, the
     over-delivered units by direction are `{p: 0, s: 24}` and
     `{p: 0, s: 131}`. Holding cost "falls on erected RTIs" (Section 4.2,
     `sec_model.tex:53`). So nothing is omitted, and the qualifier "which
     understates it" is false.
  2. **The surplus breaks coverage stock (19).** I replayed the inventory rows
     (8)–(19) of Section 4.3 on both delivery vectors, with my own script and
     the instance JSONs.
     - The replay reproduces the compact plans' inventory costs to the cent
       (23 139.17 and 63 266.21), with no violation.
     - With the re-packed deliveries, the plant's folded stock falls below its
       requirement Ie_ptr ≥ sse_ptr at:
       - I_16_15_2_90_k25: R1, t = 7, 58 < 79;
       - I_24_20_2_90_k50: R1, t = 5, 155 < 159; R2, t = 7, 121 < 171; R2,
         t = 14, 93 < 167.

     The plant ships empties it needs for its own coverage.
  3. **No plan-level check exists.** `repack_volumetric.jl` checks only
     `verify_column` per truck; there is no `verify_plan` and no inventory
     replay. `complaints 0` therefore means "every truck is legal", not "the
     plan is feasible".
  4. **Section 7.2's justification does not carry over.** "A cover is sound
     because … over-delivery is feasible" (`sec_branching.tex:127`) holds in
     the method only because step 4 re-solves the integer master with the
     inventory rows. The re-pack has no step 4.

  The trucks in row 2 carry at least the volumetric quantities. They do not
  form a plan of 3D-SCPTOP, and the cost compared against the method's
  verified plan is the cost of an infeasible schedule. The direction of the
  error in the saving is unknown: a cover that respects the plant's coverage
  could need more trucks or a different split.
- **Fix, one of:**
  - (a) Constrain the supplier-bound cover so that the surplus never takes the
    plant's folded stock below `sse` (or require exact quantities for
    empties), then verify the re-packed dispatch with the method's plan
    verifier.
  - (b) Run step 4 on the re-packed parts with the volumetric quantities as
    lower bounds.
  - (c) Keep the counts but call them "trucks needed to carry at least the
    volumetric quantities". In that case, drop the cost-saving cells and the
    "understates" clause, and say the surplus empties violate the plant's
    coverage at one and three (type, period) points.

  Contribution (iv)'s "we quantify what repairing them costs" depends on this.

- **Resolution (PENDING re-pack).** Not touched, as instructed (D3).

### T2 (minor). The conclusions drop the qualifiers that Section 8.5 and Table 8 carry

- **Where:** `sec_conclusions.tex:13-18`.
- **As printed:** "the method plans with 9.8% and 16.1% fewer trucks than
  re-packing two volumetric plans' quantities into loadable trucks used".
- **Evidence:** both qualifiers are missing: that the re-packed count is an
  upper bound (so the saving may be overstated) and that I_24_20's plan is a
  memory stop. T1 adds that the re-packed trucks are not a feasible plan.
- **Fix:** "up to 9.8% and 16.1% …", or state the comparison as trucks carrying
  at least the same quantities.

- **Resolution (FIXED, numbers unchanged).** "the method plans with up to 9.8%
  and 16.1% fewer trucks than re-packing ..., the re-packed counts being upper
  bounds and the second volumetric plan a memory stop (Table 8)". The T1
  qualifier waits for the re-pack.

### T3 (minor). Contribution (iv) says "independent placeability proof"; the check uses the method's own oracle

- **Where:** `sec_intro.tex:14`.
- **As printed:** "We show by independent placeability proof that …".
- **Evidence:** the Table 8 caption says "proved unplaceable by the method's
  placeability oracle". R40 confirms it: `oracle "pack_witness (shelf, then
  decoupled)"`, `src_tree` e4830fe. The proof is independent of the volumetric
  model, not of the method. When the old introduction was written, the check
  used a different oracle.
- **Fix (minimal):** "by placeability proof". Its last clause stands or falls
  with T1.

- **Resolution (FIXED).** "by placeability proof". Its last clause waits for T1.

### T4 (minor). Appendix C's re-pack row omits how the truck count is searched

- **Where:** `sec_appendix_impl.tex:66` (row "check and re-pack, Table 8").
- **Evidence:** `repack_volumetric.jl` "starts at the fewest trucks volume and
  payload allow and adds one until a cover is found", up to `max_extra` 12 per
  period (R40 config). The method's step 3 tries n*, n*+1 and n*+2 (Table 10,
  step 3 row). The reader will assume the two procedures are the same.
- **Fix:** add "from the volume/payload minimum, up to +12 trucks per period".

- **Resolution (FIXED).** Table 10's re-pack row adds "trucks per period from
  the volume and payload minimum up to +12" (R40 `max_extra` 12).

### T5 (minor). The over-delivered units are uncharged because they are folded RTIs, and the text should say so

- This part of T1 stands on its own if T1 is fixed by option (c).
- **Where:** `tab_repack.tex:31-33`; `sec_experiments.tex:177-178`.
- **Fix:** "the 24 and 131 surplus units are folded RTIs, which carry no
  holding cost" in place of "which understates it".

- **Resolution (PENDING re-pack).** Not touched, as instructed.

### Table 8: checked and correct

- 41 of 45 and 119 of 137 proved unplaceable, none undecided (R40
  `dispatched_verdicts`).
- Rows 1–3: 45/137, 61/186 and 55/156 (CREF `trucks`, R40 `repacked_trucks`,
  CSV `trucks`).
- Savings: (61−55)/61 = 9.84%, (186−156)/186 = 16.13%,
  (84139.17−79703.31)/84139.17 = 5.27% and
  (249266.21−223944.20)/249266.21 = 10.16%, each relative to row 2 as stated.
- 24 and 131 surplus units.
- I_24_20's plan is a memory stop (CREF `MEM_LIMIT`). I_16_15's plan is a
  proved optimum (`OPTIMAL`, bound = obj).
- "A volumetric optimum can be a plan nine tenths of which cannot be loaded":
  true. On the proved optimum 41/45 = 91.1% are proved unplaceable. On the
  memory-stop plan it is 86.9%, which the sentence does not claim.
- "The re-packed count is an upper bound … on I_24_20 in particular": true and
  consistent with R3, R12 and R40. The count falls with the round cap (65 →
  62 → 61 against 200 → 190 → 186), so it is not settled, and least settled on
  I_24_20.
- Appendix C's row matches the R40 config: check 60 s per load, 600 s per
  partition call, 40 rounds, pack 20 s, 10 threads, 20 GB, geometric rows on,
  `src_tree` e4830fe (the final method's).
- At `10ed61c` the printed text contains:
  - no first or Python implementation;
  - no commit hash;
  - no `\Pending` other than the repository URL.
- The abstract's sentence is qualitative and true. The abstract stays at 250
  words.

---

## Notation: symbols used before their definition, or colliding

In reading order. "Define at" gives the earliest natural place.

| # | where first used | symbol | problem | define at / fix |
| --- | --- | --- | --- | --- |
| N1 | `sec_intro.tex:6` | RM, FG | never defined | same sentence: "raw materials (RM) … finished goods (FG)" |
| N2 | `sec_litreview.tex:34`; Table 1 header | SC | "supply chain", never defined; collides with SC, the coverage window (Table 3) | spell out "supply chain" in the review |
| N3 | `sec_litreview.tex:75` | SLOPP, SKP | Wäscher codes, never defined | one sentence after the typology description in 2.3, or a note under Table 2 |
| N4 | `:77`, `:81`, `:88`, `:99`, `:104`, `:106`, Table 2 | SBSBPP, MBSBPP, SSLOP(P), SBSPP, MSSCSP, 3D-BPP, 3D-BSDPP/BSDPP, SSSCSP, MILOPP | never defined | same place as N3 |
| N5 | Table 1 | TO, S\|NF, M\|NF, ILP | not in the table's abbreviation note | the note |
| N6 | Table 2 note | HCLP vs HALNs | the note defines a code the table does not use | the note |
| N7 | `sec_problem.tex:13` | lt | used in Section 3, defined in Table 3 | Section 3: "a lead time of lt periods" |
| N8 | `sec_model.tex:11` | G, K, T | used one paragraph before "Throughout, g ∈ G …" (line 15) | move the sets sentence first |
| N9 | `Tikz_blocks.tex:66` (Fig. 2) | Q | "shared V̄, I, Q": Q is not defined until §7.2 (Q*_gt) | replace with V̄ or drop |
| N10 | `sec_master.tex:86` | λ̄_gt | the dispatch total, written n_gt in Table 3 and in §7 | use n_gt |
| N11 | `sec_master.tex:136-147`, `:154`, `:166` | w_c, γ_gc, π_ct | defined only in §6 (`sec_pricing.tex:11-14`) | move the commodity convention to §5.1, where c is introduced |
| N12 | `sec_master.tex:136` | ρ^g_ct | collides with ρ(n), the RTI type of n | rename (e.g. D^g_ct) |
| N13 | `sec_master.tex:140` | ū^g | third meaning of ū (also ū^V_n, ū^e_r in Table 3, ū_gc in §7.2) | rename |
| N14 | (54)–(59) vs Table 3 and text | λ^g_ωt vs λ_gωt | index position differs | one form |
| N15 | `sec_master.tex:172` | z_RMP, z_MP | undefined; given Proposition 1, z_MP reads as the MP optimum, making "≤ z*" an identity | "LP values of RMP and MP" |
| N16 | `sec_master.tex:180` | z̄, c̄*_gt | undefined; z̄ is the incumbent in Algorithm 2 | define (RMP LP value; pricing upper bound on −reduced cost), rename one |
| N17 | `Alg_certify.tex:2` (§5.4) | 𝓡 (region) | regions are introduced in §7 | Algorithm 1's caption: "a region 𝓡 (bounds on the fleet vector; root = none)" |
| N18 | Table 5, §8.2 | SCPTOP | named only in Appendix A's title | at Proposition 2 or §8.2: "the volumetric model SCPTOP (Appendix A)" |
| N19 | `tab_volumetric.tex:33` | z_DW | undefined | "z of the method" |
| N20 | `sec_branching.tex:214` | ℓ | the DFF threshold; collides with ℓ_ir (layers, §5.1) | rename (e.g. λ̂, or τ if not used elsewhere) |
| N21 | `sec_branching.tex:171-179` | c ∈ f | (87) uses it; C_f is defined after (87), at line 181, and (88) uses c ∈ C_f | define C_f before (87) and use it there |
| N22 | Table 6 caption; §8.3 | size ladder, rung | never defined; §8.1 describes the family only | §8.1: "the five instances of Table 6, the size ladder (its rungs)" |

**Resolution of N1–N22** (details and old/new text in `CHANGES-final3d.md` §17):

- N1–N13, N15–N22: **FIXED** as proposed, with these choices:
  - N3/N4: one sentence after the typology defines SLOPP, MILOPP, SKP, SSSCSP,
    MSSCSP, SBSBPP, MBSBPP and 3D-BPP; BSDPP goes in Table 2's note; "SSLOP",
    "SBSPP" and Table 2's "SLOP" were misspellings of SLOPP and SBSBPP
    (Silva et al. 2019 compare methods for the SLOPP and the SKP).
  - N5: TO, S/M, NF/F and ILP added to Table 1's note; their meanings are
    inferred from the rows, so they are listed for the author (D9 below).
  - N12: ρ^g_ct -> β^g_ct.
  - N13: the cover cut's ū^g -> ν^g; the placeability cap ū_gc -> ā_gc (the bound
    on a_kc); ū^V_n and ū^e_r in Table 3 are the only ū left.
  - N16: z̄ in §5.4 -> z_RMP, and c̲_gt defined; z̄ is only the incumbent now.
  - N20: ℓ -> ξ, as the thresholds of (88).
- N14: **NOT CHANGED.** Every occurrence in the sources is λ^g_ωt (Table 3,
  (54)–(57), §7); there is no λ_gωt to unify.

---

## Needs the author, not a fix

- **D1. AMM front matter and statements.** The Guide for Authors page returns
  403 to direct fetches, and web search surfaced only third-party extracts.
  These are standard items of Elsevier guides, **to confirm against AMM's
  page**:
  - a title page with authors, affiliations and corresponding author (the
    build warns "No \author given");
  - Declaration of competing interest;
  - CRediT author statement;
  - Funding;
  - Data availability (the `[repository URL]`);
  - the Elsevier declaration on generative AI in the writing process, relevant
    here;
  - appendix numbering as Eq. (A.1) and Table A.1: the paper continues the
    main sequence, with (91)–(94) and Tables 9–10 in the appendices.

  Already compliant:
  - highlights: 5 bullets of 71–78 characters, in a separate
    `highlights.txt`;
  - keywords: 5;
  - abstract: 250 words (a13).

  - **Resolution (AUTHOR).** Not touched; the abstract is now 247 words (a13).
- **D2. Keywords** (restored verbatim). None names the method or the returnable
  items ("branch and price", "Dantzig–Wolfe decomposition", "returnable
  transport items"). Up to seven are usually allowed.
  - **Resolution (AUTHOR).** Not touched.
- **D3. T1's remedy:** constrained re-pack, step-4 re-solve, or counts only.
  - **Resolution (AUTHOR / PENDING re-pack).** Not touched.
- **D4. Contribution (iv)'s generality.** It rests on two instances chosen
  because the crush limit binds, plus the catalogue's κ ≥ 1. On I_8_5_2_90 the
  volumetric optimum equals the 3D optimum, so there a volumetric plan is
  attainable in 3D. The 36-instance census now being re-run is what could
  support "a volumetric representation … yields plans that cannot be loaded" in
  general.
  - **Resolution (AUTHOR).** Not touched; contribution (iv) changed only by T3.
- **D5. Figure 1's "Small/Medium/Large Instances"** (a6). Restore it after the
  36-instance re-run, or relabel now.
  - **Resolution (AUTHOR).** Left as instructed; only "Managerial Implications" was relabelled (a6).
- **D6. Rung 4's gap attribution.** It is "chiefly to the bound", which is
  honest as worded. Note, though, that the server rounds ran the *final*
  method (geomboth, `src_tree` e4830fe, three times the budget) and reached
  54 trucks at 79 004.13 on this rung (`server_arms/README.md`, r1). So at
  least 0.88% of the 14.5% is the plan's under more compute. It is not
  printable as is (different machine and budget), but it bounds what "chiefly"
  can mean.
  - **Resolution (AUTHOR).** Not touched.
- **D7. The 1 GB volumetric-pricing cap,** declared in Table 10 but not
  changed.
  - **Resolution (AUTHOR).** Not touched.
- **D8. The paper's title page,** if AMM's review is single-anonymized; see D1.
  - **Resolution (AUTHOR).** Not touched.
- **D9. Wrong-key fix (A3).** The fix is mechanical, but Table 1's row for that
  paper would then describe the AMM model correctly. Confirm the row's codes
  (SS, O, FTL, UH, FMOILP) against Díaz-Madroñero, Peidro and Mula (2014).
  - **Resolution (AUTHOR).** Crossref and the abstract confirm the paper (AMM
    38(23):5705–5725) and support SS, O, FMOILP, safety stocks (minimum stock
    coverage) and transport and inventory costs. Not confirmed: FTL (consistent
    with "minimise the number of trucks") and UH ("Unit Holding"; Appendix A's
    "rather than a maximum container count" suggests a unit-count capacity).
    Also for the author: Table 1's TO for Zhang et al. (2024) against §2.2's
    "operational level", and the readings of S/M and NF/F.

---

## Checked and correct

Absence of a finding below means the item was checked.

**Numbers, all traced and recomputed:**

- **Abstract:**
  - 0.0001%: (68139.21 − 68139.17)/68139.17 = 5.9e-7, PAR against CREF;
  - 12–156 trucks, 900–3 600 s, 0.0075–0.163%, 14.5% and 10.7% (CSV);
  - minimum fleets through twelve periods: LOG 15, 651, 705, requirement
    prunes from `_cover_rhs`, a volume/payload/count argument valid in 3D;
  - "no incumbent beyond five periods" (C3D: three time-limited runs with no
    solution; rung 5 not run);
  - the catalogue sentence (CAT: n_types 15, median 1.0, max 2.0, n_severe 4,
    n_under 0).
- **Table 5:** every compact cell (CREF `obj`, `bound`, `trucks`, `wall`,
  status) and every method cell (PAR `incumbent`, `global_lb`, `trucks`,
  `seconds`); ∆z; "within 0.31%"; "67, 496 and 87"; "0.76% wide".
- **Table 6 and §8.3:** as in the previous pass, re-verified against CSV and
  LOG. Unchanged: 13.6 / 0.19 / 0.034 / 0.003%, 288.5 s, 1.3–13.8 s,
  0.2–14.2 s, 669 / 919 / 938 s, 8 335 = 6 749 + 485 + 1 101.
- **Table 7 and §8.4** against C3D: 18 816.13 and 18 795.76, 67 674 / 92 092,
  119 424–322 734 variables, 6.1 / 2.7 / 4.5 GB, the rerun's
  `plan_verified true`, 0.102%.
- **§8.2:** 52 105.31 at 274 s (LOG 699) against CREF I_12_12's 51 944.22 at
  600 s.
- **§8.5:**
  - 70 435.04 and +2 296 (`compact_ab_I_16_15`, 600 s, `stack_rows`);
  - the P1 mechanism: 57 = floor(24000/419.2); 32 = LOG `p|P1=32`, bisected;
    4 layers at 650 mm; 2 under 1 200 kg; six of eight R1 products at 2;
  - κ statistics.
- **§4.5:** 0.55 × 97.92 × 579 = 31.18 t, and seven of the fifteen catalogue
  types denser than 579 kg/m³ (CAT: maxW over box volume).

**Appendix C, Table 10, against what ran:**

- 3D column against `ladder_3d_final.sh` (flags) and `run_benchmark.jl:161-164`,
  which passes only time limit, threads, memory, `certify_regions`, geometry,
  `geom_rows` and `ip_tl`. Every other value is a `solve_fleet_tree` default:
  - pool 10 s;
  - step 2 300/120 s, gap 1e-4;
  - partition 60 s, 2 rounds;
  - pricing 20 s, pack 20 s;
  - root CG and frontier reserve 15%;
  - 1 GB in `price_volumetric`.
- Volumetric column: "same" verified against PAR's code (`10e633c`): the same
  defaults, `ip_tl` 120, 1 GB.
- Compact row against CREF and C3D configs: Gurobi 12.0.1, 10 threads,
  `soft_mem_gb` 20, MIP gap 0, seed 0, 1 800 s (3D), 600–3 600 s
  (volumetric).

**Only the final method is described:**

- No commit hash or source-tree id appears in either build's text.
- At `7327156`, "first/Python implementation" occurs only inside `\Pending`
  (§8.1, Table 8, §8.5, conclusions). At `10ed61c` it occurs nowhere.
- No variant, ablation or tuning history is printed. The rows ablation, the
  pricing heuristic and the policy comparison are gone.

**Restored sections:**

- A word-level diff of `sec_intro.tex` against `old/main.tex:4-22` shows only
  the edits CHANGES §13 lists (method name, the 0.0001% figure, the case-study
  sentence, cross-references). None changes a meaning beyond what the method
  change requires.
- Contributions (i)–(iii) and the organisation paragraph match Sections 4–9.
  Appendices are not mentioned, which is acceptable.

**The resolutions of REVIEW-final3d that survived the later passes:**

- B1 (moot: the abstract reports the final ladder);
- M1 ("chiefly … nothing here proves that attribution"; "below the method's
  plan");
- M2 (rows not implied in the volumetric relaxation; conclusions consistent);
- m2 (*dropped* in Table 4);
- m3 (DFF as (u, U));
- m6 (Algorithm 3's caption);
- m7 (Table 10, with the 1 GB cap and the step-2 gap);
- m8, m9, m10;
- m13 (requirement prune in §8.3);
- m14;
- m17 ("placeability tests cost …").

Not survived: m16 (a3) and part of m4 (new collisions N20, N10).

**Qualifiers:**

- No optimality is claimed where `proved` is false (Table 6 caption, §7,
  §8.4).
- "No plan" is used for X ≤ 155.
- "Bound" is used only for certified values, plus the compact dual bounds with
  their stop status.
- The RMP value is "a filter, never a bound" (§5.4, Algorithm 2 line 9).
- The compact 3D model's bound is said to bound its restriction (Table 7
  caption, §8.4).
- Propositions 1 and 2 and their proofs are verbatim, and still match the
  text that uses them.
- The exceptions are A1, a1 and a2 above.

**Build hygiene:** cross-references and citations all resolve; there are two
overfull boxes (both minor); there is one biber warning.
