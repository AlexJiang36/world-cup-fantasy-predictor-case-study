# Limitations and Future Work

## Evaluation limitations

### Match models

- The production match-model family changed during the tournament.
- Scoreline evaluation began with the knockout stage.
- The safely evaluated challenger comparison covered only eight late-knockout fixtures.
- Small late-round samples limit calibration and model-comparison conclusions.

### Player models

- The primary rules-versus-Ridge comparison began at MD3.
- The naive appearance baseline was available for only four knockout groupings.
- Fantasy points are sparse and dominated by discrete events.
- Starter-versus-substitute reporting was omitted because the available historical starter field was not a reliable reporting contract.
- Actual-minute bands are retrospective diagnostics, not pre-match labels.
- A leakage-safe transfer-versus-hold experiment was not available.
- Standardized Ridge coefficients and scaler metadata were not preserved clearly enough for public feature-importance ranking.

## Product limitations

- Some player availability and final squad details required manual confirmation.
- The system was not fully autonomous.
- Public screenshots require a separate privacy and rights review.
- The private source repository contains runtime and operational material that is intentionally excluded here.

## Data limitations

- Raw or scraped source payloads are not redistributed.
- This repository does not provide a public dataset.
- Third-party naming, trademarks, and tournament data remain subject to their owners’ rights.
- The case study cannot guarantee that external source schemas would remain stable for a future tournament.

## Future engineering work

### Artifact and release management

- use one artifact registry from the start;
- create signed release manifests for every round;
- automate public-safe report generation;
- isolate runtime outputs from source-controlled files.

### Player usage modeling

- add explicit lineup probability;
- improve projected-minutes calibration;
- model injury, suspension, and substitution risk;
- distinguish attacking role and set-piece responsibility.

### Decision evaluation

- freeze recommendation and hold baselines before every transfer window;
- evaluate squad-level utility rather than player-level points only;
- compare one-round and multi-round decisions;
- measure risk-adjusted value under budget and team constraints.

### Observability

- add automated freshness alerts;
- track contract violations over time;
- expose model and artifact identity consistently on every product surface;
- create public-safe operational diagrams and release summaries.

### Deployment

A future version could package a minimal reproducible demo with:

- synthetic or redistributable data;
- containerized services;
- read-only historical snapshots;
- a reduced public API;
- selected frontend surfaces.

## Public claim boundary

This case study does not claim:

- FIFA endorsement;
- state-of-the-art football forecasting;
- accurate exact player-point prediction;
- complete automation;
- redistribution rights for third-party datasets;
- production availability beyond the tournament project.
