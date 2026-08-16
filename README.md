# fMRI Preprocessing & Quality Analysis Project

## 📋 Project Overview

This project focuses on preprocessing and quality analysis of fMRI data from a schizophrenia study investigating speech perception and auditory hallucinations.

### Dataset Information
- **Source**: OpenNeuro Dataset ds004302
- **Study**: "Brain correlates of speech perception in schizophrenia patients with and without auditory hallucinations"
- **Authors**: Soler-Vidal et al. (2022)
- **Publication**: PLoS ONE 17(12): e0276975
- **DOI**: https://doi.org/10.1371/journal.pone.0276975

### Study Design
- **Task**: Speech perception block design with 3 experimental conditions:
  1. Word lists
  2. Sentence lists
  3. Reversed speech
- **Control**: White noise (implicit baseline)
- **Note**: First 5 volumes (10 seconds) discarded before analysis

### Participants
- **Total Subjects**: 60 participants
- **Groups**:
  - HC (Healthy Controls): 25 subjects
  - AVH- (Schizophrenia without auditory hallucinations): 25 subjects
  - AVH+ (Schizophrenia with auditory hallucinations): 10 subjects
- **Demographics**: Age, sex, IQ, PSYRATS scores recorded

---

## 🗂️ Project Structure

```
fMRI/
├── ds004302-download/          # Raw BIDS dataset (9.1GB) [gitignored]
├── output/                     # fMRIPrep preprocessed data (111GB) [gitignored]
├── work/                       # fMRIPrep working directory (149GB) [gitignored]
├── logs/                       # Processing logs (17MB) [gitignored]
├── quality_analysis/           # Quality metrics outputs (32MB) [gitignored]
│
├── PreProcessMultiSub.sh       # Parallel fMRIPrep preprocessing script
├── fmri_quality_metrics.py     # Comprehensive quality metrics framework
├── faculty_requested_metrics.py # SNR, tSNR, PSNR, SSIM metrics
├── advanced_visualizations.py   # Publication-quality visualizations
├── monitor_progress.sh         # Real-time preprocessing monitor
├── run_quality_analysis.sh     # Quality analysis pipeline runner
├── test_fs.sh                  # FreeSurfer configuration test
│
├── requirements.txt            # Python dependencies
├── license.txt                 # FreeSurfer license
├── README.md                   # This file
└── PROJECT_STATUS.md           # Detailed status documentation
```

---

## 🔧 Core Components

### 1. Preprocessing Pipeline (`PreProcessMultiSub.sh`)

**Purpose**: Parallelized fMRIPrep preprocessing optimized for dual-socket NUMA architecture

**Key Features**:
- NUMA-aware processing (2 nodes, 32 threads each)
- Memory-optimized (300GB per node, 600GB total)
- Alternating subject assignment across NUMA nodes
- Automatic FreeSurfer setup
- Comprehensive logging

**Configuration**:
- Output spaces: MNI152NLin2009cAsym:res-2, anat
- No surface reconstruction (--fs-no-reconall)
- Random seed: 42 (reproducibility)
- Resource monitoring enabled

**Usage**:
```bash
./PreProcessMultiSub.sh
```

### 2. Quality Metrics Framework (`fmri_quality_metrics.py`)

**Purpose**: Comprehensive pre/post preprocessing quality assessment

**Metrics Computed**:
- Temporal Signal-to-Noise Ratio (tSNR)
- Signal-to-Noise Ratio (SNR)
- Motion parameters (FD, DVARS)
- Global signal correlation
- Artifact detection
- Spatial similarity indices

**Features**:
- Parallel processing (multiprocessing)
- Publication-quality visualizations
- Statistical analysis
- Before/after comparison

### 3. Faculty-Requested Metrics (`faculty_requested_metrics.py`)

**Purpose**: Specific quality metrics requested by research faculty

**Metrics**:
1. **SNR**: Signal-to-Noise Ratio (Air method)
   - Signal from brain tissue
   - Noise from air regions
   
2. **tSNR**: Temporal Signal-to-Noise Ratio
   - Voxel-wise computation
   - Mean/median/percentile summaries
   
3. **PSNR**: Peak Signal-to-Noise Ratio
   - After spatial alignment
   - Raw vs preprocessed comparison
   
