# Models

Trained model weights and representative example data for **MouseLungLesionSeg v1.0** are hosted on Zenodo and are intentionally excluded from GitHub.

> **Research use only:** These models are not medical devices and have not been validated or approved for clinical diagnosis, treatment planning, or patient-care decisions.

## Zenodo release

- **Title:** *MouseLungLesionSeg v1.0: A Two-Staged AI Approach for Lung Lesion Segmentation on MRI for Mice Tumor Models*
- **Version:** V1
- **Published:** August 21, 2026
- **Zenodo record:** [https://zenodo.org/records/22050734](https://zenodo.org/records/22050734)
- **Version-specific DOI:** [10.5281/zenodo.22050734](https://doi.org/10.5281/zenodo.22050734)
- **Concept DOI:** [10.5281/zenodo.22050733](https://doi.org/10.5281/zenodo.22050733)
- **File access:** Restricted
- **Model license:** [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)

The Zenodo record metadata are public, but the uploaded files are restricted. Sign in to Zenodo and request access, or use access granted by the record owner, before attempting to view or download the files.

## What the Zenodo record provides

The release provides trained **nnU-Net v2** weights for automated segmentation of mouse lungs and lung lesions in T2-weighted (T2W) MRI. It also includes representative, de-identified sample MRI volumes with corresponding reference annotations and AI-generated predictions for demonstrating and evaluating the inference workflow.

The pipeline uses two independently trained 3D full-resolution segmentation models:

1. **Lung segmentation:** identifies the mouse lung region.
2. **Lung-lesion segmentation:** identifies suspected lesions from the same input MRI.

The two predictions are subsequently merged. Predicted lesion voxels are retained only when they occur within the predicted lung mask. The final combined segmentation uses:

| Label | Meaning |
|---:|---|
| `0` | Background |
| `1` | Lung without predicted lesion |
| `2` | Predicted lesion within the predicted lung |

Each input is a single-channel T2W MRI volume in compressed NIfTI format (`.nii.gz`) using the nnU-Net naming convention:

```text
CASE_IDENTIFIER_0000.nii.gz
```

The model archive includes the trained checkpoint and nnU-Net metadata required for inference, including `checkpoint_final.pth`, `dataset.json`, `dataset_fingerprint.json`, and `plans.json`.

The sample-data package may include:

- T2W mouse MRI volumes
- Reference ground-truth lung and lesion annotations
- AI-generated lung predictions
- AI-generated lesion predictions
- Combined three-label AI segmentations
- Visual comparisons of reference and AI annotations

Ground-truth annotations and AI-generated annotations are stored separately. The AI-generated outputs demonstrate model behavior and reproducibility and must not be interpreted as additional ground truth.

## Model provenance reported by the Zenodo description

| Item | Value |
|---|---|
| Framework | nnU-Net v2 |
| Configuration | `3d_fullres` |
| Trainer/plans | `nnUNetTrainer__nnUNetPlans__3d_fullres` |
| Training fold | `all` |
| Checkpoint | `checkpoint_final.pth` |
| Lung dataset identifier | `Dataset101_mice_final` |
| Lung-lesion dataset identifier | `Dataset101_MRI-Lung` |
| Recommended Python | Python 3.10 or newer |
| Recommended device | CUDA-compatible GPU; CPU inference is also supported |

The original lung-model metadata names input channel 0 as `CT` and specifies `CTNormalization`. These training metadata are retained because nnU-Net uses the saved plans during inference. The public pipeline input is nevertheless a single T2W MRI volume named `*_0000.nii.gz`.

> **Note:** The table above mirrors the public Zenodo description. For the exact directory names, trainer/plans stored in a downloaded package, and released fold layout, use the metadata inside the authorized Zenodo archive as the source of truth. Repository packaging details are documented in [`MODEL_FOLDER_STRUCTURE.md`](../MODEL_FOLDER_STRUCTURE.md).

## Download and extract the restricted files

Because the Zenodo files are restricted, their exact filenames and checksums are not exposed by the unauthenticated public files endpoint. After access is granted, use the authorized metadata command in the main [`README.md`](../README.md) to retrieve the exact filename, checksum, and size of each file.

Extract downloaded model ZIP archives into this repository's existing `models/` directory:

```bash
ZENODO_ARCHIVE=/absolute/path/to/archive-name-from-zenodo.zip
unzip "$ZENODO_ARCHIVE" -d models
```

On Windows PowerShell:

```powershell
$ZenodoArchive = "C:\path\to\archive-name-from-zenodo.zip"
Expand-Archive -LiteralPath $ZenodoArchive -DestinationPath .\models
```

Do not create an extra nested `models/models/` directory. Keep the extracted package roots directly under `models/`.

After extraction, follow the package-specific inference instructions in the main [`README.md`](../README.md). See [`MODEL_FOLDER_STRUCTURE.md`](../MODEL_FOLDER_STRUCTURE.md) for the repository's documented extracted layout.

All downloaded and extracted model/data contents under this directory are ignored by Git. Only this instruction file is tracked.
