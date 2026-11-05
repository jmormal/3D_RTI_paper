# REVIEW (AMM, round 3): the 36-instance benchmark and the new Table 9 (`3f307d0`)

Third referee pass, 2026-11-05. I reviewed the snapshot
`/home/jj/3D-RTI-paper-review`, detached at `3f307d0`. I edited no `.tex`
file and committed nothing. Every number below was recomputed from the file
named. None was taken from a `%` comment or from `CHANGES-final3d.md`.

## Sources

| tag | path |
| --- | --- |
| **M** | `/home/jj/3D-RTI-final/results/julia/final/bench36/bench36_method.csv` |
| **C** | `.../bench36/bench36_compact.csv`, plus the per-run JSONs under `.../bench36/<machine>/compact/` |
| **J** | `.../bench36/bench36_joined.csv` |
| **CEN** | `.../bench36/census_compact36.json` |
| **LOGS** | `.../bench36/<machine>/logs/method_<instance>.log` |
| **README** | `.../bench36/README.md` |
| **MERGE** | `.../bench36/merge_bench36.py` |
| **R40X** | `/home/jj/3D-RTI-final/results/julia/final/repack_volumetric_exact_r40.json` |
| **LAD** | `.../final/ladder_3d_final.csv` and `.log` |
| **CAL** | `/home/jj/3D-RTI-final/results/julia/server_arms/preflight/calib_{1..5}.log` and `calib_pinned.txt`; `server_arms/README.md`; the launcher headers `.../final/server/bench36_worker.sh`, `bench36_remote_worker.sh`, `bench36_server.sh` and `compact36_laptop.sh` |
| **code** | `/home/jj/3D-RTI-final/julia/src` (see below) |
| instances | `/home/jj/3D-RTI-final/instances/I_*.json` and `instances_scale/` |

The checked-out `julia/src` is tree `5958641`. `git diff e4830fe 5958641`
touches only `construct.jl`, so `colgen.jl`, `tree.jl`, `master.jl`,
`pricing.jl` and `solver.jl` are the files the benchmark ran (`src_tree`
e4830fe on all 36 rows of M). Where `construct.jl` is cited, I read it at
e4830fe.

## Build

I extracted `git archive 3f307d0` into the scratchpad and ran
`PATH=/usr/bin:$PATH latexmk -pdf Renderer.tex`.

- It exits 0 with **33 pages** and no undefined reference or citation.
- Its text (`pdftotext -layout`) is identical to the committed `Renderer.pdf`.
- There are two overfull boxes, both inherited: 6.75 pt at
  `sec_litreview.tex:106-107` and 1.35 pt at `tab_nomenclature.tex:60`.
- The printed text has no commit hash.
- The only purple (`\Pending`) text is the two `[repository URL]`s.

**Counts: 0 blocking, 3 major, 9 minor.** There are also 12 items for the
author and a plan to cut the length.

---

## Overall assessment

The paper formulates a truck-indexed MILP for procurement transport with
returnable containers and stack-based 3D full-truckload constraints. It
obtains the Dantzig–Wolfe master by deleting the quantifier over trucks. It
solves the master by a branch and bound over the fleet vector, with a
flow-first construction as primal heuristic. It bounds the 3D problem from
the volumetric relaxation by inclusion (Proposition 2).

**Contribution and mathematics.** The contribution is clear and suits AMM.
The mathematics is sound. I found no error in:

- Proposition 1;
- the argument of Proposition 2;
- the certificate;
- the Lagrangian bound as implemented;
- the cover cuts;
- the fleet-vector partition (81).

The defects are statements that are narrower than the way they are used (b1)
or stricter than what the code does (B1).

**Numbers.** Every number in the following places matches its file:

- Sections 8.5 and 8.6;
- Tables 8, 9 and 12;
- the abstract, the highlights, contribution (iii) and the conclusions.

I checked Table 12 cell by cell: 36 rows of 12 cells each, plus the bold
marks.

**What a referee will push on** is what those numbers are allowed to mean:

1. **How two printed bounds were obtained.** On two benchmark rows the
   volumetric certificate ended unproved, yet the text says it "closes in
   seconds". The rule printed for the replacing Lagrangian bound (a complete
   sweep) is not the rule the code applies (B1).
2. **Whether the three machines got equal compute.** The server rows are 17
   of the 36 method rows and 19 of the 36 compact runs. They had 79–88% of the
   reference compute, which the paper describes as a calibrated budget (B2).
3. **The abstract's closing clause.** It presents as a finding about the
   industrial catalogue something that holds by the definition of κ and by
   Proposition 2 (B3).

None of these changes a number; all three change sentences. The paper is 33
pages against the author's target of 30. About 3.5 pages can go without
losing a claim (see "Format for AMM").

**Recommendation: minor revision.** The three major findings are corrections
an AMM referee will require. Each can be fixed without new runs. The only
optional run is a 20-second calibration, to give the budget conversion a
recorded source. If the editor treats the untested deflated-capacity baseline
(F3) as required evidence for contribution (iv), this becomes a major
revision.

---

## Major

### B1. Two printed bounds rest on a rule the paper does not state, and the volumetric certificate does not always close

- **Where:**
  - Section 5.4: `sec_master.tex:186-192` and `:234-237`;
  - Table 4, LB row: `tab_quantities.tex:11-14`;
  - Section 8.3: `sec_experiments.tex:139-141`;
  - Section 8.5: `sec_experiments.tex:241-245`;
  - Appendix C: `sec_appendix_impl.tex:61-62`;
  - Conclusions: `sec_conclusions.tex:23-25`.
- **As printed:**
  - Section 5.4: "A complete sweep that still finds columns yields only the
    Lagrangian bound z_RMP + Σ|K| min(0, c̲_gt), with the artificials at zero
    and c̲_gt = η^K cK − μ_gt − ζ̄_gt formed from an upper bound ζ̄_gt ≥ ζ_gt
    on the pricing maximum, never from a load that was found; it needs no
    convergence, so a root stopped on its deadline still has it."
  - Section 5.4: "Over the volumetric loads the same sweep is a two-constraint
    knapsack oracle and closes in seconds, so the method asks geometric
    pricing only for columns and takes its bound from the volumetric
    relaxation."
  - Table 4: "the certified bound of Section 5.4, printed only where every
    pair of its sweep was proved, or the Lagrangian bound of that section
    where it is larger".
  - Section 8.5: "Against the method's own bound alone the median is 6.71% and
    the maximum 42.93%, on I_4_10_3_90, where the volumetric bound was not
    certified and the Lagrangian bound stands in."
  - Appendix C: "every certified bound came back with every pair proved."
  - Conclusions: Proposition 2 "makes a bound affordable where a geometric
    certificate rarely is".