4. **SSIM**: Structural Similarity Index
   - After spatial alignment
   - Perceptual quality metric

**Methodology**:
- Raw BOLD transformed to T1w space using fMRIPrep transforms
- Proper brain masking
- ANTs-based spatial alignment (if available)

### 4. Advanced Visualizations (`advanced_visualizations.py`)

**Purpose**: Publication-ready quality visualizations

**Outputs**:
- Multi-format exports (PNG 300/600 DPI, PDF, SVG)
- Statistical plots
- Comparison visualizations
- Quality control dashboards

---

## 📊 Current Status

### Preprocessing Status
- ✅ **Completed**: All 60 subjects preprocessed
- ✅ **Reports Generated**: 426 HTML quality control reports
- ✅ **Output Space**: MNI152NLin2009cAsym:res-2 + native anatomical
- ✅ **Logs**: Complete processing logs in `logs/`

### Quality Analysis Status
- ✅ **Framework**: Quality metrics framework implemented
- ✅ **Metrics**: SNR, tSNR, PSNR, SSIM computation ready
- ✅ **Visualizations**: Publication-quality plotting configured
- ⏳ **Execution**: Ready to run on preprocessed data

### Data Integrity
- ✅ **Backup**: Scripts backed up to `scripts_backup_20260816.zip`
- ✅ **Version Control**: Git repository initialized and pushed
- ✅ **Gitignore**: Comprehensive ignore rules for large data
- ✅ **Remote**: GitHub repository synced

---

## 🚀 Quick Start

### Prerequisites
- Python 3.8+ with scientific packages
- fMRIPrep installed
- FreeSurfer with valid license
- ANTs (optional, for alignment)

### Install Dependencies
```bash
pip install -r requirements.txt
```

### Run Quality Analysis
```bash
./run_quality_analysis.sh
```

### Monitor Preprocessing
```bash
./monitor_progress.sh
```

---

## 📈 Quality Metrics Details

### Signal-to-Noise Ratio (SNR)
- **Method**: Air method
- **Formula**: SNR = μ_signal / σ_noise
- **Signal**: Mean intensity in brain mask
- **Noise**: Standard deviation in air regions

### Temporal SNR (tSNR)
- **Formula**: tSNR = μ_time / σ_time
- **Computation**: Voxel-wise, then summarized
- **Summary**: Mean, median, percentiles (5th, 25th, 75th, 95th)

### Peak SNR (PSNR)
- **Formula**: PSNR = 20 * log10(MAX / √MSE)
- **MAX**: Maximum possible pixel value
- **MSE**: Mean squared error between aligned images

### Structural Similarity (SSIM)
- **Components**: Luminance, contrast, structure
- **Range**: [-1, 1] (1 = identical)
- **Application**: After spatial alignment

---

## 📝 Citation

If you use this dataset or preprocessing pipeline, please cite:

**Original Study**:
```
Soler-Vidal, J., Fuentes-Claramonte, P., Salgado-Pineda, P., Ramiro, N., 
García-León, M. Á., Torres, M. L., Arévalo, A., Guerrero-Pedraza, A., 
Munuera, J., Sarró, S., Salvador, R., Hinzen, W., McKenna, P., & 
Pomarol-Clotet, E. (2022). Brain correlates of speech perception in 
schizophrenia patients with and without auditory hallucinations. 
PloS one, 17(12), e0276975.
```

**fMRIPrep**:
```
Esteban, O., et al. (2019). fMRIPrep: a robust preprocessing pipeline for 
functional MRI. Nature Methods, 16(1), 111-116.
```

---

## 📧 Contact

- **GitHub Repository**: https://github.com/csyphor/pjt1
- **Dataset Source**: https://openneuro.org/datasets/ds004302

---

## 📜 License

- **Code**: See `license.txt`
- **Data**: OpenNeuro ds004302 (CC0 license)
- **FreeSurfer**: Separate license required

---

## 🔍 Additional Resources

- [fMRIPrep Documentation](https://fmriprep.readthedocs.io/)
- [BIDS Specification](https://bids-specification.readthedocs.io/)
- [OpenNeuro Dataset](https://openneuro.org/datasets/ds004302)
- [Original Paper](https://doi.org/10.1371/journal.pone.0276975)
