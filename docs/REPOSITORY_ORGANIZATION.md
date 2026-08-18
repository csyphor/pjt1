# Repository Organization Summary

**Date**: August 18, 2026  
**Status**: ✅ Reorganized and Consistent

---

## 📋 Organization Changes

### Directory Structure Created

The repository has been reorganized into a clean, logical structure:

```
fMRI/
├── scripts/                    # All executable scripts (NEW)
│   ├── python/                 # Python analysis scripts (NEW)
│   │   ├── fmri_quality_metrics.py
│   │   ├── signal_quality_metrics.py
│   │   └── advanced_visualizations.py
│   └── shell/                 # Shell scripts (NEW)
│       ├── PreProcessMultiSub.sh
│       ├── run_quality_analysis.sh
│       ├── monitor_progress.sh
│       └── test_fs.sh
│
├── docs/                       # Documentation (NEW)
│   ├── PROJECT_STATUS.md
│   ├── PROJECT_STATUS_UPDATE.md
│   ├── RENAMING_SUMMARY.md
│   ├── REPOSITORY_ORGANIZATION.md
│   ├── STATUS_CHECK.md
│   └── TECHNICAL_DOCS.md
│
├── logs/                       # Processing logs
│   ├── fmriprep_numa0.log
│   ├── fmriprep_numa1.log
│   └── signal_quality_metrics.log
│
├── ds004302-download/          # Raw BIDS data [gitignored]
│   ├── .datalad/
│   ├── .gitattributes
│   ├── CHANGES
│   ├── README
│   ├── dataset_description.json
│   ├── participants.json
│   ├── participants.tsv
│   ├── task-speech_bold.json
│   ├── task-speech_events.json
│   ├── task-speech_events.tsv
│   ├── sub-01/                 # Example subject (similar for all 71 subjects)
│   │   ├── anat/
│   │   │   └── sub-01_T1w.nii.gz
│   │   └── func/
│   │       └── sub-01_task-speech_bold.nii.gz
│   ├── sub-02/                 # Example subject 2
│   │   ├── anat/
│   │   │   └── sub-02_T1w.nii.gz
│   │   └── func/
│   │       └── sub-02_task-speech_bold.nii.gz
│   └── sub-03/ through sub-77/ # Remaining 69 subjects (same structure)
│
├── output/                     # fMRIPrep preprocessed data [gitignored]
│   ├── .bidsignore
│   ├── README.md
│   ├── dataset_description.json
│   ├── logs/
│   ├── sub-01.html             # Quality control HTML report
│   ├── sub-01/                 # Example subject (similar for all 71 subjects)
│   │   ├── anat/               # Anatomical preprocessing outputs
│   │   │   ├── sub-01_desc-brain_mask.json
│   │   │   ├── sub-01_desc-brain_mask.nii.gz
│   │   │   ├── sub-01_desc-preproc_T1w.json
│   │   │   ├── sub-01_desc-preproc_T1w.nii.gz
│   │   │   ├── sub-01_dseg.nii.gz
│   │   │   ├── sub-01_from-MNI152NLin2009cAsym_to-T1w_mode-image_xfm.h5
│   │   │   ├── sub-01_from-T1w_to-MNI152NLin2009cAsym_mode-image_xfm.h5
│   │   │   ├── sub-01_label-CSF_probseg.nii.gz
│   │   │   ├── sub-01_label-GM_probseg.nii.gz
│   │   │   ├── sub-01_label-WM_probseg.nii.gz
│   │   │   ├── sub-01_space-MNI152NLin2009cAsym_res-2_desc-brain_mask.json
│   │   │   ├── sub-01_space-MNI152NLin2009cAsym_res-2_desc-brain_mask.nii.gz
│   │   │   ├── sub-01_space-MNI152NLin2009cAsym_res-2_desc-preproc_T1w.json
│   │   │   ├── sub-01_space-MNI152NLin2009cAsym_res-2_desc-preproc_T1w.nii.gz
│   │   │   ├── sub-01_space-MNI152NLin2009cAsym_res-2_dseg.json
│   │   │   ├── sub-01_space-MNI152NLin2009cAsym_res-2_dseg.nii.gz
│   │   │   ├── sub-01_space-MNI152NLin2009cAsym_res-2_label-CSF_probseg.nii.gz
│   │   │   ├── sub-01_space-MNI152NLin2009cAsym_res-2_label-GM_probseg.nii.gz
│   │   │   └── sub-01_space-MNI152NLin2009cAsym_res-2_label-WM_probseg.nii.gz
│   │   ├── func/               # Functional preprocessing outputs
│   │   │   ├── sub-01_task-speech_desc-brain_mask.json
│   │   │   ├── sub-01_task-speech_desc-brain_mask.nii.gz
│   │   │   ├── sub-01_task-speech_desc-confounds_timeseries.json
│   │   │   ├── sub-01_task-speech_desc-confounds_timeseries.tsv
│   │   │   ├── sub-01_task-speech_desc-coreg_boldref.json
│   │   │   ├── sub-01_task-speech_desc-coreg_boldref.nii.gz
│   │   │   ├── sub-01_task-speech_desc-hmc_boldref.json
│   │   │   ├── sub-01_task-speech_desc-hmc_boldref.nii.gz
│   │   │   ├── sub-01_task-speech_from-boldref_to-T1w_mode-image_desc-coreg_xfm.json
│   │   │   ├── sub-01_task-speech_from-boldref_to-T1w_mode-image_desc-coreg_xfm.txt
│   │   │   ├── sub-01_task-speech_from-orig_to-boldref_mode-image_desc-hmc_xfm.json
│   │   │   ├── sub-01_task-speech_from-orig_to-boldref_mode-image_desc-hmc_xfm.txt
│   │   │   ├── sub-01_task-speech_space-MNI152NLin2009cAsym_res-2_boldref.json
│   │   │   ├── sub-01_task-speech_space-MNI152NLin2009cAsym_res-2_boldref.nii.gz
│   │   │   ├── sub-01_task-speech_space-MNI152NLin2009cAsym_res-2_desc-brain_mask.json
│   │   │   ├── sub-01_task-speech_space-MNI152NLin2009cAsym_res-2_desc-brain_mask.nii.gz
│   │   │   ├── sub-01_task-speech_space-MNI152NLin2009cAsym_res-2_desc-preproc_bold.json
│   │   │   ├── sub-01_task-speech_space-MNI152NLin2009cAsym_res-2_desc-preproc_bold.nii.gz
│   │   │   ├── sub-01_task-speech_space-T1w_boldref.json
│   │   │   ├── sub-01_task-speech_space-T1w_boldref.nii.gz
│   │   │   ├── sub-01_task-speech_space-T1w_desc-brain_mask.json
│   │   │   ├── sub-01_task-speech_space-T1w_desc-brain_mask.nii.gz
│   │   │   ├── sub-01_task-speech_space-T1w_desc-preproc_bold.json
│   │   │   └── sub-01_task-speech_space-T1w_desc-preproc_bold.nii.gz
│   │   ├── figures/            # Quality control visualizations
│   │   │   ├── sub-01_desc-about_T1w.html
│   │   │   ├── sub-01_desc-conform_T1w.html
│   │   │   ├── sub-01_desc-summary_T1w.html
│   │   │   ├── sub-01_dseg.svg
│   │   │   ├── sub-01_space-MNI152NLin2009cAsym_T1w.svg
│   │   │   ├── sub-01_task-speech_desc-carpetplot_bold.svg
│   │   │   ├── sub-01_task-speech_desc-compcorvar_bold.svg
│   │   │   ├── sub-01_task-speech_desc-confoundcorr_bold.svg
│   │   │   ├── sub-01_task-speech_desc-coreg_bold.svg
│   │   │   ├── sub-01_task-speech_desc-rois_bold.svg
│   │   │   ├── sub-01_task-speech_desc-summary_bold.html
│   │   │   └── sub-01_task-speech_desc-validation_bold.html
│   │   └── log/                # Processing logs
│   │       └── 20260801-194934_9ace174b-6f77-415c-b853-59994a4fada2/
│   ├── sub-02.html             # Subject 02 QC report
│   ├── sub-02/                 # Subject 02 (same structure as sub-01)
│   │   ├── anat/               # Same files as sub-01 (with sub-02 prefix)
│   │   ├── func/               # Same files as sub-01 (with sub-02 prefix)
│   │   ├── figures/            # Same files as sub-01 (with sub-02 prefix)
│   │   └── log/                # Processing logs
│   └── sub-03/ through sub-77/ # Remaining 69 subjects (same structure)
│
├── work/                       # fMRIPrep working directory [gitignored]
│   ├── numa0/                  # NUMA node 0 processing
│   └── numa1/                  # NUMA node 1 processing
│
├── quality_analysis/           # Quality metrics outputs [gitignored]
│   ├── metrics/
│   │   └── quality_metrics.csv
│   ├── plots/
│   │   ├── dpi300/            # 300 DPI publication plots
│   │   │   ├── bar_chart_comparison.png
│   │   │   ├── bland_altman_plots.png
│   │   │   ├── cdf_plots.png
│   │   │   ├── correlation_heatmap.png
│   │   │   ├── global_mean_boxplot.png
│   │   │   ├── global_mean_violin.png
│   │   │   ├── line_chart_progression.png
│   │   │   ├── mean_signal_boxplot.png
│   │   │   ├── mean_signal_violin.png
│   │   │   ├── metrics_density.png
│   │   │   ├── metrics_histograms.png
│   │   │   ├── motion_summary.png
│   │   │   ├── motion_trace.png
│   │   │   ├── pairwise_correlation_significance.png
│   │   │   ├── qq_plots.png
│   │   │   ├── radar_chart_comparison.png
│   │   │   ├── raw_vs_preproc_scatter.png
│   │   │   ├── similarity_distributions.png
│   │   │   ├── snr_boxplot.png
│   │   │   ├── snr_violin.png
│   │   │   ├── subject_improvement.png
│   │   │   ├── temporal_metrics.png
│   │   │   ├── tsnr_boxplot.png
│   │   │   ├── tsnr_violin.png
│   │   │   └── violin_boxplot_combined.png
│   │   ├── dpi600/            # 600 DPI publication plots (same files)
│   │   ├── pdf/               # PDF format plots (same files)
│   │   └── svg/               # SVG format plots (same files)
│   ├── signal_quality/         # Signal quality metrics outputs
│   │   ├── final_summary.json
│   │   ├── signal_quality_metrics.csv
│   │   ├── sub-01_metrics.json
│   │   ├── sub-02_metrics.json
│   │   ├── sub-03_metrics.json
│   │   ├── ... (71 individual subject JSON files)
│   │   ├── sub-77_metrics.json
│   │   └── summary_statistics.json
│   └── statistics/
│       ├── comparison_statistics.csv
│       └── descriptive_statistics.csv
│
├── requirements.txt            # Python dependencies
├── license.txt                # FreeSurfer license
├── .gitignore                 # Git ignore rules
└── README.md                  # Project overview
```