- **Evidence:**
  1. **The volumetric certificate ended unproved on two rows, not one.**
     - In M, `vol_lb_proved` is false on two rows:

       | instance | `vol_lb` | `bound_source` | `global_lb` | own-bound `gap_pct` |
       | --- | --- | --- | --- | --- |
       | I_4_10_3_90 | 44456.20 | Lagrangian | 25391.03 | 42.9268 |
       | I_8_10_3_60 | 28845.31 | Lagrangian | 25741.35 | 19.3183 |

     - LOGS, `workstation/logs/method_I_4_10_3_90.log`: root column
       generation stops at 135.0 s, and "volumetric relaxation LB 44456.2
       proved=false" follows at 138.0 s, 3.0 s later.
     - LOGS, `server/logs/method_I_8_10_3_60.log`: the same lines at 405.1 s
       and 489.7 s, 84.6 s apart.
     - Section 8.5 names only the first.
  2. **Neither ran out of its clock.**
     - The volumetric call's deadline is now + 0.10·budget (`tree.jl:833`),
       which is 90 s and 270 s here.
     - `certified_lb` (`colgen.jl:446-510`) can return a finite, unproved
       value before its deadline in two ways:
       - the 400-iteration cap (`colgen.jl:451`);
       - a sweep in which some pair came back unproved (`:converged` with
         `proved` false, `colgen.jl:489`).
     - Its gate and `rc_tol` exits cannot fire on this call, which passes no
       gate and has `rc_tol` 0.
     - Neither the log nor the CSV records which exit it was: `CGResult.stop`
       of the volumetric call is not written. **The cause is unverified.**
     - On the other 34 rows the certificate took 0.5–23.9 s and proved
       (LOGS, from "root CG stopped" to "volumetric relaxation LB").
     - Table 11 lists neither the 10% deadline nor the iteration cap.
  3. **The text's condition for the Lagrangian bound is stricter than the
     code's, and on I_4_10_3_90 it was probably not met.**
     - `price_sweep` fills every pair's reduced-cost bound before pricing
       anything, from the closed-form knapsack relaxation
       (`colgen.jl:275-288`). Its comment says this "is what keeps the
       Lagrangian bound alive when the sweep runs out of clock".
     - It replaces that entry with the oracle's dual bound only where a pair
       was priced (`colgen.jl:360`).
     - The oracle's dual bound is ∞ unless the slot set is the true one
       (`pricing.jl:544`).
     - The bound is then formed whenever the artificials are at zero
       (`colgen.jl:474-475`). A complete sweep is not required.
     - On I_4_10_3_90 the root ran one iteration and stopped on its deadline
       ("root CG stopped on deadline after 1 iterations", LOGS line 8). Its
       window was 108 s; the 20 pairs could take up to 400 s at the 20 s
       per-call limit.
     - The number of pairs priced is not logged, so "incomplete" is probable,
       not proved.
     - The bound is valid either way. But the argument for it is not the one
       Section 5.4 gives, and it explains why the bound sits 43% below the
       plan: each unpriced pair contributes |K| times the knapsack
       relaxation's slack.
  4. **Table 4's "where it is larger" does not describe these two rows.**
     Their Lagrangian bound is printed because no certified bound exists.
- **Fix:**
  - Section 5.4, replace the Lagrangian sentence with: "Any sweep, complete or
    not, yields the Lagrangian bound z_RMP + Σ_{g,t}|K| min(0, c̲_gt) with the
    artificials at zero, where c̲_gt = η^K cK − μ_gt − ζ̄_gt and ζ̄_gt ≥ ζ_gt
    is the oracle's dual bound for a pair it priced and the knapsack
    relaxation of volume and payload for a pair it did not, never a load that
    was found. It needs neither convergence nor a complete sweep, so a root
    stopped on its deadline still has it, weakened by every pair left
    unpriced."
  - Section 5.4, last paragraph: "…is a two-constraint knapsack oracle, which
    closed within 25 s on 34 of the 36 benchmark instances; on the other two
    (Section 8.5) it ended unproved, and the method's bound is the Lagrangian
    one."
  - Table 4, LB: "… or the Lagrangian bound of that section where it is
    larger or where no sweep was proved".
  - Section 8.5: "… the maximum 42.93%, on I_4_10_3_90. There and on
    I_8_10_3_60 (19.32%) the volumetric certificate ended unproved, after 3.0
    and 84.6 s and inside its deadline, and the Lagrangian bound stands in."
  - Section 8.3: "takes 1.3 to 13.8 s on the ladder".
  - Appendix C: "every bound reported as certified came back with every pair
    proved; on two benchmark instances the volumetric certificate did not
    (Section 8.5)".
  - Table 11: add the row "volumetric certificate: 10% of the budget, at most
    400 iterations".
  - Conclusions: "… which makes a bound affordable on 34 of the 36 instances,
    where a geometric certificate rarely is".
  - Data, for the next run: write the volumetric call's exit (`vb.stop`) and
    the number of pairs priced to the CSV.

