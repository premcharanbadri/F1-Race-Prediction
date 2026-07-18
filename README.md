# Predicting Formula 1 Race Outcomes (2022-2025)

A data science project applying predictive modeling to Formula 1 race strategy, built to answer a practical question for race teams and strategists: which factors actually drive race results, and which ones are noise.

## Business Problem

Race teams operate with finite resources: engineering hours, strategy software development, pit crew training, and weather contingency planning all compete for the same budget and attention. Without a data-backed view of what actually predicts race outcomes, that investment can end up misallocated, for example, over-preparing for weather scenarios that rarely change results, while underinvesting in the qualifying and pit execution work that consistently does.

This project addresses that gap. Using three seasons of driver-level race data (2022-2024), I built a set of models to identify which pre-race and in-race factors most reliably predict podium finishes, final position, and points scored, then tested those models against a real 2025 race to see how well they held up.

## Business Objective

Give race strategists and analysts a data-backed ranking of which levers, qualifying performance, pit execution, tire management, and weather, actually move the needle on race outcomes, so strategic investment and race-day decision-making can be directed at the highest-leverage areas rather than spread evenly across all of them.

## Key Business Findings

**Qualifying performance is the dominant driver of race outcomes.** Across every model I built, qualifying position and pace consistently ranked as the strongest predictors of finishing position, points, and podium probability. The practical implication: Saturday setup and qualifying strategy deserve outsized investment relative to race-day contingency planning, since the qualifying result sets the ceiling for what a driver can realistically achieve on Sunday.

<kbd> <img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/d269bf37-9fb9-4fe2-b5c8-07e1369483eb" /> </kbd>


**Pit stop discipline correlates strongly with strong finishes.** Drivers who finished on the podium pitted once, on average, compared to two stops for non-podium finishers. This supports continued investment in pit crew execution and strategy software that favors fewer, well-timed stops over reactive multi-stop strategies.

**Weather has minimal predictive value, with one exception.** Precipitation, track temperature, humidity, and wind showed almost no difference between podium and non-podium finishers once qualifying and pace were accounted for. Simulated adjustments to these variables moved podium probability by only a few percentage points in most cases. Atmospheric pressure was the one variable that showed a measurable effect (podium probability roughly doubled between high-pressure and low-pressure conditions), and it is worth monitoring, but the broader takeaway is that weather-contingency resources are likely better spent elsewhere in most conditions.

**Model confidence is strong enough to support decisions, not certainty.** The podium classifier achieved a ROC-AUC of 0.939, meaning it correctly ranks podium versus non-podium outcomes nearly 94% of the time, and captured 87% of actual podium finishes in validation. The finishing position model (Random Forest) had a mean error of about 2.65 positions, and the points model (XGBoost) had a mean error of about 3.23 points. These numbers support using the models as a decision aid for pre-race planning and scenario comparison, not as a guarantee of any single outcome.

<kbd><img width="700" height="350" alt="image" src="https://github.com/user-attachments/assets/d765a167-ed10-4bbf-a99e-51d2cc7e31a6" />
</kbd>

*The model's value also shows up in ranking, not just classification. Looking only at the top 10% of predicted podium probabilities, drivers in that group are about six times more likely to actually finish on the podium than an average driver in the dataset. This means the model can be used to prioritize attention on a small number of high-probability candidates each race weekend rather than treating every entry as equally uncertain.*

## How This Supports Decisions

- **Pre-weekend risk assessment**: Run a driver's qualifying and pace profile through the podium classifier to get an early read on realistic podium chances before the race, useful for setting expectations with sponsors or media.
- **Strategy scenario comparison**: Use the finishing position and points models to compare the expected outcome of different pit strategies (one-stop versus two-stop) given a driver's actual pace and tire data.
- **Points target setting**: Use the points model's output to set realistic season-long points targets based on a driver's demonstrated qualifying and race pace, rather than optimistic assumptions.
- **Weather contingency prioritization**: Use the what-if simulation results to decide how much weight weather forecasts should actually carry in pre-race strategy meetings, and where that planning time might be better spent.

## Limitations and What They Mean for Reliance on This Tool

The dataset covers only the 2022-2024 seasons, a period defined by a specific technical regulation era. As Formula 1 introduces new car or sporting regulations, these models will need to be retrained, since patterns learned under the current ground-effect rules may not hold.

The features used are race-level summaries (average pace, total pit time, a single weather snapshot) rather than lap-by-lap data. This means the models cannot capture moment-to-moment events like a well-timed safety car or a single costly mistake, the kind of moments that often decide real races. Strategists should treat these models as a guide to typical patterns, not a predictor of any single race's chaos.

Some missing data (incomplete qualifying laps, missing pit times) was filled in using conservative assumptions that treat missing values as underperformance. This is a reasonable default but can understate the performance of a driver who had a mechanical issue or incomplete data through no fault of strategy.

Finally, these models identify correlation, not causation. They can tell you that fewer pit stops are associated with podium finishes, but they cannot tell you that reducing pit stops will cause a podium finish for a given driver in a given race. Strategic decisions should still be made by a human strategist using this output as one input among several, not as an automated recommendation.

## Methodology

I built a driver-level dataset combining official race data (grid position, qualifying results, classifications) with detailed race telemetry (lap pace, tire stint length, pit timing, and mid-race weather) for every Grand Prix from 2022 to 2024. After cleaning and standardizing this data, I trained and compared several models: logistic regression for podium prediction, and Random Forest, XGBoost, and ordinal regression for finishing position and points. Models were trained on 2022-2024 data in chronological order and validated on held-out races, then tested against the actual 2025 Australian Grand Prix results. I also built a scenario simulation tool to isolate the effect of individual weather variables while holding all other race conditions constant, which produced the weather findings above.

## Model Performance

<kbd><img width="700" height="550" alt="image" src="https://github.com/user-attachments/assets/1f80f03f-5655-4b46-b120-3c2665b8363b" /></kbd>

<kbd><img width="700" height="600" alt="image" src="https://github.com/user-attachments/assets/535179eb-83e4-4466-a7e1-559881fe68e9" />
</kbd>


## Data Sources and Tools

- Jolpica. (2024). *Ergast F1 Public API via Jolpica*. https://api.jolpi.ca/ergast/
- FastF1 Documentation. (2024). *API reference and known issues*. https://fastf1.readthedocs.io
- Pedregosa, F., et al. (2011). Scikit-learn: Machine Learning in Python. *Journal of Machine Learning Research*, 12, 2825-2830. https://scikit-learn.org
- Chen, T., & Guestrin, C. (2016). XGBoost: A Scalable Tree Boosting System. *Proceedings of the 22nd ACM SIGKDD International Conference on Knowledge Discovery and Data Mining*. https://xgboost.readthedocs.io
- Lundberg, S. M., & Lee, S.-I. (2017). A Unified Approach to Interpreting Model Predictions. *Advances in Neural Information Processing Systems*, 30. https://shap.readthedocs.io
