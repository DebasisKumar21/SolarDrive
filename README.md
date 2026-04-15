# SolarDrive: A High-Contrast Glare Dataset and Diagnostic Probe for Advancing Autonomous Perception

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Paper](https://img.shields.io/badge/Paper-IEEE%20T--ITS-blue)](https://doi.org/10.1109/TIV.2026.XXXXXXX)
[![Dataset](https://img.shields.io/badge/Dataset-Google%20Drive-green)](https://drive.google.com/file/d/1Zb89PAYrhCZmrSrJnLzwz9rLKLSohSSa/view?usp=sharing)


> **Debasis Kumar, Md. Jahidul Alam, Tambet Matiisen, Naveed Muhammad**  
> Institute of Computer Science, University of Tartu, Estonia  
> IEEE Transactions on Intelligent Vehicles, 2026

---

## Overview

Standard autonomous driving datasets are predominantly captured under favorable lighting, creating a texture bias in perception models that leaves solar glare systematically underrepresented. **SolarDrive** is a curated Multi-Object Tracking (MOT) dataset captured in Tartu, Estonia, designed as a targeted stress-test that isolates the photometric failure modes of global shutter sensors at extreme solar elevation angles (< 15°).

**Key findings:**
- Extreme solar glare suppresses **pedestrian detection to 30.6% mAP@50** — a safety-critical gap of 53 points versus buses (83.5%)
- Our **Radiometric Information Loss (RIL)** metric reveals that geometric glare obliterates an average of **30.90%** of usable spatial gradients
- Severe stroboscopic oscillations peak at **3.20 Hz**, systematically breaking temporal tracking
- End-to-End Transformers (MeMOTR, MOTRv2) **collapse completely** under native-resolution spatial saturation
- Motion-compensated tracking (BoT-SORT) reduces Identity Switches by **52%** versus pure IoU association (ByteTrack)

---

## Dataset

### Download

The full dataset including raw stereo frames, MOTChallenge-formatted annotations, and pre-computed COCO-MOT JSON is available on Google Drive:

**[⬇ Download SolarDrive Dataset](https://drive.google.com/file/d/1Zb89PAYrhCZmrSrJnLzwz9rLKLSohSSa/view?usp=sharing)**

```
SolarDrive_dataset.zip   — left-camera images  (images/<seq>/left_camera/*.jpg)
SolarDrive_labels.zip    — YOLO-format labels   (labels/<seq>/left_camera/*.txt)
```


### Sequences

| Sequence | Frames | Boxes | Density | RIL | Photometric Character |
|---|---|---|---|---|---|
| sun_glare_0 | 836 | 3,668 | 4.39 obj/frame | 28.59% | Direct solar bloom + bimodal shadow crushing, 3.20 Hz flicker |
| sun_glare_1 | 247 | 1,699 | 6.88 obj/frame | 30.86% | Sky saturation, open road, negligible shadow crushing |
| sun_glare_2 | 323 | 1,035 | 3.20 obj/frame | 27.74% | Bridge specular reflections, mirage gradients, 2.22 Hz |
| sun_glare_3 | 1,046 | 4,194 | 4.01 obj/frame | 36.41% | Maximum RIL, sustained canopy stroboscopic flicker at 2.20 Hz |
| **Total** | **2,452** | **10,596** | **4.32 obj/frame** | **30.90%** | |

### Annotations

- **Label format:** `class_id  track_id  cx  cy  w  h` (6-column, YOLO-normalised)
- **Classes:** Pedestrian (0), Cyclist (1), Car (2), Motorcycle (3), Bus (4), Truck (5)
- **Class distribution:** Car 9,093 (85.8%), Bus 676 (6.4%), Truck 554 (5.2%), Pedestrian 245 (2.3%), Cyclist+Motorcycle 28 (0.3%)
  > *Note: In the raw annotations, Cyclist (27 instances) and Motorcycle (1 instance) are separate classes. The manuscript reports them jointly as "Cyclists/Motorcycles" (28 instances, 0.3%) due to their shared kinematic profiles and low combined frequency.*
- **Sensor resolution:** 2064 × 1544 px, 30 Hz synchronized stereo capture
- **Right-camera frames:** 2,390 unannotated (provided for stereo research)
- **License:** [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)

### Photometric Degradation (Table II)

| Sequence | Highlight Saturation (≥245) | Crushed Shadows (≤9) | Total RIL |
|---|---|---|---|
| sun_glare_0 | 26.18% | 2.41% | 28.59% |
| sun_glare_1 | 30.22% | 0.64% | 30.86% |
| sun_glare_2 | 26.98% | 0.76% | 27.74% |
| sun_glare_3 | 35.22% | 1.19% | 36.41% |
| **Average (Simple)** | **29.65%** | **1.25%** | **30.90%** |
| **Average (Weighted)** | **30.55%** | **1.49%** | **32.04%** |

---

## Repository Structure

```
SolarDrive/
│
├── README.md
│
├── notebooks/
│   ├── solardrive_dataset_characterisation.ipynb   # RIL · Table II · Table III · COCO-MOT conversion
│   ├── solardrive_detection_benchmark.ipynb        # Table IV · CLAHE ablation · Pedestrian stratification
│   ├── solardrive_tbd_tracking_benchmark.ipynb     # Table V — BoT-SORT · ByteTrack
│   ├── solardrive_e2e_tracking_benchmark.ipynb     # Table V — MeMOTR · MOTRv2
│   └── solardrive_figures.ipynb                   # Figures 1–7

```

---

## Notebooks

All notebooks run on **Google Colab (T4 GPU)**. Each is self-contained: it mounts your Google Drive, unzips the dataset, and saves all outputs back to Drive.

**Before running any notebook:**
1. Upload `SolarDrive_dataset.zip` and `SolarDrive_labels.zip` to `MyDrive/` in Google Drive
2. Open the notebook in Colab: `File → Open notebook → GitHub → paste this repo URL`
3. Set runtime: `Runtime → Change runtime type → T4 GPU`
4. Run cells top to bottom in order

### 1. Dataset Characterisation
`notebooks/solardrive_dataset_characterisation.ipynb`

Reproduces all dataset-level metrics. No GPU required.

| Cell | Output | Paper |
|---|---|---|
| 3 | RIL per sequence + average | Table II |
| 4 | Frames, boxes, density, class breakdown | Table III |
| 5 | COCO-MOT JSON for MOTRv2 / MeMOTR | Section IV.B |
| 6 | Saves `RIL_results.txt` + `dataset_statistics.txt` to Drive | — |

### 2. Detection Benchmark
`notebooks/solardrive_detection_benchmark.ipynb`

Evaluates 4 detection models at native 2064px resolution. Runtime ~60–80 min on T4.

| Cell | Output | Paper |
|---|---|---|
| 7 | YOLOv8x + YOLO11x + RT-DETR-L/X at imgsz=2064 | Table IV (main rows) |
| 8 | RT-DETR-L/X re-evaluated at imgsz=640 | Table IV (640 rows) |
| 9 | CLAHE preprocessing ablation (YOLOv8x) | Section V.B footnote |
| 10 | Pedestrian size stratification S/M/L (CNN-averaged) | Section V.D |
| 11 | Scale effect — YOLOv8/YOLO11 full family | Section V.B footnote |
| 12 | Print all tables | — |
| 13 | Save JSON + text to Drive | — |

### 3. TbD Tracking Benchmark
`notebooks/solardrive_tbd_tracking_benchmark.ipynb`

Runs BoT-SORT and ByteTrack using YOLO11x detections. Runtime ~90–120 min on T4.

| Cell | Output | Paper |
|---|---|---|
| 9 | BoT-SORT tracking across all 4 sequences | Table V |
| 10 | ByteTrack tracking across all 4 sequences | Table V |
| 12–13 | TrackEval (HOTA, MOTA, IDF1) for both trackers | Table V |
| 14 | Save metrics + raw tracker files to Drive | — |

### 4. End-to-End Transformer Tracking Benchmark
`notebooks/solardrive_e2e_tracking_benchmark.ipynb`

Runs MeMOTR and MOTRv2. Requires CUDA compilation (~3 min per model). Runtime ~3–4 hours total on T4.

| Cell | Output | Paper |
|---|---|---|
| 3 | Clone + compile MeMOTR CUDA ops | — |
| 5 | Clone + compile MOTRv2 CUDA ops | — |
| 10 | MeMOTR inference on all sequences | Table V |
| 12 | TrackEval for MeMOTR | Table V |
| 14–15 | YOLO proposals + MOTRv2 inference | Table V |
| 18 | TrackEval for MOTRv2 | Table V |
| 19 | Save all results to Drive | — |

> **Note on checkpoints:** MeMOTR uses the DanceTrack checkpoint (`memotr_dancetrack.pth`, ~603 MB, gdown ID `1MPZJfP91Pb1ThnX5dvxZ7tcjDH8t9hew`). MOTRv2 uses the DanceTrack checkpoint (`motrv2.pth`, ~168 MB, gdown ID `1EA4lndu2yQcVgBKR09KfMe5efbf631Th`). Both are downloaded automatically by their respective cells, or can be placed in `MyDrive/` to skip the download.

### 5. Figures
`notebooks/solardrive_figures.ipynb`

Generates all 7 paper figures. Requires T4 GPU for Figures 4 and 5 (YOLO inference).

| Cell | Figure | Description |
|---|---|---|
| 4 | Fig. 1 | Geographical map of Tartu routes (interactive HTML via OSRM) |
| 5 | Fig. 2 | Dataset diversity grid (2×4, algorithmic + manual selection) |
| 6 | Fig. 3 | Logarithmic pixel intensity histograms |
| 7 | Fig. 4 | YOLO detection confidence vs frame brightness |
| 8 | Fig. 5 | Transient blindness — object loss and recovery (YOLO11x) |
| 9 | Fig. 6 | FFT temporal stability (stroboscopic flicker analysis) |
| 10 | Fig. 7 | Stroboscopic flicker frame triplets (sun_glare_3) |

---

## Results

### Detection Evaluation — mAP@50 (Table IV)

*Protocol: zero-shot COCO pretrained weights · conf=0.25 · IoU=0.50 · min\_height≥50px · imgsz=2064px native*

| Model | Arch | sg0 (28.59% RIL) | sg1 (30.86% RIL) | sg2 (27.74% RIL) | sg3 (36.41% RIL) | Mean |
|---|---|---|---|---|---|---|
| YOLOv8x | CNN | 51.5% | 47.0% | 31.2% | 41.1% | **42.7%** |
| YOLO11x | CNN | 53.0% | 49.9% | 30.5% | 39.9% | **43.3%** |
| RT-DETR-L (640×640) | Transformer | 55.5% | 53.3% | 32.8% | 47.7% | **47.3%** |
| RT-DETR-X (640×640) | Transformer | 56.7% | 55.2% | 34.4% | 46.0% | **48.1%** |
| RT-DETR-L (Native 2064px) | Transformer | 6.5% | 6.0% | 3.4% | 3.2% | **4.8%** |
| RT-DETR-X (Native 2064px) | Transformer | 0.3% | 0.4% | 0.9% | 0.4% | **0.5%** |

### MOT Evaluation — Combined Results (Table V)

*Protocol: zero-shot · TbD detector: YOLO11x · conf≥0.50 · min\_height≥50px · GT = 10,596 targets*

| Tracker | HOTA ↑ | MOTA ↑ | IDF1 ↑ | IDS ↓ | Dets |
|---|---|---|---|---|---|
| BoT-SORT (TbD + ReID) | **60.02 ±8.7** | **54.04** | **66.88** | **348** | 9,690 |
| ByteTrack (TbD IoU) | 42.72 ±8.8 | 43.49 | 50.30 | 732 | 8,634 |
| MOTRv2 (E2E Transformer) | 14.36 ±5.9 | 5.03 | 13.00 | 65 | 1,360 |
| MeMOTR (E2E Transformer) | 5.60 ±2.9 | −4.85 | 1.67 | 17 | 751 |

---

## RIL Metric

The **Radiometric Information Loss (RIL)** metric quantifies photometric severity by measuring the fraction of pixels that carry zero gradient information:

$$\text{RIL} = \left(\frac{1}{H \times W} \sum_{i=1}^{H} \sum_{j=1}^{W} \mathbb{1}(P_{ij} \leq 9 \;\vee\; P_{ij} \geq 245)\right) \times 100$$

The thresholds ≤9 (crushed shadows) and ≥245 (blown highlights) isolate the physical saturation bounds of the Sony IMX265 sensor (72.7 dB dynamic range, 10,600e⁻ full-well capacity). RIL exhibits a strong positive rank correlation with ByteTrack Identity Switches (Spearman ρ = 0.80), validating it as a proxy for geometric tracking fragmentation.

---

## Sensor and Acquisition

- **Vehicle:** Lexus RX450h testbed, Autonomous Driving Lab, University of Tartu
- **Cameras:** Allied Vision Mako G-319C (×2 stereo), Sony IMX265 CMOS Global Shutter
- **Resolution:** 2064 × 1544 px
- **Frame rate:** 30 Hz synchronized
- **Capture conditions:** Summer solstice, Tartu, Estonia; solar elevation < 15°, azimuth ≈ 270° (westward)

---

## Citation

If you use SolarDrive in your research, please cite:

```bibtex
@article{kumar2026solardrive,
  title     = {{SolarDrive}: A High-Contrast Glare Dataset and Diagnostic Probe
               for Advancing Autonomous Perception},
  author    = {Kumar, Debasis and Alam, Md. Jahidul and
               Matiisen, Tambet and Muhammad, Naveed},
  journal   = {IEEE Transactions on Intelligent Vehicles},
  year      = {2026},
  doi       = {10.1109/TIV.2026.XXXXXXX}
}
```

---

## Acknowledgements

The authors thank the Autonomous Driving Lab (ADL) at the University of Tartu for providing the Lexus RX450h testbed, and Mahir Gulzar and Zain Bashir for assistance with data collection.

This repository builds on the following open-source projects:
- [Ultralytics YOLO](https://github.com/ultralytics/ultralytics) — YOLOv8x, YOLO11x, RT-DETR
- [BoT-SORT](https://github.com/NirAharon/BoT-SORT) — Motion-compensated ReID tracker
- [ByteTrack](https://github.com/ifzhang/ByteTrack) — IoU-based tracker
- [MeMOTR](https://github.com/MCG-NJU/MeMOTR) — Long-term memory transformer tracker
- [MOTRv2](https://github.com/megvii-research/MOTRv2) — End-to-end transformer tracker
- [TrackEval](https://github.com/JonathonLuiten/TrackEval) — Official MOT evaluation framework

---

## License

The SolarDrive dataset and annotations are released under the [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/) license.

The code in this repository is released under the [MIT License](LICENSE).
