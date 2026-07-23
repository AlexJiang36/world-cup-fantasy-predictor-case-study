# Engineering Retrospective

The World Cup Fantasy Predictor began as a compact forecasting project. By the end of the tournament, it had become a full-stack live machine-learning system with data ingestion, canonical state, match and player models, fantasy decision support, leakage-safe evaluation, operational dashboards, and an explicit tournament-complete mode.

The hardest part was not training one more model. It was preserving trustworthy state while the data, tournament structure, prediction horizon, and product requirements changed after every round.

## What was originally planned

The original goal was smaller:

- ingest teams, fixtures, and players;
- generate match outcome and scoreline predictions;
- estimate player fantasy points;
- recommend a fantasy squad and transfer targets;
- expose the results through FastAPI and Next.js.

The assumed flow was straightforward:

```text
source data
  -> normalized records
  -> predictions
  -> recommendations
  -> frontend
```

That was enough for a prototype, but not for a system operated across a complete live tournament. Once real rounds began, every refresh also had to answer:

- Which fixtures are complete?
- Which teams and players still have a next match?
- Which prediction existed before kickoff?
- Which model and run produced the displayed result?
- Can the completed round be evaluated without post-result information?
- Is the saved fantasy state still consistent with confirmed decisions?
- Does a zero-row output indicate failure, or the correct terminal state?

Those questions changed the project from a prediction application into a lifecycle-aware software system.

## How the project expanded

The final product included:

- canonical match, player, tournament, and fantasy-squad state;
- match outcome probabilities and scoreline predictions;
- a predicted knockout bracket and tournament forecast;
- player fantasy projections;
- general and squad-specific transfer planning;
- captain, formation, price, bank, and transfer state;
- Golden Boot and Team of the Round views;
- match-model and player-model evaluation dashboards;
- immutable pre-round prediction snapshots;
- model registry and refresh manifests;
- operations health and artifact-freshness reporting;
- active, round-closeout, and tournament-complete modes.

A round transition therefore became an ordered workflow:

```text
freeze pre-round predictions
  -> play matches
  -> synchronize completed-match actuals
  -> rebuild canonical state
  -> evaluate the frozen predictions
  -> generate next-round forecasts
  -> apply fantasy safety contracts
  -> archive and validate the next snapshot
  -> refresh decision-support surfaces
```

The ordering mattered. A command could finish successfully and still produce invalid historical evaluation or stale recommendations if the stages ran in the wrong sequence.

## Hardest technical problems

### Coordinating changing tournament state

The tournament did not have one stable operating mode. Group-stage fixtures, knockout elimination, limited-transfer windows, semifinal planning, final planning, and tournament completion all changed what a valid output meant.

The same assumptions could not apply everywhere:

- a group-stage player might have multiple future fixtures;
- an eliminated player must have no future value;
- a knockout match can be drawn after 90 minutes even though one team eventually advances after extra time or penalties, so regular-time outcome and advancement must be modeled separately;
- a transfer window may be open, exhausted, or permanently closed;
- after the final, future-facing products must stop producing recommendations.

The solution was to make lifecycle state explicit:

1. **Tournament active** — produce future forecasts and decision outputs.
2. **Round closeout** — evaluate frozen predictions before creating the next round's outputs.
3. **Tournament complete** — preserve final state and produce no future-facing decisions.

### Preserving agreement across state stores

The project used relational records, processed canonical data, generated CSV and JSON artifacts, persisted squad state, registry metadata, evaluation dashboards, and frontend API responses.

Each store was useful, but duplicated concepts created failure modes. A squad CSV could be correct while its JSON mirror was stale. A frontend label could name one model while the underlying artifact came from another. A `latest` file could point to the newest run when the product needed an older pre-match run.

The solution was to define ownership:

- one canonical source for each concept;
- generated mirrors validated against that source;
- explicit model and run identifiers;
- row-count, identity, and schema checks at boundaries;
- manifests describing what each refresh produced.

## Data quality and identity matching

Live sports data rarely arrives with one universal identity system. Different sources may represent the same player or fixture with different identifiers, names, abbreviations, team labels, positions, or timestamps.

Name-only joins were unsafe because of accents, alternate names, duplicate surnames, roster changes, inconsistent positions, and delayed records. The project increasingly relied on canonical identities and explicit mappings. When automated matching was necessary, team and position context supported the decision, and ambiguous cases remained visible for review.

> Identity resolution is part of the data model, not a cleanup step at the edge of the pipeline.

A production redesign would give identity mappings their own versioned table, confidence status, review history, and source-specific identifiers.

## State and provenance failures

The most consequential early design weakness was overreliance on files named `latest`.

A `latest` alias is useful for serving the current product, but it does not explain:

- when a prediction was generated;
- which model version produced it;
- whether it existed before the result;
- what source data was available at that time;
- whether it has since been overwritten;
- whether displayed fields came from the same source.

The project added stronger provenance through immutable timestamped snapshots, model names, run IDs, generated timestamps, snapshot registries, coverage metadata, manifests, validation reports, checksums, and a final accepted-artifact inventory.

> `latest` is a serving convenience, not historical evidence.

Prediction provenance also became part of the API contract. A frontend should not display a probability, scoreline, or player projection without enough metadata to identify its model and run.

## Leakage-safe evaluation

Historical evaluation is meaningful only when the prediction being scored existed before the actual result.

A mutable live pipeline makes accidental leakage easy. Rebuilding a `latest` prediction after results are synchronized can produce a structurally valid file that contains information unavailable before kickoff.

The final contract was:

```text
immutable pre-round prediction snapshot
  + actuals from the matching completed round
  -> evaluation
```

The system therefore:

