# Fixed subset selection protocol

This document describes the already completed selection. Packaging did not recalculate scores, fit thresholds or regenerate memberships. The six lists and corresponding CSV row identities are preserved exactly.

## Focal VJ and HJ

Each validation scenario retains its original focal agent. Retrieve its previously computed score and use the corresponding object's existing training-agent percentile thresholds:

- High: defined score > type-specific training P90.
- Low: defined score ≤ type-specific training P50.
- Middle: training P50 < defined score ≤ training P90.
- Undefined: excluded from the comparison.

No score at P90 is promoted into high. All focal histories have 50 observations, and there are no undefined focal VJ/HJ scores. Requiring at least 20 focal observations leaves both comparisons unchanged. Thresholds refer to the existing training population of eligible agents within type, not a newly fitted focal-only population.

## Reference model-visible context

Contextual MSR uses the same reference-loader geometry for every training and validation scene. Let the original focal position and heading at historical index 49 define a local coordinate frame. The original 50-history/60-future scenario structure is preserved.

1. Choose non-focal actors valid at index 49 whose Euclidean distance to the focal agent is strictly below 150 m.
2. Translate and rotate actor current positions and lane samples into the focal frame.
3. Mark lane samples strictly inside the square (−150,150) × (−150,150). Retain a lane if any sample is inside.
4. Replace outside samples of retained lanes with zero, exactly as in the reference loader.
5. Retain contextual actors whose minimum distance to the retained zero-filled lane samples is strictly below 5 m. The focal itself is retained separately by the original loader.
6. For MSR only, restrict those model-visible non-focal actors to VEHICLE, PEDESTRIAN, MOTORCYCLIST, CYCLIST and BUS.

The zero-filled lane points are part of the literal original selection rule, including distances to artificial zero points. They were not silently removed or replaced with a different geometry calculation. Thirty-two training and 32 validation scenes were checked against the unmodified reference loader, with identical membership. Future-state poisoning did not change selection in these checks.

This procedure yielded 3,577,998 eligible contextual-agent records across 199,908 training scenes, and 446,321 records across 24,988 validation scenes. There were 37 training and five validation scenes with no eligible context. Historical annotation-presence masks, not future target masks, determine MSR.

## Contextual scene MSR

For each eligible contextual actor a, use its existing training object-type agent MSR P90, qₐ. The scene score is

$$S_{\mathrm{MSR}}(s)=\frac{\sum_{a\in A_s}\mathbf{1}[\mathrm{MSR}_a>q_a]}{|A_s|}.$$

There is no score when |Aₛ|=0. Such scenes remain separate and are not assigned zero or placed in low-MSR.

The same context rules were applied to training scenes before fitting the scene distribution. Among 199,871 nonempty training scenes, linear quantiles gave P50=0.07142857142857142 and P90=0.20689655172413793. Existing actor-level type cutoffs were reused; they were not refitted on model-visible actors or validation actors. Scene high/low/middle use the same strict-high and inclusive-low rules as above. No VJ or HJ contributes to this scene statistic.

## Fixed counts and boundary handling

| Metric | High | Low | Middle | Unavailable |
|---|---:|---:|---:|---:|
| MSR | 2,483 | 12,725 | 9,775 | 5 empty-context scenes |
| VJ | 624 | 3,907 | 20,457 | 0 |
| HJ | 2,290 | 7,046 | 15,652 | 0 |

Validation boundary ties were: MSR, 455 at P50 and 17 at P90; VJ, none; HJ, one at P50 and two at P90. P50 ties are included in low; P90 ties are excluded from high. Exact floating-point comparisons were used with round-trip reading. Near-boundary diagnostics at tolerance 1e-12 agreed with these tie counts. There was no random tie breaking and no adjustment to reach a target validation fraction.

High-set intersections are MSR–VJ: 85 (Jaccard 0.028127...), MSR–HJ: 237 (0.052249...), VJ–HJ: 146 (0.052746...). The three high sets share 26 scenes. Exact previously computed values are in `statistics/subset_overlap_statistics.csv`. A scenario may belong to several independent metric splits; no combined subset or combined score is implied.

## Portability and interpretation

TXT entries are `scenario_<UUID>` stems without a filename extension. Use them with a stem-based whitelist, or remove only the prefix for UUID-based dataset readers. CSV `pt_path` values are relative to the processed dataset root. Original raw scenarios can instead be located by UUID in the official validation dataset. All methods should retain identical target identities and prediction windows when using these lists.

The reference loader was tested on all six list memberships. In five sampled scenarios per subset, filtered and full-dataset loading produced identical inputs, targets, masks and metadata, with 50 historical and 60 future steps. This is a data compatibility check, not a forecasting evaluation. Model implementations and model-performance results are absent from this release.

Because context is valid at index 49 and audited histories have no internal gaps, contextual MSR reflects late-starting histories rather than within-ID reappearances. Scene fractions can be unstable with few eligible actors: 283 high-MSR scenes have fewer than five contexts, including two with one context. Low-VJ strongly favours nearly stationary actors, and high-HJ can select genuine turns. High-VJ contains just one focal motorcyclist and one cyclist. Use the provided descriptive distributions when assessing these confounds; do not reinterpret high/low as validated noisy/clean labels.

## Calibration metadata provenance

The agent calibration preserves historical status flags from the original analysis stage. Those flags are not a new statement that the six released lists are undefined; the fixed lists and manifests are authoritative for this release. The scene calibration retains its original agent-file checksum. `calibration/RELEASE_PROVENANCE.json` maps that checksum to the release agent calibration, whose only changed field is the portable dataset-root path.
