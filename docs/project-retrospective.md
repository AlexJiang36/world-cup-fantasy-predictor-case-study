# Project Retrospective

## What changed during the project

The project began as a prediction application and became a tournament operations system.

Live use exposed problems that were less visible during initial development:

- changing stage and fixture semantics;
- mutable artifacts used for historical pages;
- player usage uncertainty;
- elimination and next-fixture constraints;
- mirrored state drift;
- evaluation leakage risk;
- terminal behavior after the final match.

## Strongest engineering outcomes

### Explicit lifecycle design

Active, closeout, and tournament-complete modes replaced implicit assumptions about “the next round.”

### Canonical state ownership

Fixtures, player actuals, squad state, and accepted artifacts gained clearer sources of truth.

### Leakage-safe evaluation

Immutable pre-match snapshots made model evaluation defensible.

### Provenance recovery

Historical prediction identity was recovered through timestamps, run IDs, snapshot-local paths, and validation.

### Measured model governance

The final system compared practical baselines and documented model limitations rather than selecting models only from intuition.

### Product observability

The operations dashboard and validation scripts made freshness and lifecycle state visible.

## What did not work perfectly

### Exact player-point prediction

Fantasy points remained sparse and event-driven. Appearance-only predictions could achieve low MAE while failing to identify high-value players.

### Usage uncertainty

Expected minutes and starter assumptions remained imperfect, especially when actual playing time was limited.

### Changing model families

The production match-model family evolved during the tournament, making some full-tournament calibration analyses less clean.

### Historical artifact organization

Earlier artifact conventions were less strict than the final snapshot and registry contracts, requiring closeout provenance recovery.

### Manual intervention

Some final squad, availability, and tournament details required manual confirmation. The project should not be described as fully autonomous.

## What I would repeat

- define immutable snapshot contracts early;
- separate current outputs from historical evidence;
- build terminal-state behavior before the final event;
- make recommendation services depend on validated snapshots;
- retain model and run identity beside every product output;
- treat evaluation as part of production governance.

## What I would change

- introduce a single artifact registry from the first round;
- formalize player availability and expected-minutes inputs earlier;
- persist standardized model coefficients and scaler metadata;
- create a frozen transfer-versus-hold experiment;
- automate screenshot and public-report export after each accepted round;
- reduce temporary patch and backup files through stronger branching and release discipline.

## Main lesson

Reliable ML products are systems problems. Model quality matters, but state ownership, provenance, lifecycle design, validation, and recovery often determine whether the result is trustworthy.
