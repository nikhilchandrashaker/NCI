# NFL Consistency Index (NCI)

Does cross-dimensional statistical balance predict future NFL success?

## What's in this repo

| File | Description |
|---|---|
| `NFL_Consistency_Index_Report.docx` | Full write-up: methodology, formula development, validation, results, discussion, limitations |
| `nci_full_dataset.csv` | Every team-season (2003–2023) with computed dimension scores, performance level, dispersion, and final NCI |
| `team_stats_2003_2023.csv` | Original source data (672 team-seasons, 35 variables, from pro-football-reference.com via Kaggle) |

## The question

Traditional NFL stats (wins, point differential) measure how *good* a team is. NCI instead measures how *balanced* a team's performance is across five dimensions of the game — independent of overall quality — and tests whether that balance predicts next-season success.

## How NCI is built

1. **Five dimensions**, each from standardized, direction-corrected component stats:
   - **Passing** — net yards/attempt, TD passes, interceptions (reversed)
   - **Rushing** — yards/attempt, rushing TDs, rush attempts
   - **Defense** — points allowed (reversed)
   - **Ball Security** — turnover % (reversed)
   - **Discipline** — penalty yards (reversed)
2. Each stat is **z-scored within its own season** (a team is compared to that year's league, not across eras).
3. Each dimension score = mean of its component z-scores.
4. **Performance Level** = mean of the five dimension scores (overall quality).
5. **Dispersion** = standard deviation across the five dimension scores (lower = more balanced).
6. **NCI** = dispersion converted to a 0–100 percentile rank, where 100 = most balanced. (NCI's own distribution is uniform by construction — see the report for the pre-scaling distribution, which has real shape.)

Dispersion and Performance Level are essentially uncorrelated (r ≈ -0.01), confirming NCI measures something distinct from "how good is this team."

## Key finding

**NCI does not meaningfully predict next-season win percentage.**

| Predictor | r with next-season win% |
|---|---|
| NCI | -0.081 (p = 0.04) |
| Current win % | 0.342 |
| Current point differential | 0.390 |

Adding NCI to a regression with win% and point differential improves R² by only 0.005, and out-of-sample testing (train 2003–2017, test 2018–2022) shows no meaningful reduction in prediction error. NCI also doesn't predict reduced year-over-year volatility or protection against decline.

This is a legitimate negative result: statistical balance, as constructed here, isn't the missing ingredient for forecasting NFL team trajectories — point differential and current record remain the stronger simple predictors.

## Reading the CSV

`nci_full_dataset.csv` columns:

- `year`, `team`, `wins`, `losses`, `win_loss_perc`, `points_diff` — from the original source
- `performance_level` — overall quality (mean of the 5 dimension z-scores)
- `dispersion_sd` — raw balance measure (lower = more balanced)
- `NCI` — final 0–100 consistency score (percentile rank of dispersion)
- `dim_passing`, `dim_rushing`, `dim_defense`, `dim_ball_security`, `dim_discipline` — the five underlying dimension scores

## Limitations

- Regular season only; no playoff data
- No opponent-adjusted (strength-of-schedule) stats
- Defense is a single variable (points allowed) — no opponent yardage data available
- No injury, roster continuity, or coaching-change data
- 2023 seasons excluded from prediction (no 2024 data to predict into)
- Dimension definitions involve judgment calls; a different reasonable grouping could yield a different formula

Full methodology, validation steps, and charts are in the `.docx` report.
