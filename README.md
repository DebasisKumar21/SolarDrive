# SolarDrive
SolarDrive: A diagnostic Multi-Object Tracking (MOT) benchmark exposing catastrophic perception failures and Vulnerable Road User (VRU) risks under extreme solar glare.
# ☀️ SolarDrive Dataset

**SolarDrive: A New Autonomous Vehicle Dataset for Advancing Perception Systems under Extreme Sun Glare**

![License: CC BY 4.0](https://img.shields.io/badge/License-CC_BY_4.0-lightgrey.svg)
![Dataset Size](https://img.shields.io/badge/Dataset-2,452_Frames-blue)
![Task](https://img.shields.io/badge/Task-Multi--Object_Tracking_(MOT)-orange)

Autonomous perception systems achieve high accuracy on standard benchmarks yet face catastrophic failure under extreme solar glare—a physically destructive condition that obliterates nearly a third of usable visual information. 

**SolarDrive** is a curated diagnostic probe dataset captured at extreme solar elevation angles (<15°). It isolates the photometric failure modes of global shutter sensors and serves as a targeted stress-test for spatial object detection and temporal tracking continuity. 

Our benchmark exposes a critical safety vulnerability: **pedestrian detectability collapses to 26.4% mAP@50** under extreme glare, creating a disproportionate risk to Vulnerable Road Users (VRUs).

---

## 🚀 Key Features & Findings

* [cite_start]**Targeted Glare Regimes:** 4 distinct geographic sequences isolating direct solar saturation, canopy-induced stroboscopic flicker, and specular asphalt reflections[cite: 50, 63].
* **Radiometric Information Loss (RIL):** Introduces a new metric quantifying photometric severity. [cite_start]Glare in this dataset obliterates an average of 30.90% of spatial gradients (∇I ≈ 0), peaking at 36.41%[cite: 96, 102].
* [cite_start]**Transformer Vulnerability:** Probing state-of-the-art models reveals a systemic failure in zero-shot End-to-End Transformers (MeMOTR, MOTRv2), which collapse under texture starvation[cite: 19, 250].
* **Kinematic Tracking Necessity:** Purely spatial tracking (ByteTrack) suffers massive identity fragmentation under stroboscopic glare. [cite_start]Motion-compensated ReID (BoT-SORT) reduces Identity Switches (IDS) by 55%[cite: 258, 260].

---

## 📊 Dataset Structure & Statistics

[cite_start]SolarDrive provides dense, continuous sequential data required for Multi-Object Tracking, intentionally avoiding the clear-weather bias of standard benchmarks[cite: 118].

* [cite_start]**Total Annotated Frames:** 2,452 (Left Camera, 30 Hz) [cite: 99, 101]
* [cite_start]**Raw Stereo Frames:** 2,390 (Right Camera) provided for future asymmetric stereo recovery research [cite: 119]
* [cite_start]**Total Bounding Boxes:** 16,628 [cite: 99]
* [cite_start]**Resolution:** 2064 × 1544 (Sony IMX265 CMOS Global Shutter) [cite: 43, 46]

### Class Taxonomy
[cite_start]To ensure seamless compatibility and prevent class-mapping collisions, all ground truth annotations are mapped to six integer class IDs[cite: 176]:
* `0`: Pedestrian
* `1`: Cyclist
* `2`: Car
* `3`: Motorcycle
* `4`: Bus
* `5`: Truck

[cite_start]*(Note: For class-agnostic MOT evaluation via TrackEval, all dynamic agents are aggregated into a unified target class)*[cite: 183, 184].

---

## 🛠️ Data Format

[cite_start]Annotations follow the standard **MOTChallenge 10-column sequence format**[cite: 182]:
`<frame_id>, <track_id>, <xmin>, <ymin>, <width>, <height>, <conf>, <class_id>, <visibility>, <unused>`

Example:
`1, 5, 1024, 600, 150, 80, 1, 2, 1, -1`

---

## 📈 Baseline Models

We provide baseline evaluation scripts for the following architectures tested in the paper:

**Detection Backbones:**
* [cite_start]CNN: YOLOv8x, YOLO11x [cite: 187, 198]
* [cite_start]ViT: RT-DETR-L, RT-DETR-X [cite: 187, 200]

**Tracking Pipelines:**
* [cite_start]Tracking-by-Detection (TbD): BoT-SORT, ByteTrack [cite: 246]
* [cite_start]End-to-End Transformers: MeMOTR, MOTRv2 [cite: 246]

---

## 📥 Download and Setup

*(Provide links to your hosted data here, e.g., Google Drive, Zenodo, or AWS S3)*

1. Clone the repository:
   ```bash
   git clone [https://github.com/YourUsername/SolarDrive.git]([https://github.com/YourUsername](https://github.com/DebasisKumar21/SolarDrive.git)
   cd SolarDrive


   Download the dataset and extract it into the data/ directory:

SolarDrive/
├── data/
│   ├── sun_glare_0/
│   │   ├── img1/
│   │   └── gt/gt.txt
│   ├── sun_glare_1/
│   └── ...
Install dependencies for evaluation:

Bash
pip install -r requirements.txt
📜 License
The SolarDrive dataset and code are released under the Creative Commons Attribution 4.0 International (CC BY 4.0) license.

🎓 Citation
If you use SolarDrive or the RIL metric in your research, please cite our paper:

Code snippet
@article{kumar2026solardrive,
  title={SolarDrive: A New Autonomous Vehicle Dataset for Advancing Perception Systems under Extreme Sun Glare},
  author={Kumar, Debasis and Alam, Md. Jahidul and Matiisen, Tambet and Muhammad, Naveed},
  journal={TBD},
  year={2026}
}

### Next Steps

This covers your repository setup. Are you ready to tackle **Step 2 (Calculating the RIL