- **Resolution (FIXED).** Checked in the code (`colgen.jl` 82-96, 275-288, 360, 446-510; `pricing.jl` 544; `tree.jl` 833) and the logs (34 rows proved in 0.5-23.9 s; I_4_10_3_90 unproved after 3.0 s, I_8_10_3_60 after 84.6 s). Table numbers below are those of the revised PDF: Table 4 (quantities) is removed, so the old Tables 5-12 are now 4-11. Section 5.4 now states the Lagrangian rule as the code applies it (any sweep, artificials at zero, the knapsack relaxation for an unpriced pair, the oracle's dual bound where priced, weakened by every pair left unpriced) and says the volumetric sweep closed within 24 s on every ladder run and on 34 of 36 benchmark instances, ending unproved on I_4_10_3_90 and I_8_10_3_60 inside its deadline with no reason in the logs; no cause is claimed. Algorithm 1 gains the 400-sweep exit. Table 4 is gone (see b3); its LB definition moved to Section 8.1 as "the largest bound of Section 5.4 it obtained, a certified value where every pair of its sweep was proved or else the Lagrangian bound". Section 8.5 names both rows with their times and own-bound gaps (42.93%, 19.32%). The Section 8.3 timing sentence moved to Appendix C, restricted to the ladder. Appendix C: "every bound reported as certified came back with every pair proved; on two benchmark instances the volumetric certificate did not". Table 10 (was 11) has the row "volumetric certificate: 10% of the budget, at most 400 sweeps". Conclusions: "made a certified bound affordable on every ladder run and on 34 of the 36 benchmark instances". The data item (writing `vb.stop` and the pairs priced) belongs to the outer repository and is not changed here.

### B2. The server rows had 79–88% of the reference compute, which the paper calls a calibrated budget

- **Where:**
  - Section 8.5: `sec_experiments.tex:203-208`;
  - Appendix C: `sec_appendix_impl.tex:67-74`;
  - Table 11: `sec_appendix_impl.tex:93`, `:99` and `:109`;
  - Table 12 caption: `tab_bench36_full.tex:20-24`.
- **As printed:**
  - Section 8.5: "a budget of 900 s on the reference machine, scaled by
    calibration on the others (Appendix C); the compact volumetric model was
    run on every instance with a budget calibrated the same way".
  - Appendix C: "Its budgets are 900 s of the workstation, converted by a
    fixed-work calibration that times one Gurobi model at ten threads and one
    single-thread Julia code on each machine: 720 s for the method and 670 s
    for the compact model on the laptop, and 2 700 s for both on the server,
    a factor of three where the calibration measured 3.4 and 3.8. Only the
    budget and step 4's limit, a sixth of it, scale with the machine; the
    per-call limits of Table 11 do not."
- **Evidence:**
  1. **Which factor is which.**
     - The benchmark ran under the pinned protocol: five workers, each on its
       own 12-CPU block. CAL `calib_pinned.txt` gives:
       - Gurobi at 10 threads: 30.89–31.66 s against the workstation's
         9.1 s, i.e. ×3.39–3.48;
       - single-thread Julia: 17.02–19.34 s against 4.8 s, i.e. ×3.55–4.03.
     - Two other sources agree:
       - the table in `server_arms/README.md`: "pinned | 30.9-31.7 s (x3.4)
         | 17.0-19.3 s (x3.8)";
       - the header of `bench36_server.sh`: "x3.4 Gurobi, x3.8 1-thread
         Julia".
     - So Gurobi is ×3.4 and Julia is ×3.8.
     - The bench36 README says the reverse ("x3.4 Julia, x3.8 Gurobi"), and
       so does this round's brief. The README's "794 workstation-seconds for
       the method and 711 for the compact model" follows from the swap.
     - Correctly, the compact model, which is Gurobi only, had
       2700/3.4 ≈ 794 workstation-seconds. The method, which mixes both, had
       between 2700/3.8 ≈ 711 and 794.
     - The paper does not say which factor is which, so it is not wrong here.
       Anyone who writes the consequence from the README will be.
  2. **What the server got.**
     - 3/3.8 to 3/3.4 is 79–88% of the reference compute.
     - That applies to 17 of the 36 method rows and 19 of the 36 compact runs
       (`machine` in M and C).
     - The paper prints both factors and the 3, but not the direction or the
       size of the difference. "Scaled by calibration" and "calibrated the
       same way" are not true of the server.
  3. **The laptop, and the provenance of every factor.**
     - The laptop's factors (×0.74 Gurobi, ×0.87 Julia; 6.61–6.79 s against
       9.1 s) exist only as launcher comments: `bench36_worker.sh:12` and
       `compact36_laptop.sh:7-9`. The README says "the raw calibration output
       was not saved".
     - The method's 720 s is a factor of 0.8. `bench36_remote_worker.sh:10`
       says so: "720 s (x0.8; calibrated x0.74 Gurobi, x0.87 Julia)". That
       matches neither measured factor, and amounts to 828–973
       workstation-seconds.
     - The compact model's 670 s follows the Gurobi factor (≈ 905
       workstation-seconds).
     - The reference figures themselves (9.1 s, 4.8 s) appear only in prose
       in `server_arms/README.md`. A grep for "CALIB host" over `results/`
       finds only the server's outputs.
     - `calib_pinned.txt` says it was "Transcribed from the session's
       terminal output: the run wrote no file."
     - So every factor that converts a budget is recovered, not recorded.
  4. **What scales with the machine.**
     - "Only the budget and step 4's limit scale" leaves out that every share
       of the budget scales too:
       - root column generation, 15%;
       - the frontier reserve, 15%;
       - the volumetric certificate's deadline, 10% (`tree.jl:833`).
     - The per-call limits do not scale: pricing 20 s, placeability 20 s,
       step 2 300 s and 120 s, step 3 60 s.
     - So a server call does about 1/3.4–1/3.8 of the work of a workstation
       call. On the server, the same code runs with effectively shorter
       per-call limits.
  5. **Memory.**
     - The compact model's 16 GB cap on the server never bound:
       `peak_rss_gb` is at most 6.31 on the server rows of C.
     - The one `MEM_LIMIT` is a laptop run at 20 GB (I_8_10_4_60, 21.38 GB
       peak).
     - The 16 GB versus 20 GB difference does not matter, and the paper can
       say so.
  6. **Does it matter for a claim?**
     - No printed number is wrong and no claim is reversed.
     - Every gap is taken against a bound that is valid however much compute
       produced it. Less compute can only weaken a server plan or a server
       compact bound, so server gaps are, if anything, overstated. For
       example, the best bound on I_4_10_3_120 (gap 17.51%) is a compact
       time stop on the server.
     - Three readings are affected:
       - (i) "No plan within the budget" on I_8_10_4_60, a server row, holds
         at 711–794 workstation-seconds, not at 900.
       - (ii) Table 8's factor medians, and Section 8.5's "the gaps are
         largest at v = 60 … and at |T| = 10", mix machines unevenly:

         | factor level | method rows | workstation | laptop | server |
         | --- | --- | --- | --- | --- |
         | v = 60 | 12 | 4 | 1 | 7 |
         | v = 120 | 12 | 1 | 6 | 5 |
         | \|T\| = 10 | 18 | 4 | 4 | 10 |

         The median gap is 8.37% over the 16 server plans, 5.55% over the 6
         workstation plans and 3.31% over the 10 laptop plans. That cannot be
         separated from which instances each machine drew.
       - (iii) Appendix C's "a difference of one or two trucks between rows
         is not read as an effect" is right, and should cover differences
         between machines too.
- **Fix:**
  - Section 8.5: "… a budget of 900 s on the reference workstation, and on
    the others 720 s (laptop) and 2 700 s (server), which by the calibration
    of Appendix C gives the server rows 79–88% of the reference compute; the
    compact model had 900, 670 and 2 700 s".
  - Appendix C, after "3.4 and 3.8": "(Gurobi and single-thread Julia
    respectively), so a server row had 79% to 88% of the workstation's
    compute. The laptop's method rows ran at a factor of 0.8, between its
    measured 0.74 and 0.87, which is 92% to 108%. Every share of the budget
    scales with it: root column generation, the frontier reserve and the
    volumetric certificate. The per-call limits of Table 11 do not, so a
    server call does about a third of the work of a workstation call. Less
    compute can only weaken a row, so the server's gaps are not understated.
    The compact model's 16 GB cap on the server was never reached (peak
    6.3 GB)."
  - Re-run `calib.jl` on the workstation and the laptop (about 20 s each) and
    archive the output next to the pinned server figures. Otherwise, say in
    Appendix C that these figures were recorded only in launcher comments and
    a README.
  - Correct the bench36 README: Gurobi is ×3.4 and Julia ×3.8, and its
    794/711 sentence follows.

