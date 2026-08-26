# Published nnU-Net model-folder structure

The restricted-access [Zenodo V1 record](https://zenodo.org/records/22050734) contains the model release represented by three self-contained nnU-Net package directories. Each package has its own nnU-Net environment directories and an inference script at the package root.

## Common package layout

```text
MODEL_PACKAGE/
|-- inference_nnUNet.sh
|-- inference_nnUNet_original.sh
|-- nnUNet_raw/
|   `-- DatasetXXX_Name/
|       |-- dataset.json
|       |-- imagesTe/
|       `-- labelsTe/
|-- nnUNet_preprocessed/
`-- nnUNet_results/
    `-- DatasetXXX_Name/
        `-- TRAINER__PLANS__3d_fullres/
            |-- dataset.json
            |-- dataset_fingerprint.json
            |-- plans.json
            `-- fold_NAME/
                `-- checkpoint_final.pth
```

The published packages retain the metadata needed by nnU-Net and distribute `checkpoint_final.pth` for each released fold.

## Extracted layout under this repository

After downloading and extracting the Zenodo files, the package roots must sit directly under `models/`.

```text
models/
|-- README.md
|-- mouse_lung_model/
|   |-- inference_nnUNet.sh
|   |-- inference_nnUNet_original.sh
|   |-- nnUNet_raw/
|   |   `-- Dataset101_mice_final/
|   |       |-- dataset.json
|   |       |-- imagesTe/
|   |       `-- labelsTe/
|   |-- nnUNet_preprocessed/
|   `-- nnUNet_results/
|       `-- Dataset101_mice_final/
|           `-- nnUNetTrainer__nnUNetPlans__3d_fullres/
|               |-- dataset.json
|               |-- dataset_fingerprint.json
|               |-- plans.json
|               `-- fold_all/
|                   `-- checkpoint_final.pth
|-- mouse_lung_lesion_resenc_fold_all/
|   |-- inference_nnUNet.sh
|   |-- inference_nnUNet_original.sh
|   |-- nnUNet_raw/
|   |   `-- Dataset101_MRI-Lung/
|   |       |-- dataset.json
|   |       |-- imagesTe/
|   |       `-- labelsTe/
|   |-- nnUNet_preprocessed/
|   `-- nnUNet_results/
|       `-- Dataset101_MRI-Lung/
|           `-- nnUNetTrainer__nnUNetResEncUNetMPlans__3d_fullres/
|               |-- dataset.json
|               |-- dataset_fingerprint.json
|               |-- plans.json
|               `-- fold_all/
|                   `-- checkpoint_final.pth
`-- mouse_lung_lesion_5fold/
    |-- inference_nnUNet.sh
    |-- inference_nnUNet_original.sh
    |-- nnUNet_raw/
    |   `-- Dataset103_MRI-Lung/
    |       |-- dataset.json
    |       |-- imagesTe/
    |       `-- labelsTe/
    |-- nnUNet_preprocessed/
    `-- nnUNet_results/
        `-- Dataset103_MRI-Lung/
            `-- nnUNetTrainer__nnUNetPlans__3d_fullres/
                |-- dataset.json
                |-- dataset_fingerprint.json
                |-- plans.json
                |-- fold_0/
                |   `-- checkpoint_final.pth
                |-- fold_1/
                |   `-- checkpoint_final.pth
                |-- fold_2/
                |   `-- checkpoint_final.pth
                |-- fold_3/
                |   `-- checkpoint_final.pth
                `-- fold_4/
                    `-- checkpoint_final.pth
```

Do not create an additional `models/models/` directory. The commands in the main README assume the three package roots shown above.

## Package provenance

| Package | Dataset | Trainer | Plans | Configuration | Folds |
|---|---|---|---|---|---|
| `mouse_lung_model` | `Dataset101_mice_final` | `nnUNetTrainer` | `nnUNetPlans` | `3d_fullres` | `all` |
| `mouse_lung_lesion_resenc_fold_all` | `Dataset101_MRI-Lung` | `nnUNetTrainer` | `nnUNetResEncUNetMPlans` | `3d_fullres` | `all` |
| `mouse_lung_lesion_5fold` | `Dataset103_MRI-Lung` | `nnUNetTrainer` | `nnUNetPlans` | `3d_fullres` | `0, 1, 2, 3, 4` |

## Run a package

Provide an input directory containing single-channel NIfTI images named `CASE_0000.nii.gz`.

```bash
INPUT_DIR=/path/to/imagesTs \
OUTPUT_DIR=/path/to/predictions \
DEVICE=cuda \
bash /path/to/MODEL_PACKAGE/inference_nnUNet.sh
```

On a SLURM cluster:

```bash
INPUT_DIR=/path/to/imagesTs \
OUTPUT_DIR=/path/to/predictions \
DEVICE=cuda \
sbatch --export=ALL /path/to/MODEL_PACKAGE/inference_nnUNet.sh
```

The package-local script computes `nnUNet_raw`, `nnUNet_preprocessed`, and `nnUNet_results` from its own location. `inference_nnUNet_original.sh` is retained only as a record of the original cluster command and may contain site-specific paths. Use `inference_nnUNet.sh` for the distributable package.

## Zenodo filenames and checksums

The exact uploaded archive names and checksums are protected by the Zenodo record's restricted file access and are not returned by the unauthenticated public API. The main [`README.md`](README.md) includes an authorized Python command that retrieves the current exact filename, checksum, and size for every file after access is granted.
