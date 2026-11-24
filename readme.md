# Fully Convolutional Cross-Scale-Flows for Image-based Defect Detection

## EN3160 – Image Processing and Machine Vision


The project focuses on reproducing the results of the paper:

> **"Fully Convolutional Cross-Scale-Flows for Image-based Defect Detection"**  
> Marco Rudolph, Tom Wehrbein, Bodo Rosenhahn, and Bastian Wandt.  
> *IEEE/CVF Winter Conference on Applications of Computer Vision (WACV), 2022, pp. 1088–1097.*  
> Official code: https://github.com/marco-rudolph/cs-flow

The project consists of three main stages:

1. **Reproduce** the experimental results reported in the original paper. 
2. **Propose** a novel application of the CS-Flow-based defect detection approach.
3. **Attempt to implement** the proposed application.

The original work uses a fully convolutional cross-scale normalizing flow to detect and localize defects using multi-scale image features. 


## Reproduction Setup

The results were reproduced with the **official implementation**, run in Jupyter notebooks (one for MTD, one for MVTec AD), using the **training settings reported in the paper**:

- **Feature extractor:** EfficientNet-B5 pretrained on ImageNet (frozen), features from block 36 (304 channels) at **3 scales**  
  (MVTec AD: 768 × 768, 384 × 384, 192 × 192 · MTD: 384 × 384, 192 × 192, 96 × 96)
- **Cross-scale flow:** 4 coupling blocks (kernel sizes 3, 3, 3, 5), 1024 hidden channels, soft clamp α = 3
- **Training:** Adam, learning rate 2 × 10⁻⁴, batch size 16, gradient norm clipped to 1  
  **240 epochs** per MVTec AD class, **60 epochs** for MTD — one model per class, same settings for every class
- **Metric:** image-level AUROC on the test set, taken **after the final epoch** (no checkpoint selection on the test set)
- **Hardware:** NVIDIA RTX 4060 Laptop GPU (8 GB) — peak GPU memory 5.3 GB at 768 × 768;  
  training time ≈ 14 min (toothbrush) to 91 min (hazelnut) per MVTec AD class, **≈ 14.1 h for all 15 classes**; ≈ 21 min for one MTD run

## Reproduction Results

### MVTec AD — image-level AUROC (%)

| Category | Class | Paper | Ours | Difference |
|---|---|:---:|:---:|:---:|
| Textures | Carpet | 100.0 | 100.00 | 0.00 |
| | Grid | 99.0 | 97.08 | −1.92 |
| | Leather | 100.0 | 100.00 | 0.00 |
| | Tile | 100.0 | 99.86 | −0.14 |
| | Wood | 100.0 | 99.74 | −0.26 |
| Objects | Bottle | 99.8 | 99.76 | −0.04 |
| | Cable | 99.1 | 99.21 | +0.11 |
| | Capsule | 97.1 | 97.97 | +0.87 |
| | Hazelnut | 99.6 | 99.57 | −0.03 |
| | Metal Nut | 99.1 | 97.75 | −1.35 |
| | Pill | 98.6 | 98.31 | −0.29 |
| | Screw | 97.6 | 96.80 | −0.80 |
| | Toothbrush | 91.9 | 93.89 | +1.99 |
| | Transistor | 99.3 | 99.46 | +0.16 |
| | Zipper | 99.7 | 99.71 | +0.01 |
| **Mean** | Textures | 99.8 | 99.34 | −0.46 |
| | Objects | 98.2 | 98.24 | +0.06 |
| | **All classes** | **98.7** | **98.61** | **−0.09** |

*Paper values: Table 1 ("Ours", full training set). Texture/object means of the paper computed from its per-class values.*

### Magnetic Tile Defects (MTD) — image-level AUROC (%)

| Split seed | Ours |
|:---:|:---:|
| 0 | 99.11 |
| 1 | 99.26 |
| 2 | 98.79 |
| **Mean ± std** | **99.05 ± 0.20** |
| **Paper** (Table 2) | **99.3** |

Per defect type (seed 0): crack 100.0 · uneven 100.0 · break 99.15 · blowhole 99.10 · fray 94.59.

### Observations

- The **mean AUROC on MVTec AD (98.61 %) is within 0.1 points** of the reported 98.7 %, and the MTD mean (99.05 %) is within 0.25 points of the reported 99.3 % — the gap is about the size of the spread between the three MTD splits.
- **12 of 15 MVTec AD classes are within 1 point** of the paper; carpet and leather reach 100 %, as reported.
- The three largest deviations — **grid (−1.92), metal nut (−1.35), toothbrush (+1.99)** — go in both directions, consistent with the run-to-run variance of single, unseeded runs. Small test sets amplify this: toothbrush has only 12 good and 30 defective test images, so one misranked image changes the AUROC by several points.
- Hard object classes remain the hardest, as in the paper (toothbrush, screw, capsule); the subtle *fray* defects are the hardest MTD defect type.
- The anomaly maps (computed on the 24 × 24 latent grid of the largest scale and upsampled) localize defects coarsely, in line with the qualitative examples of the paper (Fig. 7).


## References

- M. Rudolph, T. Wehrbein, B. Rosenhahn, B. Wandt. *Fully Convolutional Cross-Scale-Flows for Image-based Defect Detection.* WACV 2022. [Paper](https://openaccess.thecvf.com/content/WACV2022/html/Rudolph_Fully_Convolutional_Cross-Scale-Flows_for_Image-Based_Defect_Detection_WACV_2022_paper.html) · [Code](https://github.com/marco-rudolph/cs-flow)

