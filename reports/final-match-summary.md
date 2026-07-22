# Final Match-Prediction Summary

## Scope

The final evaluation used verified pre-kickoff prediction evidence.

- official fixtures: **104**
- valid pre-match outcome predictions: **104**
- knockout scoreline predictions: **32**
- excluded outcome rows: **0**

## Results

| Metric | Result |
|---|---:|
| Overall outcome accuracy | 71 / 104 (68.3%) |
| Group-stage outcome accuracy | 45 / 72 (62.5%) |
| Knockout outcome accuracy | 26 / 32 (81.3%) |
| Knockout exact scores | 6 / 32 (18.8%) |
| Team-1 goal MAE | 0.66 |
| Team-2 goal MAE | 0.94 |
| Total-goals MAE | 1.34 |
| Multiclass Brier score | 0.488 |
| Multiclass log loss | 0.846 |

## Descriptive baseline

| Model | Accuracy |
|---|---:|
| Always predict the first-listed team | 49.0% |
| Frozen production sequence | 68.3% |

Improvement: **19.2 percentage points**.

## Interpretation

The production sequence added useful predictive signal.

The stronger engineering result was full pre-match provenance across all 104 fixtures. Historical evaluation did not use predictions regenerated after the actual result.

## Important caveat

A late-knockout Poisson challenger was evaluated safely on only eight fixtures. It matched the production outcome count but did not improve probability quality. This is supporting evidence, not a full-tournament challenger comparison.
