# World Cup Fantasy Predictor — Engineering Case Study

> An independent, unofficial software engineering case study. This project is not affiliated with, sponsored by, or endorsed by FIFA or any tournament organizer.

## Overview

The World Cup Fantasy Predictor was a full-stack machine-learning system operated across an entire international football tournament.

The system:

- ingested live and official tournament data;
- maintained canonical match, player, squad, and tournament state;
- generated match and player forecasts;
- supported fantasy squad and transfer decisions;
- preserved immutable pre-match snapshots;
- evaluated completed rounds without post-result leakage;
- exposed product state and operational health through APIs and a web frontend;
- entered an explicit terminal mode after the final match.

The private source repository remains private. This public repository documents the architecture, engineering decisions, evaluation methodology, product workflows, limitations, and lessons learned.

## Why this project is primarily software engineering

Training a model was only one part of the work. The larger challenge was operating a changing prediction product safely across a live tournament.

Each round changed:

- the fixture set;
- eligible teams and players;
- available actual results;
- transfer rules and squad constraints;
- the next prediction horizon;
- the evaluation window;
- terminal behavior after elimination or tournament completion.

The project therefore required explicit data contracts, state transitions, artifact provenance, safe refresh ordering, API validation, and lifecycle-aware UI behavior.

## System at a glance

![System architecture](assets/architecture/system-overview.svg)

```text
Live and official sources
  -> ingestion and normalization
  -> canonical database and processed contracts
  -> match and player prediction services
  -> post-model safety contracts
  -> immutable pre-round snapshots
  -> fantasy decision workflows
  -> APIs and frontend product surfaces
  -> completed-round evaluation
  -> cumulative dashboards and operations health
```

## Technology

- **Backend:** FastAPI, Python
- **Frontend:** Next.js, React, TypeScript
- **Persistence:** PostgreSQL, SQLAlchemy, Alembic
- **Modeling:** rules-based forecasting, Ridge regression, probability and ranking evaluation
- **Operations:** timestamped artifacts, manifests, model registry, snapshot validation, SHA-256 checksums
- **Product surfaces:** match predictions, bracket, player evaluation, model squad, transfer targets, Team of the Round, Golden Boot, and operations health

## Public case-study map

### Architecture and engineering

- [System architecture](docs/architecture.md)
- [Data pipeline and canonical state](docs/data-pipeline.md)
- [Live operations and lifecycle](docs/live-operations.md)
- [Project retrospective](docs/project-retrospective.md)

### Prediction and decision systems

- [Match-prediction evaluation](docs/match-prediction-evaluation.md)
- [Player-prediction evaluation](docs/player-prediction-evaluation.md)
- [Fantasy decision system](docs/fantasy-decision-system.md)

### Scope and limitations

- [Limitations and future work](docs/limitations-and-future-work.md)
- [Final match summary](reports/final-match-summary.md)
- [Final player summary](reports/final-player-summary.md)

## Selected results

### Match predictions

- **104 / 104** official fixtures covered by valid pre-match outcome predictions
- **68.3%** overall outcome accuracy
- **81.3%** knockout-stage outcome accuracy
- **18.8%** exact-score accuracy across 32 knockout fixtures
- production sequence exceeded a simple descriptive baseline by **19.2 percentage points**

### Player predictions

Across 1,775 leakage-safe player-round observations per production model:

- Ridge MAE: **2.447**
- rules-baseline MAE: **2.463**
- Ridge RMSE: **3.280**
- rules-baseline RMSE: **3.429**
- Ridge Spearman: **0.281**
- rules-baseline Spearman: **0.207**

The Ridge model was retained as the fantasy-decision model because it improved ranking quality and high-value player selection, not because it accurately solved exact-point forecasting.

## Engineering highlights

- designed active, round-closeout, and tournament-complete operating modes;
- separated mutable production outputs from immutable historical snapshots;
- evaluated completed rounds only from verified pre-kickoff evidence;
- maintained canonical state across database, processed artifacts, and persisted squad state;
- implemented idempotent, transition-aware refresh sequencing;
- validated row coverage, model identity, state consistency, and terminal semantics;
- preserved accepted closeout artifacts with manifests and checksums;
- recovered historical provenance without regenerating predictions after results were known.

## Repository boundary

This public repository intentionally excludes:

- application source code;
- environment files and credentials;
- raw or scraped datasets;
- private operational commands;
- local filesystem paths;
- debugging history and backup files;
- model binaries;
- unreviewed runtime artifacts.

It is an engineering case study, not a source-code release or an official tournament data distribution.

## Screenshots

Product screenshots can be added under `assets/screenshots/` after a final privacy and licensing review. Recommended surfaces:

1. operations dashboard;
2. match prediction history;
3. tournament bracket;
4. model evaluation;
5. player evaluation;
6. model squad;
7. transfer targets;
8. Team of the Round.

## License

Documentation and repository-authored assets are released under the MIT License. Third-party names, trademarks, and any referenced tournament data remain the property of their respective owners.
