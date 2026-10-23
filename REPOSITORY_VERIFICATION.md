# Repository verification

Verification date: 23 October 2026. This is a local annotations-and-splits release. Nothing was uploaded, published or pushed; no Git repository or commit history was initialized or copied. No model evaluation or score/subset generation was performed during packaging.

## Membership and identifier checks — PASS

| Split | TXT entries | CSV rows | Duplicates | Membership match |
|---|---:|---:|---:|---|
| high_MSR | 2,483 | 2,483 | 0 | Exact, including order |
| low_MSR | 12,725 | 12,725 | 0 | Exact, including order |
| high_VJ | 624 | 624 | 0 | Exact, including order |
| low_VJ | 3,907 | 3,907 | 0 | Exact, including order |
| high_HJ | 2,290 | 2,290 | 0 | Exact, including order |
| low_HJ | 7,046 | 7,046 | 0 | Exact, including order |

All stems have the canonical `scenario_<UUID>` form without an extension. CSV `scenario_id`, `scenario_stem` and `pt_filename` agree. Every listed processed filename was found in the original validation inventory. Lists are byte-identical to the verified sources. High and low lists are disjoint within each metric. Existing cross-metric overlap statistics are copied unchanged.

## Low-MSR manifest split — PASS

| File | Scenario records | Bytes |
|---|---:|---:|
| `low_MSR_scenarios_part1.csv` | 6,363 | 4,688,508 |
| `low_MSR_scenarios_part2.csv` | 6,362 | 4,691,968 |

Both parts retain the identical original header. Reconstructing the original header followed by part1 data rows and part2 data rows is byte-for-byte identical to the pre-split release CSV, including column order, numeric text and row order. Original CSV SHA-256: `4db8954322d72d6a7423a01b5a5fcf6019872eca21cf5716ae92237212334e48`. The combined 12,725 scenario IDs are unique, with no omissions, and match `evaluation_splits/low_MSR.txt` in order. That TXT is byte-identical to its pre-correction version. The oversized release CSV was replaced by these parts; the original project manifest was left untouched.

## Scientific preservation — PASS

All original CSV fields and string values match the verified sources exactly except `pt_path`, which now has portable form `val/<pt_filename>`. No numeric score was parsed/recomputed during packaging, no threshold was refitted and no membership was regenerated.

The full agent calibration is deeply equal to its source after accounting for the single `processed_root` string edit to `data/DeMo_processed`. Percentiles, normalization parameters, definitions, units, counts and historical status flags are preserved. The scene calibration is byte-identical to its source. Its original agent-checksum reference is intentionally retained and mapped to the sanitized release file by `calibration/RELEASE_PROVENANCE.json`.

The three statistics CSVs and all three figures are byte-identical to their original verified files. The repository contains no new prediction metrics or unverified model results.

## Source preservation — PASS

All 20 copied source artifacts match the hashes recorded before copying. All 48 entries in the previous subset-generation source manifest still match their original hashes. No writes were made to the original project, model files, experimental results or dataset files. Only the separate release folder and local verification records outside it were created.

## Anonymity, metadata and exclusions — PASS for local checks

Text contents and filenames were checked for local absolute paths, email addresses, known local identifying tokens, affiliation-name patterns, credential signatures and non-upstream account URLs. No findings remain. There are no symbolic links, Git metadata, caches, logs, Python/model/inference files, checkpoints, original trajectory arrays, processed datasets or original parquet/map files.

The three PNGs have no EXIF records; their only metadata is generic Matplotlib software information and DPI. Their visible labels contain scenario/agent identifiers and scientific annotations. Official AV2 dataset/API/license references and the required public dataset-owner copyright notice are intentionally retained. These are upstream credits, not benchmark-author identities.

Local scanning cannot certify unknown personal names, external account anonymity, a hosted preview, or correlation with unpublished material. Manual inspection of the final host/account settings and the three figures remains appropriate before public release.

## File sizes and hosting checks

- Files: **30**.
- Logical content size: **24,745,918 bytes (23.600 MiB)**.
- Largest file: `subset_manifests/low_HJ_scenarios.csv`, **5,372,503 bytes**.
- Anonymous GitHub per-file requirement supplied for this correction: **PASS**; all files are strictly below **8,000,000 bytes** (decimal 8 MB).
- Both low-MSR CSV parts are strictly below **7,500,000 bytes** (decimal 7.5 MB).
- File sizes were checked locally; no upload or online action was performed. This verifies the supplied per-file limit without asserting any additional account or total-storage quota.


## Manual review before publication

1. **Licensing:** review `documentation/DATA_AVAILABILITY.md`, the README attribution and the three source-derived figures. The upstream AV2 data license is CC BY-NC-SA 4.0; confirm the redistribution/license treatment of derived annotations and intended benchmark-specific release terms. A new blanket repository license was not inferred.
2. **Anonymity:** inspect the rendered README, documentation and figures, as well as the eventual hosting identity, URL and repository/account settings. Required upstream dataset credits are deliberate. Do not add benchmark-author identities or original commit history.
3. **Hosting settings:** the supplied 8 MB per-file requirement passes. Review any additional account or total-storage settings during manual upload.

These manual items do not indicate missing scientific artifacts or changed benchmark memberships.

## Complete directory tree

```text
AV2-NaturalNoise-Benchmark/
├── calibration/
│   ├── RELEASE_PROVENANCE.json
│   ├── scene_MSR_calibration.json
│   └── train_calibration.json
├── documentation/
│   ├── DATA_AVAILABILITY.md
│   ├── METRIC_DEFINITIONS.md
│   └── SUBSET_SELECTION.md
├── evaluation_splits/
│   ├── high_HJ.txt
│   ├── high_MSR.txt
│   ├── high_VJ.txt
│   ├── low_HJ.txt
│   ├── low_MSR.txt
│   └── low_VJ.txt
├── figures/
│   ├── HJ_representative_trajectories.png
│   ├── MSR_representative_trajectories.png
│   └── VJ_representative_trajectories.png
├── statistics/
│   ├── subset_counts.csv
│   ├── subset_distributions.csv
│   └── subset_overlap_statistics.csv
├── subset_manifests/
│   ├── high_HJ_scenarios.csv
│   ├── high_MSR_scenarios.csv
│   ├── high_VJ_scenarios.csv
│   ├── low_HJ_scenarios.csv
│   ├── low_MSR_scenarios_part1.csv
│   ├── low_MSR_scenarios_part2.csv
│   └── low_VJ_scenarios.csv
├── .gitignore
├── ASSET_CHECKSUMS.sha256
├── README.md
├── REPOSITORY_VERIFICATION.md
└── requirements.txt
```

`ASSET_CHECKSUMS.sha256` covers every other released file, including this report; it excludes itself to avoid a self-referential checksum. Check with `sha256sum -c ASSET_CHECKSUMS.sha256` from the release folder. No original local source paths or source-verification records are included in this upload folder.
