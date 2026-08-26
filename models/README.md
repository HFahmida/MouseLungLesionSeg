# Models

Trained model weights are hosted on Zenodo and are intentionally excluded from GitHub.

- Zenodo record: [https://zenodo.org/records/22050734](https://zenodo.org/records/22050734)
- Version-specific DOI: [10.5281/zenodo.22050734](https://doi.org/10.5281/zenodo.22050734)
- Concept DOI: [10.5281/zenodo.22050733](https://doi.org/10.5281/zenodo.22050733)
- Model license: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)
- File access: Restricted

Sign in to Zenodo and use approved access to view or download the files. Then extract the downloaded model archive or archives directly into this `models/` directory.

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

There should not be a nested `models/models/` directory. Because the Zenodo files are restricted, their filenames and checksums are not present in the public record metadata. See the main [`README.md`](../README.md) for an authorized metadata command, installation instructions, and inference examples. See [`MODEL_FOLDER_STRUCTURE.md`](../MODEL_FOLDER_STRUCTURE.md) for the full extracted layout.

All downloaded and extracted contents of this directory are ignored by Git. Only this instruction file is tracked.
