# Match-Prediction Evaluation

## Evaluation question

Did the production match-prediction sequence add useful predictive signal while preserving valid pre-match provenance?

## Leakage-safe evidence

Historical rows were admitted only when the source contract identified a timestamped pre-kickoff prediction artifact.

Every evaluated row had to satisfy:

```text
prediction generated time <= fixture kickoff time
```

Mutable post-tournament “latest” outputs were not accepted as historical evidence.

## Coverage

| Scope | Valid predictions |
|---|---:|
| Group stage | 72 / 72 |
| Knockout stage | 32 / 32 |
| Overall | 104 / 104 |

Scoreline evaluation covered all 32 knockout fixtures.

## Core results

### Outcome accuracy

| Scope | Correct | Accuracy |
|---|---:|---:|
| Group stage | 45 / 72 | 62.5% |
| Knockout stage | 26 / 32 | 81.3% |
| Overall | 71 / 104 | 68.3% |

### Knockout scoreline quality

| Metric | Result |
|---|---:|
| Exact scores | 6 / 32 (18.8%) |
| Team-1 goal MAE | 0.66 |
| Team-2 goal MAE | 0.94 |
| Total-goals MAE | 1.34 |

### Probability quality

| Metric | Result |
|---|---:|
| Multiclass Brier score, sum over classes | 0.488 |
| Multiclass Brier score, mean per class | 0.163 |
| Multiclass log loss | 0.846 |

## Descriptive baseline

| Model | Correct | Accuracy |
|---|---:|---:|
| Always predict the first-listed team | 51 / 104 | 49.0% |
| Frozen production sequence | 71 / 104 | 68.3% |

The production sequence improved on this descriptive baseline by 19.2 percentage points.

The baseline is intentionally simple and should not be interpreted as a complete state-of-the-art benchmark.

## Safe challenger comparison

A separate eight-match comparison covered the quarter-finals through the final using the same frozen evaluation set for each model.

The best safe alternative, a Poisson scoreline model, matched the production model’s outcome and exact-score counts but did not improve probability quality.

Because the sample contained only eight fixtures, this result is supporting evidence rather than a full-tournament model comparison.

## Dominant model signals

The production system primarily used interpretable team-strength and recent-form signals:

- Elo rating difference;
- ranking-strength difference;
- recent points per match;
- recent goal balance;
- tournament points;
- tournament goal difference;
- tournament goals scored;
- recent attacking and defensive output for scoreline generation.

The report does not claim causal feature importance.

## Main conclusion

The production sequence extracted useful signal beyond a simple descriptive baseline. The stronger engineering contribution was the ability to prove that each evaluated prediction existed before kickoff.
