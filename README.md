# mdRNAcat
This repository contains metadata to access, and code to do analysis and 
validation of the [mdRNAcat](https://huggingface.co/datasets/anonymous8576/mdrnacat) 
dataset.

## Setup environment
Use Python >=3.10 in a fresh environment:
```sh
python -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

On Windows, activate with `.venv\Scripts\activate` instead.

## Dataset metadata (Croissant)

The root-level [metadata.jsonld](metadata.jsonld) describes mdRNAcat in
Croissant 1.1 format. It includes two embedded record sets:

- `file_catalog`: 96 HDF5 files covering all 48 systems, with revision-pinned
  download URLs, file sizes, repository-provided SHA-256 checksums, and
  documented sampling intervals.
- `hdf5_dictionary`: 33 manuscript-derived field descriptions, including HDF5
  path templates, array shapes, data types, and units.

Reviewers can inspect these records without downloading the `.h5` files.
The metadata describes the dataset; it does not contain trajectory coordinates
or forces, provide native HDF5 extraction, or automatically enable a trajectory
preview in Hugging Face's Dataset Viewer. Field descriptions are based on the
manuscript rather than an independent inspection of every HDF5 file.

To validate the metadata or read the embedded catalogues, install the optional
Croissant package in the environment created above:

```sh
python -m pip install mlcroissant==1.1.0
mlcroissant validate --jsonld metadata.jsonld
```

From the repository root:

```python
import mlcroissant as mlc

dataset = mlc.Dataset("metadata.jsonld")
files = list(dataset.records("file_catalog"))
fields = list(dataset.records("hdf5_dictionary"))
print(len(files), len(fields))  # 96 files, 33 field descriptions
```

Both record sets are embedded in `metadata.jsonld`, so reading them requires no
trajectory downloads or network access after package installation.

## mdRNAcat dataset plots and validation
Run the notebook `mdrnacat_plots.ipynb` using:
```sh
source .venv/bin/activate
jupyter lab mdrnacat_plots.ipynb
```

Edit Cell 2 to point `DATA_ROOT` to the directory where you downloaded the 
[mdRNAcat](https://huggingface.co/datasets/anonymous8576/mdrnacat) dataset. 
Then, run every cell from the top in your browser or notebook viewer of choice.

## mdRNAcat sequence and structure coverage analysis
Run the notebook `sequence_structural_coverage.ipynb` using:
```sh
source .venv/bin/activate
jupyter lab sequence_structural_coverage.ipynb
```

Run every cell from the top. Exactly one four-panel UltraPlot figure is created.
All 48 mdRNAcat PDB IDs are included in `mdRNAcat_pdb_ids.txt`.
No account, API key, private path or external configuration file is needed.

The default run uses the bundled, checksum-verified `data/` tables and needs no
network after package installation. Set `REFRESH_INPUTS = True` to download
public RCSB metadata and RNA 3D Hub loop annotations instead. Allow tens of
minutes for a refresh, depending on network speed and service availability.
Successful responses are cached in `cache/<configuration hash>/`. Transient
failures stop the run after retries; rerunning reuses completed downloads.
`OFFLINE = True` additionally prohibits cache misses in refresh mode.

The final plot (PDF and PNG), tables, attrition counts, chain descriptors and a
manifest with package versions and input hashes are written to
`results/<configuration hash>/`. Keep the bundled `data/` directory with the notebook. 
Archive refreshed cache and result directories for the manuscript release: a 
release-date cutoff does not freeze later database corrections.