---

## ✅ Changes Made

### 1. Script Organization
- **Created** `scripts/` directory with subdirectories:
  - `scripts/python/` - All Python analysis scripts
  - `scripts/shell/` - All shell/bash scripts
- **Moved** 3 Python scripts to `scripts/python/`
- **Moved** 4 shell scripts to `scripts/shell/`

### 2. Documentation Organization
- **Created** `docs/` directory
- **Moved** `PROJECT_STATUS.md` to `docs/`
- **Moved** `TECHNICAL_DOCS.md` to `docs/`
- **Created** this organization summary

### 3. Log Organization
- **Moved** `signal_quality_metrics.log` to `logs/` directory
- All logs now consolidated in single location

### 4. Cleanup
- **Removed** unnecessary files:
  - `node_modules/` directory
  - `package.json` and `package-lock.json`
  - `.python-version`
  - `.orchestra-skills.json`

### 5. Path Updates
- **Updated** all script paths in `README.md`
- **Updated** all script paths in `docs/PROJECT_STATUS.md`
- **Updated** `scripts/shell/run_quality_analysis.sh` to use relative paths
- **Updated** `scripts/shell/monitor_progress.sh` to use relative paths

---

## 🔍 Consistency Checks

### ✅ Documentation Consistency
- All file paths in documentation now reflect new structure
- Script usage examples updated with correct paths
- Project structure diagram updated in README.md

