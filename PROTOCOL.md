# Pantheon+ frozen-fit discrepancy localization protocol

Date: 2026-10-08. Authorized by Téo after the question: where does the
Janus-minus-flat-ΛCDM discrepancy of 47.620 chi-square units arise?

This protocol is committed before inspecting any grouped residual, allocation
or deletion result. The total gap and the earlier global residual plot are
already known: this is a prespecified exploratory follow-up on previously used
data, not a blinded confirmatory experiment. No scientific novelty or causal
interpretation is assumed.

## Frozen inputs and scope

- Use the checksum-pinned Pantheon+ table and full STAT+SYS covariance already
  acquired by `scripts/download_data.py`. Apply the existing `zHD > 0.01` and
  `IS_CALIBRATOR == 0` cuts, preserving source order and all repeated light curves.
- Require 1580 retained records and 1466 distinct CID, as recorded in the existing
  analysis. Match metadata and model arrays exactly before grouping.
- Use `reference.M7_LCDM_OMEGA_M` and `reference.M7_JANUS_Q0` at H0 = 70 only.
  Do not optimize either shape parameter, alter covariances or revise any frozen
  result. Recompute the analytically profiled additive offset as required by
  the given sample. The rounded frozen parameters can introduce tiny differences
  from the originally reported minimum.
- No new light-curve standardization, priors, MCMC, JLA fit, CMB/BAO analysis,
  individual-outlier rejection, or search over alternative bin boundaries.

## Independent numerical control, before localization

A separate script reads and selects the raw text independently of production
data code. It evaluates flat ΛCDM by adaptive quadrature and the direct Janus
2018 equation (29) with 50-digit Decimal arithmetic, from RESULTS.md §2/§5.
It profiles the offset and evaluates the residual quadratic form using a dense
generic linear solve, independent of the production Cholesky likelihood.
Only after this calculation does it compare to the production route.

Fixed gates, all required before publication of localization results:

- Exact sample size/order and metadata alignment.
- Maximum oracle/production distance-modulus difference <= 1e-10 mag per model.
- Absolute oracle/production offset and chi-square differences <= 1e-6.
- Each evaluated full-sample chi-square matches its frozen rounded reference
  within 0.001; the gap matches the frozen difference within 0.002.
- L1 difference between the independent and production signed row allocations
  <= 1e-6, which also bounds the disagreement for every group sum.
- Allocation sums and both complete partitions recover the stable full gap
  within 1e-8 absolute.

Any failure is investigated and documented before interpreting grouped outcomes.
These new checks do not relax any existing numerical gate.

## Primary allocation and fixed partitions

Starting from the published Gaussian quadratic likelihood (Brout et al. 2022,
eq. 9) and the offset profiling in RESULTS.md §6.1, let r_m denote the globally
profiled residual vector for model m. Solve C w_m = r_m and define

    a_i = r_J,i * w_J,i - r_L,i * w_L,i.

Then sum_i a_i = chi2_J - chi2_L exactly in real arithmetic. For symmetric
precision this convention allocates each cross term equally to its endpoints.
It is equivariant to row permutations, but it is not independent evidence from
each record. Allocations may be negative and individual groups may exceed the
total. Report signed chi-square units, not causal percentages or local sigmas.

Two complete, separately reported partitions:

1. Redshift intervals: (0.01, 0.05], (0.05, 0.10], (0.10, 0.30],
   (0.30, 0.50], (0.50, 1.00], (1.00, infinity).
2. Every `IDSURVEY` value present in the selected table, in numeric ID order,
   with the official release names. No merging of small groups after looking.

Each group reports record count, distinct CID count, minimum/maximum zHD,
both signed model allocations and their difference. The row-level audit file
retains source index, CID and IDSURVEY; it is not an outlier-rejection list.
For interpretation, also show the cumulative signed allocation in ascending z
and the frozen prediction difference, including the two full-sample offsets.
The cumulative curve is descriptive, not a cutoff search.

## Secondary leave-group-out controls

For every redshift bin and every survey, remove its records and use the retained
principal covariance C_HH, not the retained block of C^-1. Keep both shape
parameters frozen and reprofile only each model's offset on the retained sample.
Report the remaining gap and

    gap_change = full_gap - retained_gap.

A positive change means the fixed-shape ΛCDM advantage shrinks when removing
that group. Deletion effects are non-additive, not likelihood-ratio tests and
not the same quantity as primary allocations. For each model individually,
require retained chi-square <= full chi-square + 1e-6; nonnegative chi-square
is required within 1e-8 numerical tolerance.

For every survey also perform CID-closed removal: remove all retained records
whose CID occurs in that survey, including records from other surveys. Report
the extra records removed. These closed sets overlap and are not a partition.
Empty retained samples, if any, are reported as undefined rather than fitted.

## Controls and reporting

Synthetic tests cover dense-solve agreement, offset invariance, row permutation,
partition conservation, a correlated example with negative allocation, and
the distinction between marginal covariance and conditional precision blocks.
Report all fixed groups and all deletion variants, including unfavorable or
counterintuitive results. No bootstrap, p-value, multiple-testing claim or
survey-malfunction verdict is planned. Do not combine group allocations from
the two different partitions.

Survey and redshift are confounded; calibration is partly shared across surveys.
Localization alone cannot identify instrumental error versus cosmological curve
shape. The frozen-shape deletion calculation is not a fresh best-fit comparison.
Claims of a physical explanation require an independently designed follow-up.

Deliverables: independent oracle JSON, complete group/deletion/row CSV files,
a standalone diagnostic figure, reproducible scripts and tests, a results report,
and a concise addition to RESULTS.md. Existing chains and figures are preserved.

## Primary references

- [Pantheon+ distance release README](https://github.com/PantheonPlusSH0ES/DataRelease/blob/main/Pantheon%2B_Data/4_DISTANCES_AND_COVAR/README),
  consulted 2026-10-08: IDSURVEY names, repeated light curves and the full-covariance
  requirement. Labels are recorded in the script; data bytes remain SHA256-pinned.
- [Brout et al., arXiv:2202.04077v2](https://arxiv.org/html/2202.04077v2),
  §II.2 equations 5 and 7 (covariance), §II.3 equation 9 (likelihood),
  §III.2.1 (shared photometric calibration).
- RESULTS.md §2 (Janus equation provenance), §5 (model implementation),
  §6.1 (offset), §7.1 (frozen fits) and §7.3 (record/object distinction).
