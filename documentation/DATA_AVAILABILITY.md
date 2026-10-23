# Data availability, attribution and licensing

This repository distributes fixed AV2 validation scenario identifiers, derived scalar annotations and contextual summaries, training-derived calibration parameters, verified descriptive statistics, documentation and derived illustrative figures. Original AV2 trajectory arrays, HD-map files, `.parquet` files and processed `.pt` files are not included. No model code, checkpoints, inference outputs or performance claims are distributed.

Download the original Motion Forecasting dataset separately from the [official Argoverse 2 dataset page](https://www.argoverse.org/av2.html), using its download links and terms. The [official AV2 API](https://github.com/argoverse/av2-api) documents access to those files. Users supply their own data and compatible processing environment.

Dataset reference: **Argoverse 2: Next Generation Datasets for Self-driving Perception and Forecasting**, NeurIPS Datasets and Benchmarks 2021. The full publication and citation are linked from the official dataset page. Dataset attribution: **© 2021 Argo AI, LLC**. These public dataset credits do not identify the authors of the benchmark submission.

The [official Argoverse terms](https://www.argoverse.org/about.html#terms-of-use) specify **Creative Commons Attribution–NonCommercial–ShareAlike 4.0 International (CC BY-NC-SA 4.0)** for dataset data/documentation. Read the [license](https://creativecommons.org/licenses/by-nc-sa/4.0/) before using or redistributing dataset-derived materials. The official API's MIT license is distinct and does not replace the dataset license.

The figures are illustrative renderings of selected AV2 histories. Original trajectory samples were not changed when those figures were created. The released scalar annotations are derived materials, not a substitute for obtaining the original dataset. Local path fields were sanitized for this anonymous release; scenario identities, score values and cutoffs were preserved.

Before public release, manually confirm the redistribution and attribution treatment of these derived annotations and figures, and the intended license for the benchmark-specific materials. No blanket repository license was assigned automatically. Upstream rights and restrictions remain applicable; this notice does not grant an alternative license for AV2. Keep the dataset credit and license links in any redistributed version.

The release supports fixed-membership reproducibility and inspection of calibration. It does not include the original preprocessing/score-generation implementation or an executable end-to-end benchmark. Independent recomputation requires the original dataset, the formulas and the exact context protocol described in the other documentation. Earlier training verification lacked original training parquet, so preservation was supported by source/structural consistency and validation raw comparisons rather than direct raw verification of every training record.

For double-anonymous review, the folder omits benchmark-author names, affiliations, email addresses, account names and commit history. Before upload, inspect the final hosted preview and account/repository settings for identity leakage; local content scanning cannot verify hosting-account anonymity or unseen identities. Only public upstream dataset/API references are retained.