### ✅ Script Consistency
- Shell scripts use relative paths (portable)
- Python scripts remain unchanged (no hardcoded paths)
- All scripts executable and functional

### ✅ Git Consistency
- `.gitignore` properly excludes large data directories
- All source code and documentation tracked
- No temporary or build files in repository

### ✅ File Organization
- No loose scripts in root directory
- Clear separation of concerns (scripts vs docs vs data)
- Logical grouping by file type and purpose

---

## 📊 File Inventory

### Tracked in Git (Source Code & Docs)
| Category | Files | Location |
|----------|-------|----------|
| Python Scripts | 3 | `scripts/python/` |
| Shell Scripts | 4 | `scripts/shell/` |
| Documentation | 4 | `docs/` and root |
| Configuration | 2 | Root (`.gitignore`, `requirements.txt`) |
| License | 1 | Root (`license.txt`) |
| **Total** | **14** | |

### Gitignored (Large Data)
| Directory | Size | Contents |
|-----------|------|----------|
| `ds004302-download/` | 9.1 GB | Raw BIDS data |
| `output/` | 111 GB | Preprocessed data |
| `work/` | 149 GB | fMRIPrep working directory |
| `quality_analysis/` | 32 MB | Quality metrics outputs |
| `logs/` | ~17 MB | Processing logs |
| **Total** | **~270 GB** | |

