# VentrEX

Official implementation of **VentrEX: An Anatomically Guided Deep Learning Pipeline for Ventricular Segmentation in Cine Cardiac MRI**.

VentrEX combines:

1. **VentrEX-Seg**, a 3D residual encoder–decoder with parallel channel-spatial attention and a Transformer bottleneck for joint end-diastolic and end-systolic segmentation.
2. **PM/T standardization**, a post-segmentation stage that harmonizes the assignment of papillary muscles and trabeculae at the ventricular blood-pool boundary.

## Repository Contents

```text
VentrEX/
├── notebooks/
│   ├── 01_VentrEX_Seg_Training_and_Evaluation.ipynb
│   ├── 02_PM_T_Threshold_Regressor_Training.ipynb
│   └── 03_VentrEX_Complete_Inference_Demo.ipynb
├── src/
│   ├── ventrex_seg.py
│   ├── preprocessing.py
│   ├── pmt_standardization.py
│   └── metrics.py
├── configs/
│   └── inference_config.yaml
├── weights/
│   └── README.md
├── requirements.txt
├── LICENSE
└── CITATION.cff
```

## Notebook Description

* `01_VentrEX_Seg_Training_and_Evaluation.ipynb` contains the VentrEX-Seg architecture, training procedure, checkpoint loading, and segmentation evaluation.
* `02_PM_T_Threshold_Regressor_Training.ipynb` trains and exports the gradient-boosting regressor used by the PM/T standardization stage.
* `03_VentrEX_Complete_Inference_Demo.ipynb` demonstrates the complete inference pipeline, from ED/ES image preprocessing to VentrEX-Seg prediction and PM/T-standardized output generation.

## Installation

Clone the repository:

```bash
git clone https://github.com/ablabedoui1/VENTREX.git
cd VentrEX
```

Create a Python environment and install the dependencies:

```bash
python -m venv ventrex-env
source ventrex-env/bin/activate
pip install -r requirements.txt
```

On Windows, activate the environment using:

```bash
ventrex-env\Scripts\activate
```

## Pretrained Models

The following pretrained artifacts are required for complete inference:

* `ventrex_seg_checkpoint.pth.tar`: VentrEX-Seg neural-network checkpoint.
* `pmt_threshold_gbr.joblib`: gradient-boosting model for adaptive PM/T threshold prediction.

.

## Input Preparation

The pipeline expects end-diastolic and end-systolic cine-CMR volumes in NIfTI format.

For each subject:

1. The ED and ES frames are extracted.
2. Each frame is center-cropped to eight short-axis slices with an in-plane size of (144\times144) pixels.
3. Volumes containing fewer than eight slices are zero-padded.
4. Intensities are standardized using z-score normalization.
5. The ED and ES frames are concatenated along the through-plane dimension.

The resulting network input has the shape:

```text
batch × 1 × 16 × 144 × 144
```

## Running the Complete Pipeline

Open:

```text
notebooks/03_VentrEX_Complete_Inference_Demo.ipynb
```

Set the following paths in the configuration cell:

```python
ED_IMAGE_PATH = "path/to/subject_ED.nii.gz"
ES_IMAGE_PATH = "path/to/subject_ES.nii.gz"
SEGMENTATION_CHECKPOINT = "weights/ventrex_seg_checkpoint.pth.tar"
PMT_REGRESSOR_PATH = "weights/pmt_threshold_gbr.joblib"
OUTPUT_DIRECTORY = "outputs/"
```

Run all notebook cells sequentially. The notebook generates:

* Direct VentrEX-Seg segmentation masks.
* PM/T-standardized VentrEX masks.
* Representative visualizations.
* NIfTI output files for subsequent volumetric analysis.

## Evaluation Metrics

The supplied evaluation functions implement:

* Dice similarity coefficient.
* 95th-percentile Hausdorff distance.
* Patient-level ventricular volume calculations.
* Bland–Altman agreement analysis.

## Data Availability

The imaging datasets are not redistributed in this repository. Users must obtain them from their respective providers and comply with their licensing and access requirements.

The ACDC dataset is available from the [official ACDC Challenge](https://www.creatis.insa-lyon.fr/Challenge/acdc/). Sunnybrook Cardiac Data are available through the [Cardiac Atlas Project](https://www.cardiacatlas.org/sunnybrook-cardiac-data/).

Study-specific cardiologist-validated PM/T annotations are not included because they were created for the present study and contain derived research data. The pretrained PM/T regressor and inference implementation are provided to support application of the standardization procedure.

## Reproducibility

Random seeds and preprocessing parameters are specified in the notebooks and configuration file. The model checkpoints correspond to the implementations used for the experiments reported in the manuscript.

## Citation

If you use this implementation, please cite:

```bibtex
@article{bedoui2026ventrex,
  title   = {VentrEX: An Anatomically Guided Deep Learning Pipeline for Ventricular Segmentation in Cine Cardiac MRI},
  author  = {Bedoui, Abla and Rancati, Julieta Anahí and Lugones, Ignacio and Cherkaoui, Mohammed},
  journal = {Journal of Imaging},
  year    = {2026}
}
```

The final volume, issue, page range, and DOI will be added after publication.

## Contact

For questions concerning the implementation, please contact:

**Abla Bedoui**
Long Island University
[abla.bedoui@liu.edu](mailto:abla.bedoui@liu.edu)
