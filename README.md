# AV2 Natural Trajectory Quality Benchmark

This anonymous release provides six fixed validation splits for studying motion forecasting under naturally incomplete and kinematically irregular trajectory histories. The splits are derived from the Argoverse 2 (AV2) Motion Forecasting dataset. They preserve the original scenario identities and trajectories; no synthetic corruption, interpolation or smoothing was applied during quality annotation.

The benchmark separates historical observation availability (**MSR**), scalar-speed irregularity (**VJ**) and heading irregularity (**HJ**). These are trajectory-quality proxies, not ground-truth sensor-noise labels. The release contains split identifiers, derived annotations, frozen training calibration, descriptive statistics and illustrative figures. It contains no forecasting model, weights, predictions or model-performance results.

## Dataset and released splits

AV2 Motion Forecasting consists of 11-second scenarios sampled at 10 Hz. This release uses the verified local validation inventory of **24,988 scenarios**. Quality measures use only the first **50 historical states**, indices 0–49, with Δt = 0.1 s. The original 60 future states are reserved for later prediction evaluation and were not used to select these splits. See the [official AV2 dataset description and downloads](https://www.argoverse.org/av2.html).

| Metric | High | Low | Middle, not distributed as a separate split |
|---|---:|---:|---:|
| Contextual MSR | 2,483 (9.94%) | 12,725 (50.92%) | 9,775 |
| Focal VJ | 624 (2.50%) | 3,907 (15.64%) | 20,457 |
| Focal HJ | 2,290 (9.16%) | 7,046 (28.20%) | 15,652 |

Five validation scenarios have no eligible model-visible context and are excluded from both MSR groups. Focal VJ/HJ are defined in every validation scenario. Middle and empty-context counts are retained in `statistics/subset_counts.csv`; this release includes only the six requested high/low membership lists. A scenario may occur in several metric-specific splits. High and low are disjoint within each metric.

## Metric definitions

For an agent, let mₜ ∈ {0,1} denote the original annotation-presence mask, vₜ its scalar speed in m/s and θₜ its bounding-box heading in radians. Let C be the set of t ∈ {0,…,47} for which mₜmₜ₊₁mₜ₊₂ = 1. Define wrap(α) = ((α + π) mod 2π) − π.

**Missing-step ratio:**

$$\mathrm{MSR}=1-\frac{1}{50}\sum_{t=0}^{49}m_t.$$

MSR includes leading and trailing absence. It does not identify the reason an annotation is absent.

**Scalar-speed irregularity:**

$$\mathrm{VJ}=\operatorname{median}_{t\in C}\frac{|v_{t+2}-2v_{t+1}+v_t|}{\Delta t^2}.$$

VJ has units m/s³ and is a scalar-speed jerk proxy. It is not vector jerk.

**Heading irregularity:**

$$d_t=\operatorname{wrap}(\theta_{t+1}-\theta_t),\qquad
\mathrm{HJ}=\operatorname{median}_{t\in C}\frac{|\operatorname{wrap}(d_{t+1}-d_t)|}{\Delta t^2}.$$

HJ has units rad/s² and measures changes in heading increments; it is not angular jerk. Circular differences prevent ±π representation crossings from being mistaken for large turns.

Primary agent annotation includes VEHICLE, PEDESTRIAN, MOTORCYCLIST, CYCLIST and BUS with at least one historical observation. Other types and agents with zero historical observations are excluded. VJ/HJ require at least five valid adjacent triples; overlapping triples count, so seven contiguous observations suffice. Missing kinematic values remain unavailable, not zero. Differences never bridge invalid frames. [Full definitions and eligibility](documentation/METRIC_DEFINITIONS.md).

## Training calibration and scenario selection

All existing agent thresholds and normalization parameters were estimated from **training agents only**, separately by object type. High focal VJ/HJ requires score **> training P90**; low requires score **≤ training P50**. Boundary ties at P90 are excluded from high; ties at P50 are retained in low. The training reference population comprises eligible agents within type, not only focal agents. Therefore validation group sizes need not be 10% or 50%.

Focal histories are complete, so MSR selection uses contextual agents visible to the reference validation loader. Context is selected at timestep 49 using the same 150 m radius and cropped-lane distance rules in training and validation, followed by the five-type eligibility filter. For scene s, define:

$$S_{\mathrm{MSR}}(s)=\frac{1}{|A_s|}\sum_{a\in A_s}\mathbf{1}\!\left[\mathrm{MSR}_a>Q^{\mathrm{train}}_{0.90,\mathrm{type}(a),\mathrm{MSR}}\right].$$

Here Aₛ is the eligible model-visible non-focal context set. The scene score is unavailable when Aₛ is empty. Scene-level P50/P90 were fitted on **199,871 nonempty training scenes**, excluding 37 empty scenes:

| Scene MSR threshold | Frozen value |
|---|---:|
| P50 | 0.07142857142857142 |
| P90 | 0.20689655172413793 |

High MSR uses Sₘₛᵣ > P90; low uses Sₘₛᵣ ≤ P50. This is an MSR-only scene statistic, not a combined MSR+VJ+HJ score. No validation thresholds were fitted. [Exact context and selection protocol](documentation/SUBSET_SELECTION.md).

The JSON files retain the full original training-derived scientific values. Only a local dataset-root path was made portable. `calibration/RELEASE_PROVENANCE.json` explains original versus release checksums, including the original agent-calibration checksum retained by the scene calibration.

## Download and use

Obtain AV2 Motion Forecasting data separately from the [official AV2 website](https://www.argoverse.org/av2.html), following its download instructions and terms. Use the validation split, and preprocess it in your own environment if your forecasting model requires `.pt` files. No AV2 data download is performed by this repository.

The text files contain **filename stems** such as `scenario_<UUID>`, without `.pt`. A loader that filters on stems can consume them directly. For loaders that expect UUIDs, remove only the leading `scenario_` prefix. CSV manifests also provide `scenario_id`, `scenario_stem` and `pt_filename`. Their `pt_path` field is relative to the processed dataset root, for example `val/scenario_<UUID>.pt`; no machine-specific absolute paths are needed.

```python
from pathlib import Path

release = Path(".")
processed_root = Path("data/DeMo_processed")  # set to your own dataset root
split_file = release / "evaluation_splits/high_VJ.txt"
stems = split_file.read_text().splitlines()
paths = [processed_root / "val" / f"{stem}.pt" for stem in stems]
missing = [path for path in paths if not path.is_file()]
if missing:
    raise FileNotFoundError(f"Missing {len(missing)} selected scenarios")
```

For original AV2 files, UUID `u` generally maps to `val/u/scenario_u.parquet` under the raw dataset root. Preserve the same focal target, 50 historical steps and 60 future steps for every forecasting method. These manifests select scenarios; they do not transform trajectories or alter targets. Reference-loader membership and sampled filtered-versus-unfiltered inputs/targets were checked before packaging. Different model-specific context encoders may see different neighbours even on the same scenarios.

### Reading the split low-MSR manifest

The low-MSR manifest is distributed as `subset_manifests/low_MSR_scenarios_part1.csv` (6,363 records) and `subset_manifests/low_MSR_scenarios_part2.csv` (6,362 records), each below 7,500,000 bytes. Both files have the same column header. Read their data rows in **part1, then part2** order to recover all **12,725** original records without changing any values. When combining CSVs, include the header only once; do not concatenate the second header as a data row.

```python
import csv
from pathlib import Path

rows = []
for name in ("low_MSR_scenarios_part1.csv", "low_MSR_scenarios_part2.csv"):
    with (Path("subset_manifests") / name).open(newline="") as stream:
        rows.extend(csv.DictReader(stream))
assert len(rows) == 12725
assert len({row["scenario_id"] for row in rows}) == 12725
```

`evaluation_splits/low_MSR.txt` remains unchanged and contains the complete ordered scenario list. Use that TXT directly for evaluation filtering; the CSV split changes only annotation-file packaging. All other scenario manifests are single CSV files.

No external Python packages are required to read the TXT, CSV and JSON files with `pathlib`, `csv` and `json`. `requirements.txt` records this annotations-only scope. Model preprocessing and the official AV2 API require their own environments, which are not supplied here.

## Released contents and reproducibility

- `evaluation_splits/`: the six fixed stem lists.
- `subset_manifests/`: one logical scenario-level manifest for each list (low MSR spans the two ordered CSV parts described above), including scores, reference cutoffs, focal metadata and contextual summaries.
- `calibration/`: original training agent values, training scene MSR values and a metadata-sanitization checksum mapping.
- `statistics/`: previously verified counts, overlap statistics and descriptive distributions, copied unchanged.
- `figures/`: previously generated representative historical-trajectory illustrations, copied unchanged.
- `documentation/`: metric definitions, selection protocol and data availability/attribution.
- `REPOSITORY_VERIFICATION.md`: membership, calibration, path, metadata and file-size checks.

Scores and splits were **not recomputed** for this release. No raw trajectory arrays, map files, processed datasets, model code, model weights or inference outputs are distributed. Figures are derived visualizations of selected histories, not original machine-readable trajectory files. The release is designed to reproduce fixed memberships and inspect frozen calibration; it does not contain an end-to-end score-generation or forecasting implementation.

## Interpretation and limitations

High MSR indicates limited contextual history. In this inventory, model-visible context is valid at frame 49 and the audited historical masks have no internal gaps; positive context MSR therefore represents late-starting annotation histories. It does not establish occlusion or sensor failure. Fractions are sensitive to small context counts.

High VJ/HJ can reflect genuine acceleration, stop–go motion, turning, orientation ambiguity, state-estimation irregularities or float32 precision effects. Low median metrics can hide short late events. High/low groups are not certified noisy/clean labels. In particular, low-VJ is dominated by nearly stationary focal actors, and some high-group object-type cells are very small. Scene overlap, type, speed and context-count differences must be reported when interpreting subsequent prediction performance. No model robustness or baseline-improvement claim is supported by this release alone.

## Dataset attribution and release terms

Underlying data: **Argoverse 2: Next Generation Datasets for Self-driving Perception and Forecasting**, NeurIPS Datasets and Benchmarks 2021, available through the [official dataset page](https://www.argoverse.org/av2.html). AV2 attribution: **© 2021 Argo AI, LLC**. Source data are licensed under **CC BY-NC-SA 4.0**; see [data availability and licensing](documentation/DATA_AVAILABILITY.md). The reference to AV2 credits the dataset creators and does not identify the authors of this benchmark submission. No unpublished-paper author identities or repository account names are included.