---

## 🎯 Benefits of New Structure

### 1. **Improved Organization**
- Clear separation of scripts, documentation, and data
- Easy to navigate and understand project structure
- Professional repository layout

### 2. **Better Maintainability**
- Scripts grouped by type (Python vs Shell)
- Documentation centralized in `docs/`
- Easier to find and update files

### 3. **Enhanced Portability**
- Shell scripts use relative paths
- No hardcoded absolute paths
- Works across different environments

### 4. **Cleaner Root Directory**
- Only essential files in root
- No clutter from loose scripts
- Professional appearance

### 5. **Better Version Control**
- Clear distinction between tracked and ignored files
- Easier to review changes
- Reduced repository size

---

## 📝 Usage After Reorganization

### Running Scripts
```bash
# Quality analysis
./scripts/shell/run_quality_analysis.sh

# Monitor progress
./scripts/shell/monitor_progress.sh

# Preprocessing (if needed again)
./scripts/shell/PreProcessMultiSub.sh
```

### Viewing Documentation
```bash
# Project status
cat docs/PROJECT_STATUS.md

# Technical documentation
cat docs/TECHNICAL_DOCS.md

# Organization summary
cat docs/REPOSITORY_ORGANIZATION.md
```

### Checking Logs
```bash
# All logs in one place
ls logs/

# Monitor quality analysis
tail -f logs/signal_quality_metrics.log
```

---

## ✅ Verification Checklist

- [x] All Python scripts moved to `scripts/python/`
- [x] All shell scripts moved to `scripts/shell/`
- [x] All documentation moved to `docs/`
- [x] All logs consolidated in `logs/`
- [x] Unnecessary files removed
- [x] README.md updated with new paths
- [x] PROJECT_STATUS.md updated with new paths
- [x] Shell scripts updated with relative paths
- [x] .gitignore properly configured
- [x] No broken references or paths
- [x] Repository structure is logical and clean

---

## 🔧 Future Maintenance

### Adding New Scripts
- Python scripts → `scripts/python/`
- Shell scripts → `scripts/shell/`
- Update README.md with new script information

### Adding New Documentation
- Technical docs → `docs/`
- Update README.md references as needed

### Path References
- Use relative paths in scripts
- Use `$(dirname "${BASH_SOURCE[0]}")` for script location
- Use `$PROJECT_ROOT` variable for project root

---

## 📌 Summary

The repository has been successfully reorganized with:
- ✅ Clear directory structure
- ✅ Consistent file organization
- ✅ Updated documentation
- ✅ Portable scripts
- ✅ Professional layout
- ✅ No broken references

The repository is now well-organized, maintainable, and follows best practices for scientific computing projects.
