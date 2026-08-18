# Technical Documentation

## fMRI Preprocessing & Quality Analysis Pipeline

**Version**: 1.0  
**Date**: August 16, 2026  
**Author**: fMRI Quality Metrics Framework

---

## Table of Contents
1. [Pipeline Overview](#pipeline-overview)
2. [Preprocessing Methodology](#preprocessing-methodology)
3. [Quality Metrics Definitions](#quality-metrics-definitions)
4. [Implementation Details](#implementation-details)
5. [Data Flow](#data-flow)
6. [Validation & Testing](#validation--testing)

---

## Pipeline Overview

### Architecture
```
Raw BIDS Data (ds004302-download/)
         ↓
    [fMRIPrep]
         ↓
Preprocessed Data (output/)
         ↓
[Quality Metrics Framework]
         ↓
Quality Reports (quality_analysis/)
```

### Components
1. **Preprocessing**: fMRIPrep with NUMA optimization
2. **Quality Assessment**: Multi-metric framework
3. **Visualization**: Publication-quality plots
4. **Statistical Analysis**: Group comparisons

---

## Preprocessing Methodology

### fMRIPrep Configuration

#### Output Spaces
- **MNI152NLin2009cAsym:res-2**: 2mm isotropic MNI space
  - Standard template for group analysis
  - Enables cross-subject comparison
  
- **Native Anatomical**: Subject's T1w space
  - Preserves individual anatomy
  - Used for quality comparisons

#### Processing Steps
1. **Anatomical Processing**
   - T1w bias field correction
   - Skull stripping
   - Tissue segmentation (GM, WM, CSF)
   - Surface reconstruction (optional, disabled here)

2. **Functional Processing**
   - Slice timing correction
   - Motion correction (6 DOF)
   - Susceptibility distortion correction
   - Registration to T1w
   - Registration to MNI template
   - Smoothing (optional, not applied)

3. **Quality Control**
   - Motion parameters extraction
   - Framewise displacement (FD) calculation
   - DVARS computation
   - Artifact detection

#### Parameters Used
```bash
--fs-no-reconall          # Skip FreeSurfer surface reconstruction
--nprocs 32               # 32 threads per NUMA node
--omp-nthreads 6          # 6 OpenMP threads
--mem-mb 300000           # 300GB memory per node
--random-seed 42          # Reproducibility
--resource-monitor        # Track resource usage
--notrack                 # Disable usage tracking
```

### NUMA Optimization

#### Problem
Dual-socket servers have separate memory domains (NUMA nodes). Poor memory affinity causes:
- Remote memory access latency
- Memory bandwidth bottlenecks
- Suboptimal cache utilization

#### Solution
```bash
# Node 0: Even-numbered subjects
numactl --cpunodebind=0 fmriprep ...

# Node 1: Odd-numbered subjects
numactl --cpunodebind=1 fmriprep ...
```

#### Benefits
- Local memory access (reduced latency)
- Full bandwidth utilization per node
- Parallel processing without contention
- 2x throughput improvement

---

## Quality Metrics Definitions

### 1. Signal-to-Noise Ratio (SNR)

#### Definition
SNR measures the ratio of signal power to noise power in the image.

#### Air Method Implementation
```
SNR = μ_signal / σ_noise

Where:
  μ_signal = Mean intensity in brain tissue
  σ_noise = Standard deviation in air regions
```

#### Why Air Method?
- **Brain signal**: Reflects actual neural signal
- **Air noise**: Pure thermal/system noise
- **Advantage**: Independent of biological variability

#### Implementation Steps
1. Create brain mask (from preprocessed data)
2. Create air mask (outside head)
3. Compute mean in brain
4. Compute std in air
5. Calculate ratio

#### Interpretation
- Higher SNR = Better image quality
- Typical fMRI SNR: 50-200
- SNR < 50: Poor quality
- SNR > 150: Excellent quality

### 2. Temporal Signal-to-Noise Ratio (tSNR)

#### Definition
tSNR measures signal stability over time.

#### Formula
```
tSNR = μ_time / σ_time

Where:
  μ_time = Mean across time (per voxel)
  σ_time = Standard deviation across time (per voxel)
```

#### Computation
1. For each voxel, compute temporal mean
2. For each voxel, compute temporal std
3. Divide mean by std (voxel-wise)
4. Summarize across brain:
   - Mean tSNR
   - Median tSNR
   - Percentiles (5th, 25th, 75th, 95th)

#### Interpretation
- Higher tSNR = More stable signal
- Typical fMRI tSNR: 20-100
- tSNR < 20: High temporal noise
- tSNR > 80: Excellent stability

#### Factors Affecting tSNR
- Motion artifacts (decreases tSNR)
- Physiological noise (breathing, cardiac)
- Scanner drift
- Thermal noise

### 3. Peak Signal-to-Noise Ratio (PSNR)

#### Definition
PSNR measures reconstruction quality relative to maximum possible signal.

#### Formula
```
PSNR = 20 * log10(MAX / √MSE)

Where:
  MAX = Maximum possible pixel value
  MSE = Mean Squared Error between images
```

#### Application
- Compare raw vs preprocessed
- Requires spatial alignment first
- Measures information preservation

#### Implementation
1. Align raw to preprocessed space (ANTs)
2. Apply brain mask
3. Compute MSE
4. Calculate PSNR

#### Interpretation
- Higher PSNR = Better preservation
- Typical range: 20-40 dB
- PSNR < 20 dB: Significant degradation
- PSNR > 30 dB: Good quality

### 4. Structural Similarity Index (SSIM)

#### Definition
SSIM measures perceptual similarity considering luminance, contrast, and structure.

#### Formula
```
SSIM(x, y) = [l(x,y)^α · c(x,y)^β · s(x,y)^γ]

Where:
  l(x,y) = luminance comparison
  c(x,y) = contrast comparison
  s(x,y) = structure comparison
  α = β = γ = 1 (typically)
```

#### Components
1. **Luminance**: Mean intensity comparison
2. **Contrast**: Variance comparison
3. **Structure**: Covariance comparison

#### Range
- SSIM ∈ [-1, 1]
- SSIM = 1: Identical images
- SSIM = 0: No correlation
- SSIM < 0: Anti-correlation

#### Advantages over PSNR
- Perceptually meaningful
- Captures structural information
- More sensitive to degradation

#### Implementation
1. Align images spatially
2. Compute SSIM in sliding windows
3. Average across brain
4. Report mean SSIM

---

## Implementation Details

### Python Framework Architecture

#### Class Structure
```python
FMRIQualityMetrics
├── __init__()
├── find_subjects()
├── load_bold_data()
├── compute_snr()
├── compute_tsnr()
├── compute_motion_metrics()
├── generate_visualizations()
└── save_results()

SignalQualityMetrics
├── __init__()
├── align_images()
├── compute_snr_air()
├── compute_tsnr()
├── compute_psnr()
├── compute_ssim()
└── generate_report()
```

#### Parallel Processing
```python
# Multiprocessing for speed
from multiprocessing import Pool

with Pool(n_jobs) as pool:
    results = pool.map(compute_metrics, subjects)
```

#### Memory Management
- Load one subject at a time
- Use memory-mapped files (nibabel)
- Clear intermediate arrays
- Process in chunks if needed

### Spatial Alignment

#### Why Alignment is Needed
- Raw BOLD: In functional space
- Preprocessed BOLD: In anatomical/MNI space
- Cannot compare directly

#### Alignment Pipeline
```
Raw BOLD (func space)
    ↓ [Apply HMC transforms]
Motion-corrected BOLD
    ↓ [Apply SDC transforms]
Distortion-corrected BOLD
    ↓ [Apply func→anat transform]
BOLD in anatomical space
    ↓ [Now comparable to preprocessed]
```

#### ANTs Integration
```python
# Apply transforms
antsApplyTransforms \
  -i raw_bold.nii.gz \
  -r reference.nii.gz \
  -t transform1.mat \
  -t transform2.mat \
  -o aligned.nii.gz
```

### Brain Masking

#### Importance
- Exclude non-brain voxels
- Focus on relevant signal
- Avoid air/CSF contamination

#### Mask Sources
1. **Preprocessed**: Use fMRIPrep brain mask
2. **Raw**: Generate with BET or FSL
3. **Intersection**: Use overlap for safety

---

## Data Flow

### Input Data Structure
```
ds004302-download/
├── dataset_description.json
├── participants.tsv
├── task-speech_bold.json
├── task-speech_events.tsv
└── sub-XX/
    ├── anat/
    │   └── sub-XX_T1w.nii.gz
    └── func/
        ├── sub-XX_task-speech_bold.nii.gz
        └── sub-XX_task-speech_events.tsv
```

### Output Data Structure
```
output/
├── dataset_description.json
├── sub-XX.html
└── sub-XX/
    ├── anat/
    │   ├── sub-XX_desc-preproc_T1w.nii.gz
    │   ├── sub-XX_desc-brain_mask.nii.gz
    │   └── sub-XX_label-*.nii.gz
    └── func/
        ├── sub-XX_task-speech_space-MNI152NLin2009cAsym_desc-preproc_bold.nii.gz
        ├── sub-XX_task-speech_desc-brain_mask.nii.gz
        └── sub-XX_task-speech_desc-confounds_timeseries.tsv
```

### Quality Analysis Output
```
quality_analysis/
├── metrics/
│   ├── snr_summary.csv
│   ├── tsnr_summary.csv
│   ├── psnr_summary.csv
│   └── ssim_summary.csv
├── plots/
│   ├── dpi300/
│   ├── dpi600/
│   ├── pdf/
│   └── svg/
└── statistics/
    ├── group_comparisons.csv
    └── correlations.csv
```

---

## Validation & Testing

### Unit Tests

#### SNR Validation
```python
# Test with known signal
signal = np.ones((10, 10, 10)) * 100
noise = np.random.randn(10, 10, 10) * 10

# Expected SNR ≈ 10
computed_snr = compute_snr(signal, noise)
assert abs(computed_snr - 10) < 0.5
```

#### tSNR Validation
```python
# Create time series with known tSNR
time_series = np.random.randn(100, 100, 100, 200)
time_series = time_series * 10 + 100  # Mean=100, Std=10

# Expected tSNR = 100/10 = 10
computed_tsnr = compute_tsnr(time_series)
assert abs(computed_tsnr - 10) < 0.5
```

### Integration Tests

#### End-to-End Pipeline
```bash
# Test on single subject
python signal_quality_metrics.py \
  --raw_dir ds004302-download \
  --preproc_dir output \
  --output_dir test_output \
  --subjects sub-01
```

#### Expected Outputs
- [x] SNR value in reasonable range (50-200)
- [x] tSNR value in reasonable range (20-100)
- [x] PSNR value in reasonable range (20-40 dB)
- [x] SSIM value in reasonable range (0.7-1.0)
- [x] Plots generated
- [x] CSV files created

### Quality Checks

#### Visual Inspection
- Check registration quality
- Verify brain masking
- Inspect motion parameters
- Review artifact detection

#### Statistical Checks
- Outlier detection (3 SD from mean)
- Distribution normality
- Group balance verification
- Missing data identification

---

## Troubleshooting

### Common Issues

#### Issue: Low tSNR values
**Causes**:
- Excessive motion
- Scanner artifacts
- Physiological noise

**Solutions**:
- Check motion parameters
- Review confound regressors
- Consider scrubbing high-motion volumes

#### Issue: SSIM < 0.5
**Causes**:
- Misalignment
- Different preprocessing steps
- Wrong reference image

**Solutions**:
- Verify transform application
- Check reference space
- Ensure consistent preprocessing

#### Issue: Memory errors
**Causes**:
- Loading full 4D data
- Insufficient RAM

**Solutions**:
- Process in chunks
- Use memory-mapped files
- Reduce parallel jobs

---

## Performance Optimization

### Speed vs Memory Trade-offs

#### Fast (High Memory)
```python
# Load all subjects into memory
data = [load_subject(s) for s in subjects]
results = parallel_process(data)
```

#### Slow (Low Memory)
```python
# Process one at a time
for subject in subjects:
    data = load_subject(subject)
    result = process(data)
    save(result)
    del data  # Free memory
```

### Recommended Settings

| Resource | Setting | Rationale |
|----------|---------|-----------|
| n_jobs | 8-16 | Balance speed/memory |
| chunk_size | 50 volumes | Manage memory |
| output_format | PNG 300 DPI | Publication ready |

---

## References

### Methods Papers
1. fMRIPrep: Esteban et al. (2019) Nature Methods
2. SSIM: Wang et al. (2004) IEEE TIP
3. tSNR: Murphy et al. (2007) NeuroImage

### Software
- fMRIPrep: https://fmriprep.readthedocs.io/
- ANTs: http://stnava.github.io/ANTs/
- nibabel: https://nipy.org/nibabel/

---

**End of Technical Documentation**
