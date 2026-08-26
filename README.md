# Mouse T2W MRI Lung Lesion Segmentation

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.22050734.svg)](https://doi.org/10.5281/zenodo.22050734)

A two-stage nnU-Net v2 pipeline for segmenting the lungs and lung lesions in mouse T2-weighted (T2W) MRI.

> **Research use only:** This software and the accompanying models are not medical devices and have not been validated for clinical diagnosis or treatment.

## Pipeline

```text
T2W MRI (*_0000.nii.gz)
        |----------------------|
        v                      v
  Lung nnU-Net          Lesion nnU-Net
        |                      |
        |------ merge ---------|
                  v
       0 background / 1 lung / 2 lesion
```

The lung and lesion models receive the same input MRI. The merge step retains predicted lesion voxels only within the predicted lung mask.

## Repository contents

The GitHub repository contains source code and documentation. Trained weights, extracted model folders, example MRI volumes, and generated predictions are excluded from Git.

```text
.
|-- models/
|   `-- README.md
|-- scripts/
|   |-- merge_masks.py
|   |-- total_tumor_burden.py
|   |-- package_inference_lung.sh
|   |-- package_inference_lesion_single.sh
|   `-- package_inference_lesion_5fold.sh
|-- CITATION.cff
|-- LICENSE
|-- MODEL_FOLDER_STRUCTURE.md
|-- requirements.txt
`-- README.md
```

## Installation

Python 3.10 or newer is recommended. Install PyTorch for the CUDA version available on your system first, then install the remaining dependencies.

```bash
git clone https://github.com/HFahmida/MouseLungLesionSeg.git
cd MouseLungLesionSeg

python -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

On Windows PowerShell, activate the environment with:

```powershell
.venv\Scripts\Activate.ps1
```

See the official [nnU-Net installation guide](https://github.com/MIC-DKFZ/nnUNet/blob/master/documentation/getting-started/installation-and-setup.md) and [inference guide](https://github.com/MIC-DKFZ/nnUNet/blob/master/documentation/how-to/run-inference.md).

## Access the trained models on Zenodo

The trained model files are hosted on Zenodo because the checkpoints are too large for GitHub. The published V1 record is titled **MouseLungLesionSeg v1.0: A Two-Staged AI Approach for Lung Lesion Segmentation on MRI for Mice Tumor Models**.

> **Version-specific record:** [https://zenodo.org/records/22050734](https://zenodo.org/records/22050734)  
> **Version-specific DOI:** [10.5281/zenodo.22050734](https://doi.org/10.5281/zenodo.22050734)  
> **Concept DOI:** [10.5281/zenodo.22050733](https://doi.org/10.5281/zenodo.22050733)  
> **Published:** August 21, 2026  
> **File access:** Restricted  
> **Model license:** [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)

The record metadata are public, but the uploaded files are restricted. Open the Zenodo record while signed in to request or use granted access. The record owner can also share access with a Zenodo user or provide a secret viewing link.

### Retrieve the exact Zenodo filenames and checksums

Because the files are restricted, an unauthenticated API response does not expose their filenames or checksums. After access has been granted, the following command queries the dedicated Zenodo files endpoint and prints every exact filename, checksum, and file size. Set `ZENODO_ACCESS_TOKEN` to a personal token that has access to the record. Do not commit or share the token.

```bash
export ZENODO_ACCESS_TOKEN="replace-with-your-private-token"

python - <<'PY'
import json
import os
import urllib.request

# Read the restricted file inventory using an authorized Zenodo token.
record_id = "22050734"
token = os.environ.get("ZENODO_ACCESS_TOKEN")
if not token:
    raise SystemExit("Set ZENODO_ACCESS_TOKEN before running this command.")

request = urllib.request.Request(
    f"https://zenodo.org/api/records/{record_id}/files",
    headers={
        "Accept": "application/json",
        "Authorization": f"Bearer {token}",
        "User-Agent": "MouseLungLesionSeg/1.0",
    },
)
with urllib.request.urlopen(request, timeout=60) as response:
    payload = json.load(response)

# Current Zenodo records generally return an `entries` mapping. The fallback
# keeps the command compatible with list-based responses.
entries = payload.get("entries", payload.get("files", payload))
if isinstance(entries, dict):
    files = [dict(value, key=value.get("key", key)) for key, value in entries.items()]
elif isinstance(entries, list):
    files = entries
else:
    raise SystemExit("Zenodo returned an unexpected file-metadata format.")

if not files:
    raise SystemExit("No files were returned. Confirm that the token can view this restricted record.")

for item in files:
    filename = item.get("key") or item.get("filename") or item.get("id") or "unknown"
    checksum = item.get("checksum", "not provided")
    if isinstance(checksum, dict):
        checksum = checksum.get("md5") or checksum.get("sha256") or str(checksum)
    size = item.get("size", item.get("filesize", "not provided"))
    print(f"{filename}\t{checksum}\t{size} bytes")
PY
```

You can also copy the exact filenames and checksums from the **Files** section after opening the record with authorized access.

To verify a downloaded file against the MD5 value reported by Zenodo:

```bash
md5sum /path/to/downloaded-file
```

On Windows PowerShell:

```powershell
Get-FileHash -Algorithm MD5 C:\path\to\downloaded-file
```

### Extract the downloaded models

Extract every downloaded ZIP archive into this repository's existing `models/` directory. Set the archive path to the exact filename printed by the command above.

Linux or macOS:

```bash
ZENODO_ARCHIVE=/absolute/path/to/archive-name-from-zenodo.zip
unzip "$ZENODO_ARCHIVE" -d models
```

Windows PowerShell:

```powershell
$ZenodoArchive = "C:\path\to\archive-name-from-zenodo.zip"
Expand-Archive -LiteralPath $ZenodoArchive -DestinationPath .\models
```

After extraction, the three package directories must sit directly under `models/`. There should not be a nested `models/models/` directory.

```text
models/
|-- README.md
|-- mouse_lung_model/
|-- mouse_lung_lesion_resenc_fold_all/
`-- mouse_lung_lesion_5fold/
```

The packages correspond to:

| Package | Dataset | Trainer and plans | Fold selection |
|---|---|---|---|
| `mouse_lung_model` | `Dataset101_mice_final` | `nnUNetTrainer__nnUNetPlans__3d_fullres` | `all` |
| `mouse_lung_lesion_resenc_fold_all` | `Dataset101_MRI-Lung` | `nnUNetTrainer__nnUNetResEncUNetMPlans__3d_fullres` | `all` |
| `mouse_lung_lesion_5fold` | `Dataset103_MRI-Lung` | `nnUNetTrainer__nnUNetPlans__3d_fullres` | `0 1 2 3 4` |

See [`MODEL_FOLDER_STRUCTURE.md`](MODEL_FOLDER_STRUCTURE.md) for the complete extracted layout. Do not commit the extracted folders. The repository's `.gitignore` excludes everything under `models/` except `models/README.md`.

## Input format

Each input is a single-channel NIfTI image that follows nnU-Net naming conventions.

```text
imagesTs/
|-- mouse001_0000.nii.gz
|-- mouse002_0000.nii.gz
`-- mouse003_0000.nii.gz
```

Case identifiers must be unique, and input files must end in `_0000.nii.gz`.

## Run lung inference

Always provide `INPUT_DIR` and `OUTPUT_DIR`. The package-local inference script configures `nnUNet_raw`, `nnUNet_preprocessed`, and `nnUNet_results` from its own location.

```bash
INPUT_DIR=/absolute/path/to/imagesTs \
OUTPUT_DIR=/absolute/path/to/lung_predictions \
DEVICE=cuda \
bash models/mouse_lung_model/inference_nnUNet.sh
```

On a SLURM cluster:

```bash
INPUT_DIR=/absolute/path/to/imagesTs \
OUTPUT_DIR=/absolute/path/to/lung_predictions \
DEVICE=cuda \
sbatch --export=ALL models/mouse_lung_model/inference_nnUNet.sh
```

## Run lesion inference

Select one of the two lesion packages. The ResEnc package uses the `fold_all` checkpoint. The five-fold package ensembles folds 0 through 4.

### ResEnc fold-all lesion model

```bash
INPUT_DIR=/absolute/path/to/imagesTs \
OUTPUT_DIR=/absolute/path/to/lesion_predictions \
DEVICE=cuda \
bash models/mouse_lung_lesion_resenc_fold_all/inference_nnUNet.sh
```

On a SLURM cluster:

```bash
INPUT_DIR=/absolute/path/to/imagesTs \
OUTPUT_DIR=/absolute/path/to/lesion_predictions \
DEVICE=cuda \
sbatch --export=ALL models/mouse_lung_lesion_resenc_fold_all/inference_nnUNet.sh
```

### Five-fold lesion model

```bash
INPUT_DIR=/absolute/path/to/imagesTs \
OUTPUT_DIR=/absolute/path/to/lesion_predictions_5fold \
DEVICE=cuda \
bash models/mouse_lung_lesion_5fold/inference_nnUNet.sh
```

On a SLURM cluster:

```bash
INPUT_DIR=/absolute/path/to/imagesTs \
OUTPUT_DIR=/absolute/path/to/lesion_predictions_5fold \
DEVICE=cuda \
sbatch --export=ALL models/mouse_lung_lesion_5fold/inference_nnUNet.sh
```

Use `DEVICE=cpu` for CPU inference. GPU inference is recommended.

## Merge lung and lesion predictions

Run the merge utility with the lung output and the output from the selected lesion model.

```bash
python scripts/merge_masks.py \
  --images /absolute/path/to/imagesTs \
  --lung-masks /absolute/path/to/lung_predictions \
  --lesion-masks /absolute/path/to/lesion_predictions \
  --output /absolute/path/to/combined_predictions
```

The combined masks use these labels:

| Value | Meaning |
|---:|---|
| 0 | Background |
| 1 | Lung without predicted lesion |
| 2 | Predicted lesion inside the predicted lung |

If prediction geometry differs from the input image, the merge utility resamples labels into the input image's physical space using nearest-neighbor interpolation.

## Calculate tumor burden

The current tumor-burden utility uses editable path variables rather than command-line arguments. Open [`scripts/total_tumor_burden.py`](scripts/total_tumor_burden.py) and set these values near the bottom of the file:

```python
# Directory where the two Excel workbooks will be written.
stats_path = "/absolute/path/to/statistics"

# Directory containing AI-generated NIfTI masks.
ai_pred_path = "/absolute/path/to/combined_predictions"

# Directory containing ground-truth NIfTI masks.
gt_path = "/absolute/path/to/ground_truth"
```

Then run:

```bash
python scripts/total_tumor_burden.py
```

The script writes:

- `Total_lung-lesion_volume_AI.xlsx`
- `Total_lung-lesion_volume_GT.xlsx`

The required Excel and connected-component packages are included in `requirements.txt`. The tumor-burden implementation itself is retained unchanged in this release.

## Preserved lung-model metadata

The original lung-model nnU-Net metadata names channel 0 `CT`, stores the internal dataset name as `Dataset101_mice`, and specifies `CTNormalization`. These training metadata are retained unchanged because nnU-Net uses the saved plans during inference. The public pipeline input remains a single T2W MRI named `*_0000.nii.gz`.

## License

The source code and documentation in this GitHub repository are licensed under the [MIT License](LICENSE).

The trained model files in Zenodo record `22050734` are licensed under [Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/) and have restricted file access. The GitHub MIT license does not replace the model license, Zenodo access conditions, or licenses of third-party dependencies.

## Citation

The associated paper has not yet been published. `CITATION.cff` is intentionally retained as a placeholder and will be completed after publication.

Until then, please cite the [Zenodo model record](https://zenodo.org/records/22050734) using the version-specific DOI [`10.5281/zenodo.22050734`](https://doi.org/10.5281/zenodo.22050734), this GitHub repository, and [nnU-Net](https://github.com/MIC-DKFZ/nnUNet). Use the concept DOI [`10.5281/zenodo.22050733`](https://doi.org/10.5281/zenodo.22050733) when referring to the model collection across versions.
