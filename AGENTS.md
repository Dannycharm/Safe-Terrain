# Project context

CS 543 / ECE 549, Fall 2026. Estimate semantic terrain risk from RGB images and measure whether safety-aware learning and object evidence reduce false-safe predictions. The study compares risk predictions against annotated masks. Physical robot traversability is outside scope.

- Praise Daniels owns repository setup and SegFormer-B0 fine-tuning on RELLIS-3D. Compare standard cross-entropy, class-weighted cross-entropy, and an asymmetric loss that penalizes underestimated risk. Praise also owns segmentation ablations.
- Derek Ngan owns frozen Grounding DINO Tiny inference with predefined prompts, thresholds, and cached predictions. Detector fine-tuning is outside scope.
- Roxi Li owns RELLIS-3D preprocessing, risk-label mapping, splits, and evaluation. Report false-safe rate, false-unsafe rate, hazard recall, IoU by tier, and mean IoU to characterize safety and conservativeness.
- Denver Soekawan owns alignment, deterministic pixelwise max fusion, margin ablations, and pipeline integration. Compare segmentation-only risk with fused risk. Box-margin and detection-threshold ablations are optional.

Everyone contributes to experiments, analysis, and reporting. The progress report is due November 30, 2026. The final deadline is December 16, 2026.

# Repository architecture

- Place preprocessing and dataset interfaces in `src/data/` and metrics in `src/evaluation/` (Roxi).
- Place segmentation models, training, and losses in `src/segmentation/` (Praise), detector inference and prompts in `src/detection/` (Derek), and risk alignment and fusion in `src/fusion/` (Denver).
- Use `configs/` for shared experiment settings, `scripts/` for entry points that orchestrate modules, `tests/` for checks, and `reports/` for report sources and small result summaries.
- Keep dependencies directional. Pass component outputs through agreed interfaces, keep orchestration at entry points, and reuse logic from the module that owns it to avoid circular imports and duplication.

# Coding conventions

Use Python 3, consistent names, readable modules, and type hints where they clarify interfaces. Document expected inputs, outputs, shapes, and coordinate conventions where relevant. Prefer small, testable functions and existing dependencies. Keep refactoring and abstractions proportional to the task.

# Research reproducibility

- Record random seeds, model identifiers and revisions when available, hyperparameters, dependency versions, and dataset splits.
- Document and coordinate every risk-label mapping change. Keep training, validation, and held-out test data separate. Select hyperparameters on validation data only.
- Apply the same evaluation protocol across methods. Record ignore handling and metric definitions.
- Preserve checkpoint and prediction metadata, including experiment settings and the provenance needed to reproduce them. Keep large datasets, weights, and generated artifacts outside Git.

# Integration contracts

- Risk tiers are traversable (0), caution (0.5), and hazard (1). Exclude ignored labels from loss and metrics.
- Dataset items provide an RGB image, risk-tier mask, and stable image identifier. Resize categorical labels with nearest-neighbor interpolation.
- Segmentation returns three per-pixel probabilities in a documented channel order and a continuous terrain-risk map.
- Detections carry image identifiers, boxes, labels, and confidence scores. Document pixel-coordinate conventions and image dimensions. Grounding DINO scores are detector confidence scores. Do not interpret them as calibrated physical-risk probabilities.
- Align predictions by image identifier and pixel grid before taking the pixelwise maximum of terrain and object risk. Fusion uses model predictions only. Reserve test ground-truth masks for evaluation.

# Git collaboration

Keep `main` stable. Use small feature branches, focused commits, and reviewed Pull Requests. Incorporate changes from `main` frequently. Coordinate interface changes before implementing them and avoid edits in another member's module unless necessary. Do not force-push shared branches or commit large generated files.

# Codex instructions

1. Inspect the repository and relevant modules first.
2. Identify the requested scope.
3. Preserve established interfaces unless the task requires changes.
4. Make focused changes and leave unrelated modules alone.
5. Add or update appropriate tests for the requested behavior.
6. Run relevant tests or static checks when feasible.
7. Clearly distinguish completed work from untested assumptions.
8. Summarize changed files, verification results, and remaining issues.

Perform Git commits, merges, or pushes only when explicitly requested.

# Evolving documentation

Keep this guide concise and accurate. Update it when confirmed decisions materially change the architecture, interfaces, evaluation rules, or experiment conventions. Routine small code changes seldom require a guide update.
