# Models

Trained model weights are hosted on Zenodo and are intentionally excluded from GitHub.

- Zenodo record: [https://zenodo.org/records/22050734](https://zenodo.org/records/22050734)
- DOI: [10.5281/zenodo.22050734](https://doi.org/10.5281/zenodo.22050734)

Download the model archive or archives listed under **Files** in the Zenodo record, then extract them directly into this `models/` directory.

```bash
ZENODO_ARCHIVE=/absolute/path/to/archive-name-from-zenodo.zip
unzip "$ZENODO_ARCHIVE" -d models
```

After extraction, confirm that these three package directories exist:

```text
models/
|-- README.md
|-- mouse_lung_model/
|-- mouse_lung_lesion_resenc_fold_all/
`-- mouse_lung_lesion_5fold/
```

Representative required paths are:

```text
models/mouse_lung_model/inference_nnUNet.sh
models/mouse_lung_model/nnUNet_results/Dataset101_mice_final/

models/mouse_lung_lesion_resenc_fold_all/inference_nnUNet.sh
models/mouse_lung_lesion_resenc_fold_all/nnUNet_results/Dataset101_MRI-Lung/

models/mouse_lung_lesion_5fold/inference_nnUNet.sh
models/mouse_lung_lesion_5fold/nnUNet_results/Dataset103_MRI-Lung/
```

There should not be a nested `models/models/` directory. See the main [`README.md`](../README.md) for commands that print the exact Zenodo filenames and checksums, installation instructions, and inference examples. See [`MODEL_FOLDER_STRUCTURE.md`](../MODEL_FOLDER_STRUCTURE.md) for the full extracted layout.

All downloaded and extracted contents of this directory are ignored by Git. Only this instruction file is tracked.
