# Data Pipeline and Canonical State

## Pipeline purpose

The pipeline converted changing tournament sources into stable contracts used by prediction, fantasy decision, evaluation, and UI services.

## Data layers

### 1. Source layer

The system consumed live and official tournament information such as:

- fixtures and results;
- player participation and fantasy points;
- team and tournament state;
- squad and transfer state.

Raw source payloads were not treated as public artifacts and are not included in this repository.

### 2. Normalization layer

Normalization resolved source-specific inconsistencies, including:

- identifiers;
- team naming;
- fixture stage;
- result status;
- player identity;
- matchday mapping;
- numeric types and missing values.

### 3. Canonical layer

Canonical contracts represented the accepted source of truth for:

- fixtures;
- results;
- player actuals;
- current model squad;
- tournament state.

The goal was explicit ownership: every important value had one defined authority.

### 4. Projection layer

Prediction services consumed canonical state and pre-match features to generate:

- match outcomes and scorelines;
- player fantasy projections;
- Golden Boot projections;
- transfer and squad recommendations.

### 5. Snapshot and evaluation layer

Before a round, timestamped snapshots preserved predictions and relevant metadata.

After the round, evaluation used those frozen artifacts together with finalized canonical actuals.

## Refresh ordering

A typical round transition followed this order:

```text
1. synchronize completed results
2. rebuild canonical fixtures and player actuals
3. evaluate the completed round from frozen snapshots
4. rebuild cumulative evaluation dashboards
5. generate next-round match and player outputs
6. archive the next-round snapshots
7. validate snapshot coverage and model identity
8. refresh fantasy decision workflows
9. update APIs, frontend surfaces, and operations health
```

This order prevented recommendation generation from running against an incomplete or unvalidated next-round snapshot.

## Idempotency

The workflow was designed so that a safe rerun would not silently duplicate or corrupt accepted state.

Idempotent behavior depended on:

- stable identifiers;
- controlled upserts;
- explicit output paths;
- snapshot run IDs;
- validation before downstream publication;
- separation between generated current outputs and frozen artifacts.

## Reconciliation policy

Historical predictions remained immutable.

Finalized canonical actuals could correct stale evaluation labels. Those differences were recorded rather than silently ignored.

Fixture-identity disagreements were treated more strictly because they indicated an unsafe join.

## State synchronization

Some product state had multiple representations, such as a canonical squad file and a generated mirror used by application services.

The pipeline validated that mirrors matched the canonical player list and that metadata hashes were current.

## Why this matters

Without canonical state and ordered refresh contracts, a structurally valid prediction system can still produce incorrect historical evaluation, stale recommendations, or inconsistent frontend state.
