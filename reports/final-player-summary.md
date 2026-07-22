# Final Player-Prediction Summary

## Scope

Primary rules-versus-Ridge comparison:

- six tournament round groupings;
- **1,775** player-round observations per model.

Common three-model appearance benchmark:

- final four knockout groupings;
- **508** player-round observations per model.

## Primary results

| Model | Coverage | MAE | RMSE | Within 1 | Within 2 | Bias | Spearman |
|---|---:|---:|---:|---:|---:|---:|---:|
| Rules baseline | 1,775 | 2.463 | 3.429 | 36.2% | 53.7% | -0.182 | 0.207 |
| Ridge model | 1,775 | 2.447 | 3.280 | 28.0% | 55.2% | +0.142 | 0.281 |

## Ranking results

| Model | Mean round Spearman | Top-20 recall | Top-20 average actual points | Gain vs full pool |
|---|---:|---:|---:|---:|
| Rules baseline | 0.252 | 25.0% | 4.16 | +1.14 |
| Ridge model | 0.299 | 29.2% | 4.78 | +1.76 |

## Naive appearance benchmark

The naive appearance model achieved lower MAE by making conservative low predictions, but it had:

- the worst RMSE;
- a strong negative bias;
- weaker ranking correlation.

## Interpretation

Ridge was retained as the fantasy-decision model because it improved ranking quality and reduced large-error exposure.

It was not presented as a high-accuracy exact-point model.

## Realism audit

No admitted immutable post-contract row triggered the predefined checks for:

- high forecast with low projected minutes;
- non-starter or role inconsistency;
- eliminated or no-next-fixture player with positive forecast;
- high forecast without supporting prior contribution.

This is a targeted diagnostic result, not proof that every forecast was realistic.