- **Resolution (FIXED).** Factors stated by direction from `calib_pinned.txt` (Gurobi x3.4, Julia x3.8, against the workstation's 9.1 s and 4.8 s): the server's 2700 s gave the compact model about 794 and the method 711-794 workstation-seconds, 79-88% of the reference (Section 8.5, Appendix C, and the caption of the full benchmark table, now Table 11, was 12). The laptop now has recorded calibration logs (`bench36/calib/calib_{workstation,laptop}.log`, final-3d 13d55a2: x0.71-0.75 Gurobi, x0.84-0.91 Julia; the README rounds the latter to 0.90); Appendix C says the method's 720 s is a factor of 0.8 between them and the compact model had 670 s. Appendix C now says every share of the budget scales (root CG, frontier reserve, the volumetric certificate's tenth, step 4's sixth) and the per-call limits do not, that the 16 GB server cap was never reached (peak 6.3 GB), and that differences between machines, like those between rows, are not read as an effect. Section 8.5 reads I_8_10_4_60's "no plan" as "within 711 to 794 workstation-seconds". The README correction is in the outer repository (7727255, 72cd32f).

### B3. "The error runs one way" is true by definition and by Proposition 2, not a finding about the catalogue

- **Where:**
  - the abstract's last clause, `sec_abstract.tex:16`;
  - the conclusions, `sec_conclusions.tex:33`;
  - Section 8.6, equation (90) and the paragraph after it,
    `sec_experiments.tex:349-373`.
- **As printed:**
  - Abstract: "… so a volumetric capacity is not a conservative
    simplification; on the industrial catalogue the error runs one way."
  - Conclusions: "on the industrial catalogue the error runs one way only."
  - Section 8.6: "κ has median 1.000, ranges to 2.000 and exceeds 1.20 on four
    types, and no type has κ < 1: the error cannot run in the planner's
    favour, and a plan over a mixed catalogue fails on its worst container."
- **Evidence:**
  1. **κ ≥ 1 holds for every container type of every catalogue.**
     - The s_r footprints of one orientation fit on the floor only if
       s_r a_r ≤ pK·qK.
     - L^h_r = ⌊rK/r_pr⌋ ≤ rK/r_pr.
     - So s_r min(L^h_r, L^c_r) ≤ s_r L^h_r ≤ Γ_K/γ_pr.
     - Both sides of (90) carry the same payload term, so the denominator
       never exceeds the numerator.
     - "No type has κ < 1" therefore cannot fail. `results/catalogue_realism.json`
       agrees (`theta_min` 1.0, `n_under` 0), and it could show nothing else.
  2. **That a volumetric capacity never under-credits is Proposition 2.** The
     inclusion Ω^g ⊆ Ω^g_vol holds for any catalogue, given bounding-box
     volumes.
  3. **What the catalogue does show is magnitude, and it is mixed.**
     - Nine of the fifteen types have κ = 1.000 (`n_benign` 9).
       `n_payload_bound_denominator` is 8: payload binds both counts on eight
       types.
     - Two types are at 1.176.
     - Four are between 1.231 and 2.000 (`n_severe` 4).
     - The abstract's clause hides that the diagnostic finds no over-credit
       on most real container types.
  4. **"A plan over a mixed catalogue fails on its worst container" is
     computed nowhere.** The census is over generated instances.
  5. **One part is checked and true.** For this catalogue the denominator is
     an upper estimate of what fits. For each of its five footprints, the
     single-orientation grid count is the most that fit on a 13600×2400 floor:

     | footprint (mm) | grid count |
     | --- | --- |
     | 800×600 | 68 |
     | 1200×800 | 34 |
     | 1200×1000 | 26 |
     | 1600×1200 | 16 |
     | 2400×1200 | 11 |

     For 1200×1000 and 1600×1200 I argued this by cross-section width. So
     κ > 1 is a conservative diagnosis here, as printed.
- **Fix:**
  - Abstract: "… so a volumetric capacity is not a conservative
    simplification: it can only over-credit, and on the industrial catalogue
    it does so for six of fifteen container types, four of them by more than
    20%." Alternatively, drop the clause, which saves eight words (b5).
  - Conclusions: the same wording.
  - Section 8.6: "No type can have κ < 1, the volumetric credit bounding the
    grid count by construction, and Proposition 2 shows that the error never
    runs in the planner's favour. What the catalogue measures is its size:
    κ = 1 on nine types, 1.176 on two and 1.231–2.000 on four."
  - Delete "a plan over a mixed catalogue fails on its worst container", or
    support it with a computation.

- **Resolution (FIXED).** kappa >= 1 by construction checked (s_r L^h_r <= Gamma_K/gamma_pr). Abstract: "... is not a conservative simplification: it over-credits six of fifteen industrial container types." Conclusions: "On the industrial catalogue a volumetric capacity over-credits six of the fifteen container types, four of them by more than 20%." Section 8.6: "No type can have kappa < 1, the volumetric credit bounding the grid count by construction, and Proposition 2 already shows that the error never runs in the planner's favour; what the catalogue measures is its size. Over its fifteen types kappa is 1.000 on nine, 1.176 on two and 1.231 to 2.000 on four." (`catalogue_realism.json` theta: 9 / 2 / 3 / 1.) "A plan over a mixed catalogue fails on its worst container" is deleted.

---

## Minor

### b1. Proposition 2 is stated for an LP value; the paper uses it for the compact model's integer bound and for the two optima

- **Where:**
  - Proposition 2: `sec_master.tex:207-232`;
  - Section 8.5: `sec_experiments.tex:235-242` ("The compact model's bound is
    valid by Proposition 2"; "attain the compact model's proved volumetric
    optimum, which proves them optimal by inclusion");
  - conclusions: `sec_conclusions.tex:23-24` and `:36-38`;
  - the Table 6 caption.
- **As printed:** "z_vol the certified LP value of the master (54)–(57) over
  them. Then z_vol ≤ z*".
- **Evidence:**
  - The compact bound used for 25 of the 32 plans' gaps is the dual bound of
    SCPTOP, an integer programme. J `best_lb_from` is `compact` on 28 rows,
    25 of which have a plan. That bound can exceed z_vol.
  - The two optima rest on z*_SCPTOP ≤ z*_3D-SCPTOP. In C and the JSONs, plan
    cost = compact `OPTIMAL` obj = bound on both (21502.06 and 18796.96), with
    `mipgap` 0.
  - The proof's last clause contains the argument: "the 3D master is the
    volumetric master with columns removed … its optimum at least its LP
    value". The statement does not.
  - Neither does the statement say that SCPTOP is the compact form of the
    volumetric master. That needs Proposition 1's argument with Ω^g_vol in
    place of Ω^g.
  - Related, in Section 5.4: "z_RMP = z_MP ≤ z*". The RMP carries the cover
    cuts (59), which are valid for integer plans but are not rows of MP. The
    certified value is therefore the LP value of MP with (59). It is still at
    most z*, but it is not z_MP as defined.
- **Fix:**
  - Add to Proposition 2: "… and so is z*_vol, the optimum of the volumetric
    problem. By the argument of Proposition 1, z*_vol is the optimum of
    SCPTOP (Appendix A), so any dual bound of SCPTOP is a lower bound on
    z*_3D-SCPTOP, and a 3D plan that attains z*_vol is optimal."
  - In Section 5.4, define z_MP as the LP value of MP with (59).

- **Resolution (FIXED).** Proposition 2 now also states z*_vol, which by the argument of Proposition 1 is the optimum of SCPTOP, so any SCPTOP dual bound bounds z*_3D-SCPTOP and a 3D plan attaining z*_vol is optimal; the proof adds the step and notes that SCPTOP's truck rows (A-vol)-(A-ftl) admit exactly Omega_vol. Section 5.4 says z_RMP and z_MP both carry the cover cuts (59), which are valid for every integer plan.

### b2. The four instances with no plan are missing from the limits, and one is the ladder's rung 2 with another seed

- **Where:** "Three limits are real" (`sec_conclusions.tex:35-47`); Section
  8.5 (`sec_experiments.tex:209-216`).
- **Evidence:**
  - M `has_plan` is false on I_8_5_3_60, I_8_10_2_60, I_8_10_2_90 and
    I_8_10_4_60.
  - These are exactly the four rows whose root RMP still carried artificial
    slack when root column generation reached its deadline. LOGS show "root
    LB" values of 1.29e8, 2.61e8, 1.91e7 and 2.82e7 (penalty included) and
    "best rc Inf". On every other row the root LP value is between 13 967 and
    66 993.
  - That is a sharper statement of the mechanism than "stopped on its
    deadline after one to three iterations", which is true of 31 of the 36
    rows.
  - The conclusions' first paragraph mentions the four; the limits paragraph
    does not.
  - The benchmark's I_8_10_2_90 (seed 1028) and the ladder's
    I_8_10_2_90_k25 (seed 7000) share every generator parameter: `meta` v 90,
    `trucks_per_period` 2.5, `demand_spread` 0.1; |N| 8, |T| 10, |R| 2, K 12.
  - Table 6 gives the ladder instance 26 verified trucks at a 0.163% gap.
    Table 12 gives the benchmark instance no plan.
  - The difference is the instance:
    - in the benchmark twin, R2 is 1200×1600×600 mm, so height allows 5
      layers, but every R2 product (403.8–472.5 kg) is crush-limited to 2;
    - three of the four R1 products are crush-limited to 2 of 3 layers;
    - on the ladder twin, crush never binds below the height count.
  - A reader comparing Tables 6 and 12 needs that sentence.
- **Fix:**
  - Add a fourth limit: "On four benchmark instances with eight products the
    method returns no plan within its budget. Its root master still needs
    artificial slack when root column generation reaches its deadline, so the
    pool cannot cover the demand." Then name the next step; the choice of step
    is the author's.
  - In Section 8.5, add: "I_8_10_2_90 shares every generator parameter with
    the ladder's I_8_10_2_90_k25 and differs only in seed. Its second
    container type is crush-limited to two of the five layers its height
    allows."

- **Resolution (FIXED).** Checked: on exactly the four no-plan rows the root LB carries artificial slack (1.29e8, 2.61e8, 1.91e7, 2.82e7 with BIGM 1e6, against 13 967-66 993 elsewhere). "best rc Inf" also appears on I_4_10_4_60 and I_8_5_3_120, which have plans, so the text uses the slack, not that marker. Section 8.5 says so and adds the twin: I_8_10_2_90 (seed 1028) and the ladder's I_8_10_2_90_k25 (seed 7000) share every generator parameter; R2 is 1200x1600x600 (five layers by height) with every R2 product crush-limited to two, while on the ladder instance no crush count falls below its height count. Conclusions: "Four limits are real"; the new one says the method returns no plan on four eight-product instances because the root master still needs artificial slack at the root deadline. Its next step is left in purple (`\Pending`) for the author.

### b3. Table 4's definition of "stop" is false for Table 5

- **Where:** `tab_quantities.tex:19-20`; `tab_volumetric.tex:48-52`.
- **As printed:** "after a time or memory stop the row's time is the budget."
- **Evidence:**
  - Table 5's memory stops print 3269, 1122, 2093 and 2403 s, against a
    3 600 s budget.
  - Its time-stopped method rows print 3062–3133 s (`parity_vol.csv`
    `seconds`). They stop short of 3 600 s by the frontier reserve.
- **Fix:** "after a memory stop the row's time is when the cap was reached;
  the method's wall clock stops short of its budget by the frontier reserve
  (Table 11)".

- **Resolution (FIXED).** Table 4 is removed (one of the length cuts). z, LB and gap are defined in three sentences in Section 8.1; nodes, open and dropped in the caption of the ladder table (now Table 5, was 6). The false "stop" definition no longer exists; the volumetric table (now Table 4, was 5) explains its own marks.

### b4. "threads 10" does not hold for column generation

- **Where:** Table 11 (`sec_appendix_impl.tex:105`); Section 8.2 ("at the
  same 3 600 s, 20 GB and ten threads"); Appendix C, which calibrates
  Gurobi at ten threads.
- **Evidence:** from the code at the run's tree.
  - `solve_fleet_tree`'s `price_workers` defaults to `nothing`
    (`tree.jl:599`), and `run_benchmark.jl` does not pass it.
  - `price_sweep` passes `threads=price_workers` to both oracles.
  - `certified_lb` solves the master LP with `solve!(ms; time_limit=…)` and no
    thread count (`colgen.jl:465` and `:505`).
  - `solve!` and `new_model` set a thread count only when one is given
    (`master.jl:390`, `solver.jl:47`).
  - So the column-generation master LPs and pricing models run at the solver
    default:
    - all logical cores on the workstation (12, Ryzen 5 5600X);
    - all logical cores on the laptop (16, Ryzen 7 260);
    - 12 on the server, from the `gurobi.env` its log reports reading.
  - Steps 2–4, placeability and the initial pool do get 10:
    `construct.jl:244` and `:606` at e4830fe, `tree.jl:667` and
    `:703-715`.
  - The actual thread counts are not recorded. This is the Julia counterpart
    of the Python defect recorded in CLAUDE.md
    (`gp.setParam("Threads")`).
- **Fix:** Table 11, "threads: 10; column generation (master LPs and pricing)
  at the solver default, 12 on the workstation and the server, 16 on the
  laptop". Or pass `price_workers` and the master's thread count in the next
  run.

- **Resolution (FIXED).** Checked: `run_benchmark.jl` passes no `price_workers` (`tree.jl:599`); the master is built without a thread count (`master.jl:106`) and solved without one in `certified_lb` (`colgen.jl:465`, `:505`); the same holds at 10e633c for the volumetric runs. Steps 2-4, `pack_witness` and the seed pool get 10 threads. Appendix C and the settings table (now Table 10, was 11): "10; column generation's master LPs and pricing at the solver default (12 on the server)". The server's 12 is read from its logs (`gurobi.env`). The workstation and laptop counts are not recorded, so "solver default" is printed rather than 12 and 16. Section 8.2 and the volumetric table's caption now say "ten threads, the method's column generation apart".

### b5. The abstract is back at the 250-word limit

- **Where:** `sec_abstract.tex:16`.
- **Evidence:**
  - Method: lines 6–22 of `pdftotext -layout` of the PDF, line-end hyphens
    joined, split on whitespace, keywords excluded. "Dantzig–Wolfe",
    "relax-and-fix", "3D-SCPTOP" and each percentage count as one word.
  - Result: **250 words** with "3 600" as two tokens, 249 as one.
  - Round 2 trimmed the abstract to 247 words (a13). The new benchmark
    sentence used up that margin.
  - Elsevier's form counts the pasted text, in which the thin space of
    "3 600" may survive as a space.
- **Fix:** trim at least ten words, for example:
  - drop "in manufacturing supply chains" (−4);
  - "of an assembly plant and its supplier" → "of a plant and its supplier"
    (−2);
  - "This paper proposes" → "We propose" (−1);
  - B3's rewrite or deletion of the last clause (up to −8).

- **Resolution (FIXED).** 240 words from the PDF with "3 600" as two tokens (239 as one), counted as in this finding: text from "We propose" to the last full stop, line-end hyphens joined, split on whitespace. Trims: "This paper proposes" -> "We propose"; "in manufacturing supply chains" dropped; "an assembly plant" -> "a plant"; "Because the placement subproblem is NP-hard," dropped; B3's clause rewritten. F1's clarification ("gaps to its certified bound") is included in the count.

### b6. Figure 1 still promises Small, Medium and Large Instances

- **Where:** Figure 1, `sec_litreview.tex:359-362`.
- **Evidence:**
  - Section 8 has a six-instance volumetric check, a five-rung size ladder
    and the 36-instance benchmark. None of them is split into small, medium
    and large.
  - Round 2 left this item (D5) "for the 36-instance re-run". That re-run has
    now happened.
- **Fix:** relabel the three boxes "Volumetric check (8.2)", "Size ladder
  (8.3–8.4)" and "36-instance benchmark (8.5)".

- **Resolution (FIXED).** Figure 1 is the initial manuscript's TikZ diagram (`sec_litreview.tex`, restored in d0a434f). Its three side boxes are relabelled "Volumetric Check", "Size Ladder" and "36-Instance Benchmark"; nothing else in the figure changed.

### b7. An undefined term, and two sentences about components the runs do not use

- **Where:**
  - `Alg_tree.tex:51`: "no restart occurred";
  - `sec_branching.tex:64-66`: "A third level, on deliveries, is disabled in
    every run reported";
  - `sec_pricing.tex:57-60`: "Four implied rows are available … and the
    oracles carry the second pair".
- **Evidence:**
  - "Restart" appears nowhere else in the paper. M has `restarts` 0 on all 36
    rows.
  - The other two sentences describe things the reported method does not do.
    That breaks the author's rule of describing only the final method.
- **Fix:**
  - Delete "no restart occurred", or define a restart where Algorithm 2 is
    introduced.
  - Replace the third-level sentence with "a region whose fleet vector is
    fully fixed is covered by its certificate and not split".
  - Introduce only the rows the oracles use, as "the oracles carry two
    implied rows that bound the occupied floor area, (72)". Keep (71) only
    as the cross-reference Table 10 needs.

- **Resolution (FIXED).** "no restart occurred" is deleted from Algorithm 2 (`restarts` is 0 on all rows). The third-level sentence now reads "A region whose fleet vector is fully fixed is covered by its certificate and not split." Pricing now reads "The oracles carry two implied rows bounding the occupied floor area, (72); (71) restates the floor on the layer capacity rather than on the load, as the compact model's (35) does"; (71) is kept for the row map's cross-reference (now Table 9, was 10).

### b8. "Its optimum is an extreme point"

- **Where:** `sec_pricing.tex:126-129`.
- **As printed:** "The pricing objective is linear in the image, so its
  optimum is an extreme point of conv(Ω^g). A balanced load inside the hull
  is … and no dual vector returns it."
- **Evidence:** under ties a load that is not an extreme point can be
  optimal, for example when a commodity has a zero price. An exact oracle may
  then return it. What holds is the weaker statement below.
- **Fix:** "an optimum can always be taken at an extreme point of
  conv(Ω^g), and a load in its relative interior is never the unique
  optimum, so no dual vector asks for it".

- **Resolution (FIXED).** As proposed: "an optimum can always be taken at an extreme point of conv(Omega^g), and a load in its relative interior is never the unique optimum ... no dual vector asks for it". Section 7.2's "Pricing returns only extreme loads" also became "Pricing returns extreme loads".

### b9. The node medians are taken over the plans only

- **Where:** `sec_experiments.tex:263-264`.
- **As printed:** "At |T| = 10 the search opens a median of three nodes
  within the budget, against 38 at |T| = 5."
- **Evidence:**
  - From M `nodes`: the median is 3 over the 15 plans at |T| = 10 and 38 over
    the 17 plans at |T| = 5.
  - Counting the no-plan rows (0 nodes), the medians are 2.5 and 33 over 18
    rows each.
- **Fix:** "… a median of three nodes over its 15 plans, against 38 over 17 at
  |T| = 5".

- **Resolution (FIXED).** Now "a median of three nodes over its 15 plans, against 38 over the 17 at |T| = 5" (M `nodes`).

---

## Format for AMM

- **Abstract:** 250 words, or 249 with "3 600" as one word, counted as in b5.
  It is compliant but has no margin (b5).
- **Highlights:** 5 bullets, each within the 85-character limit including
  spaces. I counted with `wc -m` per line of `highlights.txt`:

  | bullet | characters |
  | --- | --- |
  | 1 | 78 |
  | 2 | 75 |
  | 3 | 76 |
  | 4 | 83 |
  | 5 | 75 |

- **Keywords:** 5, within 1–7: Procurement; inventory; transport planning;
  full-truckload; 3D container loading. See F9.
- **Length:** 33 pages, about 3.6 of them references. The introduction, the
  literature review and the formulation stay as they are. These cuts lose no
  claim:

| cut | page | saves | why nothing is lost |
| --- | --- | --- | --- |
| Table 10 (Appendix B, the row map) → one sentence in Section 5 citing Figure 2 and (47); repoint the conclusions' "(Table 10)" to Section 5.2 | 28 | ~0.7 | Figure 2 and Section 5 already state the correspondence |
| Table 11 → supplementary material, keeping Appendix C's paragraph | 29, two-thirds empty | ~0.7 | settings stay available |
| Table 12 → supplementary data, keeping Table 8 and the instances the text names | 30 | ~0.9 | a referee can still read every row; optional |
| Section 7.2's enumerated steps 1–4 → two sentences pointing at Algorithm 3, which states every step; keep (82)–(86) | 18–20 | ~0.35 | duplicated |
| Section 5.4's prose rules that Algorithm 1 repeats (`sec_master.tex:179-186`) → keep the algorithm, the definition of the certificate and the Lagrangian | 15 | ~0.2 | duplicated |
| Table 4 → three sentences in Section 8.1 (z, LB, gap), with nodes, open and dropped defined where Table 6 uses them | 22 | ~0.35 | definitions kept; b3 goes away |
| Table 7's five method columns (z, trucks, LB, gap and budget, which repeat Table 6) | 24 | ~0.15 | duplicated |
| Section 8.3's last paragraph (timings, placeability counts) and Section 8.4's second paragraph (three bounded regions) → one sentence each in Appendix C | 24 | ~0.3 | detail kept in the appendix |
| b7's two sentences | — | ~0.1 | components not used |

That totals about 3.7 pages, or about 2.8 if Table 12 stays. It brings the
paper to 29–30 pages. Figure 3 (about 0.4 page) is the next candidate if more
is needed.

- **Resolution (PARTLY APPLIED).** The duplication cuts are applied: Section 7.2's steps (now a numbered paragraph pointing at Algorithm 3, with (82)-(86) kept); Section 5.4's prose that repeats Algorithm 1; Table 4 (replaced by three sentences in Section 8.1); the compact 3D table's five method columns (now Table 6, was 7); Section 8.3's last paragraph and Section 8.4's second paragraph (one sentence each in Appendix C); and b7's two sentences. Moving Tables 10, 11 or 12 (old numbering) is the author's decision and was not done. Result: **33 pages**. The body is the same length, because the additions for B1, B2, b1, b2, F2 and the deflated-capacity census offset the cuts, and Appendix C grew by about a page.

---

## For the author

These are decisions, not errors.

- **F1. Two gap conventions in one abstract.**
  - The ladder's gaps (0.0075–0.163%, 14.5%, 10.7%) are taken against the
    method's own bound. The benchmark's 6.46% is taken against the better of
    two bounds.
  - Against the compact bound 38 851.93, Table 6's row 2 would read 0.0065%,
    not 0.163%.
  - 25 of the 32 benchmark gaps are taken against the compact model's bound
    (J `best_lb_from`).
  - Either use one convention throughout, or name both in the abstract. The
    method's own median on the benchmark is 6.71%.
  - **Resolution (NOT CHANGED).** This is the author's decision. The abstract is now unambiguous within 250 words: the ladder's gaps are "gaps to its certified bound", the benchmark's "to the best valid bound".
- **F2. An unused proof-level argument for contribution (iv).**
  - On five instances a 3D lower bound exceeds the volumetric optimum (or,
    on I_4_10_2_60, the compact incumbent). There, no loadable plan reaches
    the volumetric cost.
  - The excess, relative to the volumetric value (M `global_lb` against C
    `obj`):

    | instance | at least |
    | --- | --- |
    | I_4_5_3_60 | 37.46% |
    | I_4_10_2_60 | 21.75% |
    | I_4_5_2_90 | 8.76% |
    | I_4_10_2_120 | 2.47% |
    | I_4_5_3_120 | 0.12% |

  - Unlike the census, this does not depend on how the solver split a period
    into trucks.
  - **Resolution (FIXED).** One sentence added to Section 8.6. On five benchmark instances a 3D lower bound exceeds the volumetric optimum (on I_4_10_2_60, the compact incumbent) by 0.12% to 37.46%, so no loadable plan reaches the volumetric cost there, whatever the truck split. Re-derived from M `global_lb` and C `obj`.
- **F3. The baselines the next referee will name.**
  - **The deflated-capacity volumetric model.** Section 8.6 says its 48 loads
    "have not been tested". The plan is on disk
    (`results/compact_ref/compact_ab_I_16_15_2_90_k25.json`), and
    `repack_volumetric.jl --check-only` runs the census in seconds.
  - **A re-split of the volumetric plans.** The census tests each plan's own
    split into trucks. Running Table 9's exact per-period re-pack over all 36
    would say how many plans no re-split can repair.
  - **A 3D comparator on the benchmark.** None exists yet. The compact 3D
    model produced a plan at |T| = 5 on the ladder, so running it on the 18
    benchmark instances with |T| = 5 would give a head-to-head where one is
    possible.
  - **Resolution (FIXED for the deflated-capacity baseline; the other runs NOT CHANGED (the author's decision)).** `census_compact_ab.json` (final-3d 72cd32f): on I_16_15_2_90_k25, 39 of 48 loads are proved unplaceable (39 of 45 distinct), none undecided; on I_12_12_2_90_k25, 3 of 34. Section 8.6 now says this in place of "has not been tested", and the conclusions say most such loads still cannot be placed.
- **F4. Replication.** Each cell of the factorial has one instance, and each
  instance one run. The twin in b2 shows that outcomes can differ by seed
  with identical parameters. Add a second seed per cell, or say that the
  factor medians are over single instances.
  - **Resolution (NOT CHANGED).** This is the author's decision.
- **F5. Data documentation (not printed).**
  - The README's swapped calibration factors (B2).
  - The missing raw `calib.jl` outputs for the workstation and the laptop.
  - The volumetric certificate's exit and the number of pairs priced (B1).
  - The `merge_bench36.py` docstring cites `julia/scripts/calib.jl`, which
    does not exist; the script is at
    `results/julia/server_arms/launchers/calib.jl`.
  - **Resolution (NOT CHANGED here).** This is data documentation in the outer repository. The README's factors were corrected there (7727255), and calibration logs were recorded (13d55a2). The `merge_bench36.py` docstring and the logging of `vb.stop` are outside this worktree.
- **F6. "Executable by construction"** (abstract; Section 5.1).
  - The plans are executable under the model's loading rules:
    homogeneous-layer stacks, crush and payload.
  - There is no axle-load, weight-distribution or load-securing row; Table 2
    now says so.
  - The evidence supports "loadable by construction".
  - **Resolution (NOT CHANGED).** This is the author's decision.
- **F7. The name "branch and price".**
  - Under 3D loading, geometric pricing runs at the root and when a
    construction fails (Algorithm 2, line 21). Node bounds come from the
    volumetric relaxation.
  - The tree stays: it is part of the method. But a referee may ask for this
    to be said where the method is named, in Section 7 or the abstract.
  - **Resolution (NOT CHANGED).** This is the author's decision.
- **F8. Front matter (carried over from round 2, D1).**
  - Still missing: authors, competing interests, CRediT, funding, data
    availability (the two purple "[repository URL]"s, in Section 8.1 and
    Appendix C), the generative-AI declaration, and appendix numbering
    (A.1).
  - `Renderer-diff.pdf` in the snapshot is stale; per the change log it has
    been since a4cb2ca. Do not send it.
  - **Resolution (NOT CHANGED).** This is the author's decision. `Renderer-diff.pdf` is still stale and was not regenerated.
- **F9. Keywords (D2).** There are five of a possible seven, and none names
  the method or the returnable transport items.
  - **Resolution (NOT CHANGED).** This is the author's decision.
- **F10. Say what the verifier checks.** Section 8.1 says plans are "checked
  truck by truck". The verifier (`verify_plan`,
  `julia/src/verify.jl:40-80`) also replays inventory balances, coverage stock
  and cost. That is the plan-level check Table 9's argument relies on, and
  saying so strengthens "verified plan".
  - **Resolution (FIXED).** Checked in `verify.jl` (`verify_plan`). Section 8.1 now says the same routine replays the plan's inventory balances, coverage stock and cost.
- **F11. Table 1 (carried over from round 2, A2).** It still marks 3D-SCPTOP
  with "Inventory Capacity Restriction X", and Section 4 has no such row.
  - **Resolution (NOT CHANGED).** This is the author's decision.
- **F12. Language.** Sections 5–8 run long sentences chained by dashes and
  semicolons, which a referee from the applied side will find hard. One
  copy-edit pass after the cuts.
  - **Resolution (NOT CHANGED).** This is the author's decision.

---

## Checked and correct

### Table 8

All 11 rows × 6 columns, recomputed by factor from M (`has_plan`), J
(`gap_pct`) and CEN (`dispatched_verdicts`): instances, plans, median, max,
count within 1%, unplaceable/dispatched. All match.

### Table 12

- All 36 rows × 12 cells and the bold marks: 0 mismatches. The cells were
  checked against:
  - M: `machine`, `trucks`, `plan_cost`, `global_lb`, `bound_source`;
  - J: `gap_pct`, `best_lb_from`;
  - C: `machine`, `status`, `trucks`, `obj`, `bound`;
  - CEN: unplaceable/dispatched.
- `best_lb` = max(`global_lb`, compact bound, proved `vol_lb`) on all 36
  rows.
- `gap_pct` = 100(z − `best_lb`)/z on all 32 plans.

### Section 8.5

- **Plans and the four without.**
  - 32 plans.
  - For the four without a plan (M `has_plan`, `proof_reason`; LOGS):
    - root column generation ran 2, 2, 1 and 3 iterations;
    - step 2 reported "the pool cannot cover the demand" on one attempt of
      each;
    - the last attempt ran out of time in step 3, except on I_8_10_2_90,
      where step 2 refused again;
    - `vol_lb_proved` is true on all four.
  - "No plan", never "infeasible", wherever these four are mentioned.
- **Bounds.**
  - Bound sources: 29 volumetric, 6 Lagrangian, 1 geometric.
  - Five 3D bounds lie above the volumetric optimum: four against `OPTIMAL`
    compact runs, one against the `TIME_LIMIT` incumbent 31 895.21.
  - Compact stops: 28 optimal, 7 time, 1 memory.
- **Gaps.**
  - Against the best bound: median 6.4642, mean 7.9377, max 29.4949; 9
    within 1%, 7 within 0.1%.
  - Against the method's own bound: median 6.7067, max 42.9268.
  - Medians by factor: 11.77 at v = 60; 9.12 and 2.19 at |T| = 10 and 5.
  - 37.46% and 21.75% above the volumetric optimum.
  - The other seven v = 60 plans: 6.3871–29.4949.
- **Trucks.** Equal to the compact plan on 10 instances, +1 to +12 on 22,
  never fewer.

### The two optima by inclusion

- I_4_5_3_90: plan 21502.06 = compact obj 21502.06 = bound
  21502.059999999998 (`OPTIMAL`, `mipgap` 0, server JSON).
- I_8_5_2_90: 18796.96 = 18796.96 (workstation JSON).
- Both plans have `complaints` 0.
- I_4_5_3_90's compact plan is the one plan with 0 of 14 loads unplaceable,
  which is consistent.

### Section 8.6 and Table 9

- **Table 9 (R40X):**
  - unplaceable 41 of 45 and 119 of 137;
  - placed 4 and 18, undecided 0;
  - distinct compositions 39 of 43 and 111 of 129;
  - uncovered periods 1 and 5 (`pairs_uncovered`; `per_pair` trucks null,
    tried 1–13), all supplier-bound.
- **The six unsplit loads.** Each is one truck at 97.3–99.9% of volume and
  51.8–68.7% of payload, recomputed from `instances_scale/*.json` and
  `results/compact_ref/*.json`.
- **Caps (LAD log lines 770 and 909).**
  - I_16_15: 208 < 226 (R1).
  - I_24_20: 208 < 226 twice, and 306 < 312.
- **The method's plans (LAD):** 55 trucks at 79 703.31 and 156 at
  223 944.20.
- **Census (CEN):**
  - 527 of 723 loads unplaceable, 196 placed, 0 undecided, 0 witnesses
    rejected;
  - 512 of 706 distinct compositions unplaceable;
  - zero unplaceable loads only on I_4_5_3_90;
  - 347 of 505 over the 28 optimal plans;
  - by v: 171 of 182, 140 of 238, 216 of 303.
  - The census's volumetric objective, trucks and status match C on all 36.

### Abstract, highlights, contribution (iii) and conclusions

- 0.0001% (5.9e-5 %, rounded up).
- 12 to 156 trucks, 900 to 3 600 s.
- Gaps 0.0075, 0.1628, 0.022, 14.5186 and 10.686 (LAD).
- 32 of 36 plans at 6.46%.
- Best bound by Proposition 2 on 31 of 36: 28 compact plus 3 method
  volumetric.
- 527 of 723 unplaceable; six unsplit periods.

### Appendix C and Table 11

- Method budgets 900, 720 and 2700 s; compact 900, 670 and 2700 s (M and C
  `time_limit`).
- Step 4 at 150, 120 and 450 s (`ip_tl` = budget/6).
- Method: 10 threads and 20 GB on all rows.
- Compact: `mipgap` 0, seed 0, 10 threads; 16 GB on the server, 20 GB
  elsewhere.
- Four method rows end 0.03, 0.01, 0.11 and 1.47 s past their budget.
- Compact wall clock up to 29.6 s past its limit (I_8_10_2_120).

### Instances paragraph

- In all 36 JSONs:
  - |R| and Γ/v as stated;
  - two footprints for |R| ≤ 3, three for |R| = 4;
  - products assigned to types in turn;
  - 2.5 truckloads per period;
  - K = 9 or 12, W^max 1200, α 0.55, cK 1000, lt 1, SC 1;
  - heights in multiples of 50 mm with at least two layers.
- `instances/I_8_5_2_90.json` is the ladder's rung-1 file.

### Round-2 resolutions that still hold

- A1: Algorithm 1 returns (0, false) and (∞, proved).
- A2: vertical stability by construction; Table 2's Load Balance cell is
  blank; Table 1's RTIs cell reads M|F.
- A3: all five sites cite the AMM 2014 paper.
- a1: dropped regions are in Section 7 and in Algorithm 2.
- a2: the Lagrangian's validity condition is present. It is now too strict
  (B1).
- a3: the certification policy is in Section 7 and Table 11.
- a4, a5, a7, a8, a10 and a11.
- T1–T5: no re-packed row or saving survives; "by placeability proof"; the
  +12 is in Table 11.
- **Regressed:** a13 (b5).
- **Now due:** a6/D5 (b6).

### Mathematics

- **Proposition 1**, both directions: (35) follows from (31)–(34) and (37),
  and the empty column is handled.
- **Proposition 2's inclusion argument:** each slot's volume is at most
  p_h q_h rK, and the footprints are disjoint.
- **The certificate:** duals rounded up; a cutoff stop that returns no point
  counts as proof.
- **The Lagrangian bound in the code:** formed only with the artificials at
  zero and every pair bounded; the geometric dual bound is ∞ unless the slot
  set is exact.
- **The cover cuts (59):** each term is a valid truck count for integer plans
  under both load models.
- **The fleet-vector partition (81):** disjoint and exhaustive.