- archived match and player predictions before each round;
- registered immutable player snapshots;
- joined actuals only to the matching frozen round;
- separated round checkpoints from cumulative dashboards;
- preserved null metrics when a model had no safe comparison;
- rejected regenerated post-result predictions as historical evidence.

The goal was not to fill every dashboard cell. It was to report only comparisons that could be defended.

## Fantasy scoring realism

A statistically generated point estimate is not automatically a useful fantasy recommendation.

During development, some low-minute or low-output players received forecasts near the range normally associated with a goal, assist, clean sheet, or other major return. The estimates could be mathematically consistent with their inputs while still violating the practical structure of fantasy scoring.

The final flow separated:

```text
raw model estimate
  -> usage and starter checks
  -> scoring-realism checks
  -> availability and eligibility checks
  -> decision-support value
```

Important contracts included:

- actual starts and minutes override stale expected-starter assumptions;
- a player without a reliable role should not inherit a full-starter projection;
- appearance points remain separate from attacking upside;
- eliminated players and players without a next fixture receive no future value;
- manual availability information may override stale automated assumptions;
- transfer ranking considers role, risk, price, team limits, and transfer constraints rather than projected points alone.

> Model output and product decision value are different concepts.

The Ridge model was retained because it improved ranking quality and high-value player selection, not because it solved exact-point forecasting.

## Confirmed fantasy state versus recommendations

The persisted model squad represented accepted state after manual review. Transfer recommendations represented proposed actions. Combining those concepts would allow a new model run to overwrite decisions that had already been confirmed.

The system therefore kept confirmed squad state separate from generated recommendations. The stored state included selected players, roles, captaincy, formation, prices, bank, transfer usage, and confirmed manual changes.

Recommendation artifacts could read that state and propose changes, but they could not silently redefine it.

> Recommendations are suggestions; confirmed state is a separate source of truth.

## Terminal-state design

After the final match, a naïve health check could interpret empty future outputs as a broken pipeline.

The correct terminal state was:

```text
104 completed fixtures
0 future fixtures
0 future scoreline predictions
0 future player inference required
0 general transfer targets
0 model-squad recommendations
```

The final squad, model registry, evaluation artifacts, bracket, and historical predictions still had to remain available. The system needed to stop generating future actions without erasing the completed product.

The Operations dashboard therefore exposed semantic status, not just `HTTP 200`. It explained that the tournament was complete, zero future rows were expected, production and evaluation models were preserved, recommendations were intentionally closed, and final artifacts remained healthy.

> Health is semantic. A successful response code does not prove that the returned state is correct.

## What would be redesigned

### A first-class tournament state machine

Lifecycle behavior should be modeled explicitly from the beginning:

```text
pre_round
  -> round_live
  -> round_closeout
  -> next_round_ready
  -> tournament_complete
```

Each transition would declare permitted reads, writes, generated artifacts, and validation gates.

### One artifact metadata store

Generated files would remain useful, but a queryable metadata store would track artifact identity, model and run ID, stage, generation time, source snapshot, schema version, coverage, checksum, lifecycle status, and public-safe status.

### A dedicated identity-resolution layer

Player, team, and fixture mappings should be durable versioned data with confidence and manual-review states, rather than logic reconstructed inside individual ingestion jobs.

### Contract-first schemas

Backend responses, frontend clients, manifests, and generated artifacts should share versioned schemas validated before data reaches decision or evaluation stages.

### Smaller orchestration units

The refresh pipeline should be split into idempotent jobs:

```text
ingest -> normalize -> validate -> evaluate
       -> predict -> archive -> recommend -> publish
```

Retries could then occur at job boundaries without rerunning unrelated stages.

### Contract-focused regression tests

The most valuable tests would verify that:

- post-result runs cannot be used as historical evidence;
- eliminated players cannot retain future value;
- actual usage overrides stale starter flags;
- squad CSV and JSON mirrors agree;
- round checkpoints cannot replace cumulative evaluation;
- tournament-complete mode produces the expected zero outputs;
- APIs expose model and provenance metadata consistently.

## What would be productionized next

For another live tournament, the next priorities would be:

1. **Managed deployment** — containerized services, managed PostgreSQL, object storage for immutable artifacts, and environment-specific configuration.
2. **Workflow orchestration** — scheduled or event-aware jobs with retries, dependency tracking, transition locks, and validation-gate notifications.
3. **Observability** — structured logs, metrics, error tracking, freshness alerts, data-quality dashboards, and semantic health checks.
4. **Versioned model and feature registry** — reproducible feature definitions, promotion status, evaluation coverage, rollback, and lineage.
5. **Stronger source adapters** — source-specific schemas, caching, rate-limit handling, and contract tests.
6. **Automated release checks** — CI validation for schemas, documentation links, API contracts, frontend builds, snapshot safety, and public-release boundaries.
7. **Durable user state** — database-backed fantasy state with a strict boundary between confirmed actions and generated recommendations.
8. **Operational ownership** — clear approval rules for identity mappings, availability overrides, model promotion, historical reconstruction, and final freezes.

## Final lessons

The project produced several reusable engineering principles:

1. A mutable `latest` artifact is not sufficient historical evidence.
2. Prediction provenance belongs in storage and API contracts.
3. Evaluation must be designed before predictions need to be scored.
4. Raw model output requires domain and product safety contracts.
5. Confirmed state must remain separate from recommendations.
6. Empty future outputs can represent a valid completed lifecycle.
7. Operations dashboards should report semantic state, not only service availability.
8. A live ML product is primarily a state, data, and operations problem—not only a modeling problem.

The project ended with a stronger architecture than originally planned because the live tournament exposed failure modes that a static prototype would not reveal. The most valuable outcome was not one prediction metric, but a set of engineering patterns for building prediction products that remain explainable, reproducible, and safe as their real-world state changes.
