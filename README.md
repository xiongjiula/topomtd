# TopoMTD

Official implementation of **TopoMTD: Topology-Aware Multi-Teacher Distillation for Multi-Target Domain Adaptation in Retinal Vessel Segmentation**.

TopoMTD is a topology-aware multi-teacher distillation framework for retinal vessel segmentation under multi-target domain shift. It transfers complementary knowledge from multiple target-specific teachers into a unified student model while preserving vessel connectivity and centerline consistency.

## Overview

Retinal vessel segmentation often suffers from domain shifts caused by different imaging devices, acquisition protocols, and clinical centers. In realistic scenarios, annotations may only be available in one source domain, while multiple target domains remain unlabeled.

TopoMTD addresses this problem with:

- **Target-specific teachers** for different unlabeled target domains.
- **A unified student model** for efficient single-model deployment.
- **Probability-skeleton distillation** to transfer both region-level and topology-aware knowledge.
- **Reliability-aware transfer** to suppress noisy pseudo supervision and emphasize topology-critical vessel regions.
- **Partial teacher sampling** to balance teacher diversity, teacher conflict, and training efficiency.

## Framework

The training pipeline contains two stages:

1. **Teacher training stage**  
   One teacher is trained for each target domain using labeled source data and unlabeled target data.

2. **Student distillation stage**  
   The frozen teachers supervise a unified student model through:
   - a probability branch for vessel response alignment;
   - a skeleton branch for centerline continuity;
   - reliability-aware weighting for robust target-domain transfer.

## Datasets

The experiments are conducted on four public retinal vessel segmentation datasets:

- DRIVE
- CHASEDB1
- FIVES
- IOSTAR

We follow a **single-source three-target** adaptation protocol. In each experiment, one dataset is used as the labeled source domain, and the remaining three datasets are used as unlabeled target domains during training.

## Metrics

We report:

- **Dice**: region-level vessel segmentation overlap.
- **clDice**: centerline-aware topology and connectivity consistency.

## Installation

```bash
git clone https://github.com/xiongjiula/TopoMTD.git
cd TopoMTD

conda create -n topomtd python=3.10
conda activate topomtd

pip install -r requirements.txt
```

## Dataset Preparation

Please organize the datasets as follows:

```text
data/
├── DRIVE/
│   ├── images/
│   ├── masks/
│   └── fov/
├── CHASEDB1/
│   ├── images/
│   ├── masks/
│   └── fov/
├── FIVES/
│   ├── images/
│   ├── masks/
│   └── fov/
└── IOSTAR/
    ├── images/
    ├── masks/
    └── fov/
```

## Training

### 1. Train target-specific teachers

```bash
python train_teacher.py \
  --source DRIVE \
  --target CHASEDB1 \
  --config configs/teacher.yaml
```

Repeat this step for each target domain.

### 2. Train the unified student

```bash
python train_student.py \
  --source DRIVE \
  --targets CHASEDB1 FIVES IOSTAR \
  --teacher_dir checkpoints/teachers/ \
  --config configs/student.yaml
```

## Evaluation

```bash
python evaluate.py \
  --checkpoint checkpoints/student/best.pth \
  --dataset CHASEDB1 \
  --config configs/eval.yaml
```

## Main Results

TopoMTD achieves strong topology-preserving performance under the single-source three-target adaptation setting, improving vessel connectivity and reducing thin-vessel fragmentation while retaining single-model deployment efficiency.

## Citation

If you find this work useful, please cite:

```bibtex
@article{xiong2026topomtd,
  title   = {Topology-Aware Multi-Teacher Distillation for Multi-Target Domain Adaptation in Retinal Vessel Segmentation},
  author  = {Xiong, Wenyi and Liao, Shenghui and Li, Li and Zheng, Jie and Kui, Xiaoyan},
  journal = {Pattern Recognition},
  year    = {2026}
}
```

## Acknowledgements

This work was supported by the National Natural Science Foundation of China under Grant Nos. 62372475, U22A2034, and 62177047.

## License

This repository is released for academic research purposes. Please refer to the LICENSE file for details.
