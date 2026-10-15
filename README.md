# spaVelo benchmark source fork

This fork supports [VelocityBenchmarking](https://github.com/vikkihuangkexin/VelocityBenchmarking) and is based on [1062638515/spavelo](https://github.com/1062638515/spavelo) commit `fce5428c2dea452b2974c01eba6eeb1e0d9ece6e`. Please cite the original spaVelo work when using the method.

## Compatibility changes

| File and location | Change | Reason |
| --- | --- | --- |
| `experiments/preprocess_real.py`, `_expressed_genes` and spatial gene-selection branches | Sums spliced and unspliced counts without sparse-matrix `.A` access; keeps the original threshold of 500. | Supports current sparse-matrix APIs without changing the threshold. |
| Same file, spatial gene-selection branches | Passes `spatial_layer` to Squidpy's `spatial_neighbors` and exposes `moran_jobs`. | Uses the selected coordinate field and lets callers bound Moran workers. |
| `experiments/preprocess_real.py`, `preprocess_data_real` | Converts sparse `X`, spliced, and unspliced matrices with `.toarray()` and makes UMAP calculation optional through `compute_umap` (default `True`). | Accepts sparse formats beyond CSR and avoids UMAP work when it is not needed. |
| `experiments/preprocess_real.py`, `_run_dynamo_dynamics` and `get_init_variables` | Converts only per-gene stochastic-regression `SVD did not converge` errors to invalid fits, restores Dynamo's original function afterward, and records the skipped count. | Lets downstream gene filtering continue instead of aborting the dataset. |
| `spaVelo.py`, `SPAVELO.train_model` data setup and backward pass | Removes unused device tensors and the unnecessary `retain_graph=True`. | Reduces training memory and graph retention. |
| `spaVelo.py`, `SPAVELO.train_model` logging and recorder | Adds `verbose`, `progress_callback`, and `recorder_save_every`; saves once more at the end. | Gives callers control over progress output and repeated kinetic CSV writes. |

The architecture, ELBO terms, and optimizer are unchanged. Filtering numerically degenerate genes can change the modeled gene set and results. `recorder_save_every` defaults to `1`, preserving per-epoch saves unless a caller changes it.

## Environment

Reference versions: Python 3.11.15 and PyTorch 2.2.1+cu121. No environment lock is supplied; the upstream machine-specific `environment.yml` was omitted.

## Use

Run from this repository root, or place it and `experiments/` on `PYTHONPATH`, so imports from `spaVeloDenoise.py` and `preprocess_real.py` resolve to this checkout. The separate benchmark runner supplies inputs and training parameters. Datasets, notebooks, checkpoints, and results are not included.

## License and provenance

The upstream snapshot has no standalone `LICENSE`. This fork adds no new license statement; consult the upstream authors for licensing terms.
