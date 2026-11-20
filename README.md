# Safety-Aware Terrain Segmentation with Open-Vocabulary Object-Guided Risk Refinement

CS 543 / ECE 549 · Fall 2026

Terrain segmentation can miss small obstacles or underestimate the risk of ambiguous surfaces. This project tests whether a safety-aware segmentation loss and open-vocabulary object detections reduce those errors in RGB images. The expected output is a reproducible comparison of risk maps and their safety–conservativeness trade-offs. The risk tiers are semantic proxies derived from image labels. Physically validated robot traversability and navigation are outside the project scope.

## Research questions and approach

- Does class weighting or an asymmetric penalty for underestimating risk reduce false-safe predictions? Fine-tune pretrained SegFormer-B0 on RELLIS-3D and compare standard cross-entropy, class-weighted cross-entropy, and the asymmetric loss. Map semantic labels to traversable (0.0), caution (0.5), or hazard (1.0).
- Does object evidence improve hazard recall, and at what cost in false-unsafe predictions? Run frozen, pretrained Grounding DINO Tiny with predefined text prompts to produce boxes, labels, and scores. Compare segmentation-only predictions with deterministic max fusion, `R_fused = max(R_terrain, R_object)`, applied pixel by pixel. Optionally ablate box margins and detection thresholds.
- How do the methods trade off safety and conservativeness? Compare false-safe and false-unsafe rates, hazard recall, tier-wise IoU, and mean IoU under a common evaluation protocol.

## Team and repository organization

| Path | Owner | Responsibility |
| --- | --- | --- |
| `src/segmentation/` | Praise Daniels | SegFormer, segmentation baselines, safety-aware loss, segmentation ablations, and repository setup |
| `src/detection/` | Derek Ngan | Grounding DINO, prompts, detection thresholds, and cached predictions |
| `src/data/` and `src/evaluation/` | Roxi Li | RELLIS-3D preprocessing, risk-label mapping, dataset splits, and evaluation metrics |
| `src/fusion/` | Denver Soekawan | Risk-map alignment, deterministic fusion, margin ablations, and pipeline integration |
| `configs/` | Shared | Experiment settings, maintained by the relevant component owner |
| `scripts/` | Shared | Experiment entry points, maintained by the relevant component owner |
| `tests/` | Shared | Component and integration checks |
| `reports/` | Shared | Report sources and small result summaries |

Everyone contributes to experiments, analysis, and final reporting.

## Getting started

On Ubuntu/Linux, replace the placeholder clone URL with the team's GitHub URL. The commands below use the segmentation branch as an example.

```bash
git clone https://github.com/YOUR-ORG/YOUR-REPO.git terrain-risk
cd terrain-risk
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
git switch main
git pull --ff-only origin main
git switch -c feature/segmentation
```

The dependency list is an initial, unpinned starting point. Record the versions used for experiments.

## Dataset and pretrained models

- Follow the official instructions for dataset access and setup. [RELLIS-3D](https://github.com/unmannedlab/RELLIS-3D)
- Initialize the segmentation encoder from the pretrained B0 checkpoint. [SegFormer-B0](https://huggingface.co/nvidia/mit-b0)
- Use the pretrained detector in inference mode. [Grounding DINO Tiny](https://huggingface.co/IDEA-Research/grounding-dino-tiny)

Keep datasets, extracted archives, checkpoints, and prediction caches local and out of Git. The root `data/`, `checkpoints/`, and `cache/` directories are ignored. See `.env.example` for optional path settings. Dataset acquisition is manual.

## Git workflow

Use `main` as the stable shared integration branch and avoid direct pushes during feature development. Start from updated `main` on `feature/segmentation`, `feature/detection`, `feature/data-eval`, or `feature/fusion`. Commit and push focused changes to your feature branch. Frequently incorporate updates from `main`. Open a Pull Request into `main` once a coherent increment works, and merge it after review. Small, useful increments can land while the rest of the feature is still in progress.

```bash
# From your feature branch, with your changes already committed
git fetch origin
git merge origin/main
git push -u origin HEAD
```

## Integration expectations

| Stage | Intended interface |
| --- | --- |
| Dataset | RGB image, risk-tier mask, and stable image identifier |
| Segmentation | Three per-pixel class probabilities with a documented channel order, plus a continuous terrain-risk map |
| Detection | Bounding boxes, labels, confidence scores, and image identifier |
| Fusion | Aligned terrain and object risk maps in, fused risk map out |
| Evaluation | Prediction outputs and corresponding ground-truth risk masks |

Agree on image dimensions, coordinate conventions, risk-tier definitions, and the representation of ignored pixels. Align predictions to a shared pixel grid before fusion. Exclude ignored pixels from loss and metrics.

## Milestones

| Dates in 2026 | Milestone |
| --- | --- |
| November 22–28 | Dataset setup, baseline model inference and training, and evaluation infrastructure |
| November 29–December 5 | Required model comparisons and initial fusion |
| November 30 | Progress report due |
| December 6–12 | Evaluation, ablations, and result analysis |
| December 13–16 | Final report, reproducibility review, and submission |
| December 16 | Final deadline |
