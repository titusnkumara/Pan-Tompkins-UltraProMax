# Pan‑Tompkins UltraProMax – Sparse Min‑Max R‑Peak Detection

This repository contains the code and evaluation scripts for the paper:

> **"Pan‑Tompkins UltraProMax: Sparse Min‑Max Sampling Enables 10× Faster, 38× Compressed, and More Accurate R‑Peak Detection"**

The MATLAB scripts reproduce all frequency‑sweep results (MIT‑BIH, QTDB, NSTDB) and the 9+7 bit compression + LZ4 experiments. Python scripts download the required databases from PhysioNet.


> **Note**: The MATLAB scripts are **self‑contained** – they include the complete Pan‑Tompkins implementation and all helper functions.

## Prerequisites

### Python (for data download)
- Python 3.7+
- Install required packages:
  ```bash
  pip install wfdb numpy requests
### MATLAB (for all experiments)
MATLAB R2020b or newer (older versions may work but are not tested)
Signal Processing Toolbox (required for filtfilt, butter, resample, findpeaks)
### Optional (for compression script only)
LZ4 command‑line tool (used for extra compression of packed sparse data)
macOS: brew install lz4
Linux: sudo apt install lz4 (or yum install lz4)
Windows: download from lz4.github.io and add to PATH

## Running Instructions

Follow these steps to download the databases, run the frequency‑sweep experiments, and test the compression pipeline.
1. Clone the repository
2. Download the ECG databases
   Run each Python script once. They will create folders mitdb-mat/, qtdb-mat/, nstdb-mat/.
    python downloadMITBIH.py
    python downloadQT.py
    python downloadNSTDB.py
3. Run the frequency sweep, and compression mlx files.  Select the database as your requirement within the code. 

## Performance Evaluation of Sparse Min‑Max Pan‑Tompkins

### Native Baseline (Standard Pan‑Tompkins)

| Database | Sampling Rate | F1 (%) | Sensitivity (%) | Precision (%) | Timing Jitter (ms) | CPU Time (s) |
|----------|---------------|--------|----------------|---------------|--------------------|---------------|
| MIT‑BIH  | 360 Hz        | 99.19  | 99.17           | 99.20         | -2.62 ± 12.10      | 26.28         |
| QTDB     | 250 Hz        | 99.52  | 99.52           | 99.51         | 8.94 ± 28.59       | 15.45         |
| NSTDB    | 360 Hz        | 87.38  | 92.48           | 82.81         | -0.69 ± 26.19      | 6.62          |

---

### MIT‑BIH Arrhythmia Database (MITDB) – Sparse Min‑Max Pipeline

| Effective Rate (Hz) | Block Size | F1 (%) | Sens (%) | Prec (%) | Timing Jitter (ms) | Total Time (s) |
|---------------------|------------|--------|----------|----------|--------------------|----------------|
| 180                 | 4          | 99.14  | 99.08    | 99.20    | -2.25 ± 12.10      | 24.69          |
| 120                 | 6          | 99.20  | 99.14    | 99.26    | -1.98 ± 12.28      | 21.12          |
| 90                  | 8          | 99.23  | 99.16    | 99.30    | -1.67 ± 12.30      | 18.33          |
| 72                  | 10         | 99.37  | 99.49    | 99.26    | -1.37 ± 12.34      | 16.09          |
| 60                  | 12         | 99.25  | 99.12    | 99.39    | -1.31 ± 11.97      | 13.69          |
| 45                  | 16         | 99.20  | 99.12    | 99.29    | -1.25 ± 12.16      | 10.16          |
| 36                  | 20         | 99.40  | 99.55    | 99.25    | -0.51 ± 12.45      | 9.46           |
| 30                  | 24         | 99.44  | 99.61    | 99.26    | -0.71 ± 11.30      | 7.37           |
| 24                  | 30         | 99.53  | 99.57    | 99.49    | -0.71 ± 11.29      | 5.96           |
| **18** (optimal)    | 40         | **99.62** | 99.67 | 99.56    | -0.74 ± 11.32      | **4.61**       |
| 15                  | 48         | 99.54  | 99.63    | 99.44    | -0.99 ± 11.29      | 4.36           |
| 12                  | 60         | 99.45  | 99.57    | 99.34    | -0.92 ± 11.32      | 3.39           |
| **10** (native‑beating) | 72     | **99.40** | 99.26 | 99.53    | -0.90 ± 11.14      | **2.58**       |
| 9                   | 80         | 98.89  | 98.45    | 99.33    | -1.37 ± 10.31      | 2.97           |

> Native baseline (raw 360 Hz): F1 = 99.19%, time = 26.28 s  
> → At 18 Hz: **+0.43 pp F1**, **5.7× faster**  
> → At 10 Hz: **+0.21 pp F1**, **10.2× faster**

---

### QT Database (QTDB) – Sparse Min‑Max Pipeline

| Effective Rate (Hz) | Block Size | F1 (%) | Sens (%) | Prec (%) | Timing Jitter (ms) | Total Time (s) |
|---------------------|------------|--------|----------|----------|--------------------|----------------|
| 120                 | 4          | 99.51  | 99.45    | 99.56    | 9.34 ± 28.28       | 13.46          |
| 80                  | 6          | 99.54  | 99.53    | 99.54    | 9.73 ± 28.37       | 11.00          |
| 60                  | 8          | 99.57  | 99.56    | 99.57    | 10.28 ± 27.86      | 9.36           |
| 48                  | 10         | 99.68  | 99.59    | 99.77    | 10.98 ± 28.13      | 7.20           |
| 40                  | 12         | 99.66  | 99.71    | 99.60    | 10.87 ± 27.29      | 5.78           |
| 30                  | 16         | 99.61  | 99.77    | 99.44    | 10.22 ± 26.73      | 4.43           |
| **24** (optimal)    | 20         | **99.71** | 99.76 | 99.67    | 10.37 ± 26.49      | **3.96**       |
| **20** (optimal)    | 24         | **99.71** | 99.78 | 99.65    | 10.36 ± 26.49      | **3.61**       |
| 16                  | 30         | 99.59  | 99.54    | 99.65    | 10.17 ± 26.16      | 3.11           |
| 12                  | 40         | 99.63  | 99.51    | 99.76    | 10.30 ± 26.13      | 2.30           |
| **10**              | 48         | **99.55** | 99.36 | 99.74    | 10.29 ± 26.12      | **1.78**       |
| 8                   | 60         | 98.40  | 97.05    | 99.79    | 9.02 ± 25.37       | 1.72           |
| 6                   | 80         | 97.70  | 96.96    | 98.45    | 9.12 ± 25.29       | 1.49           |

> Native baseline (raw 250 Hz): F1 = 99.52%, time = 15.45 s  
> → At 20–24 Hz: **+0.19 pp F1**, **4.3× faster**  
> → At 10 Hz: **+0.03 pp F1**, **8.7× faster**

---

### Noise Stress Test Database (NSTDB) – Sparse Min‑Max Pipeline

| Effective Rate (Hz) | Block Size | F1 (%) | Sens (%) | Prec (%) | Timing Jitter (ms) | Total Time (s) |
|---------------------|------------|--------|----------|----------|--------------------|----------------|
| 180                 | 4          | 87.46  | 92.50    | 82.94    | -0.05 ± 26.04      | 6.72           |
| 120                 | 6          | 87.68  | 92.79    | 83.10    | 0.42 ± 25.87       | 5.67           |
| 90                  | 8          | 87.85  | 92.92    | 83.30    | 1.25 ± 25.63       | 4.96           |
| 72                  | 10         | 87.76  | 93.08    | 83.02    | 1.68 ± 26.05       | 4.33           |
| 60                  | 12         | 88.18  | 93.03    | 83.82    | 1.23 ± 25.51       | 3.91           |
| **45** (optimal)    | 16         | **88.77** | 93.61 | 84.40    | 1.31 ± 25.16       | **2.71**       |
| 36                  | 20         | 88.37  | 93.36    | 83.88    | 1.83 ± 24.93       | 2.50           |
| 30                  | 24         | 87.99  | 92.02    | 84.30    | 2.00 ± 23.60       | 1.74           |
| 24                  | 30         | 88.27  | 91.64    | 85.14    | 1.96 ± 23.41       | 1.33           |
| 18                  | 40         | 88.04  | 91.00    | 85.26    | 2.00 ± 23.66       | 0.98           |
| 15                  | 48         | 88.20  | 91.56    | 85.07    | 1.80 ± 23.25       | 0.91           |
| 12                  | 60         | 88.30  | 91.47    | 85.34    | 1.69 ± 23.12       | 0.74           |
| **10**              | 72         | **88.50** | 89.73 | 87.29    | 1.85 ± 22.59       | **0.62**       |
| 9                   | 80         | 88.49  | 89.97    | 87.06    | 1.52 ± 21.76       | 0.72           |

> Native baseline (raw 360 Hz): F1 = 87.38%, time = 6.62 s  
> → At 45 Hz (optimal F1): **+1.39 pp F1**, **2.4× faster**  
> → At 10 Hz (best precision): **+1.12 pp F1**, **10.7× faster**

---

### Compression Performance (MITDB, 10 Hz Sparse + 9+7 Packing)

| Representation                         | Size (MB) | Compression Ratio | % of Original |
|----------------------------------------|-----------|-------------------|----------------|
| Original ECG (16‑bit, 360 Hz)          | 59.51     | 1.00 : 1          | 100%           |
| Packed sparse (uncompressed)           | 1.65      | 36.0 : 1          | 2.8%           |
| LZ4 compressed sparse ECG              | 1.57      | 37.9 : 1          | 2.6%           |
| Original annotations (int32)           | 0.42      | –                 | –              |
| LZ4 compressed annotations             | 0.42      | 1.0 : 1           | 100%           |
| Combined ECG + annotations (LZ4)       | 1.99      | 30.2 : 1          | 3.3%           |
| Final tar.gz (gzip -9)                 | 1.67      | 35.8 : 1          | 2.8%           |

> After compression + decompression, the 10 Hz sparse signal achieves F1 = **99.31%** (uncompressed 10 Hz sparse gives 99.40%).
