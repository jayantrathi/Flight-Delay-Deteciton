# Flight Delay Prediction

Predicting whether a U.S. flight will be delayed more than 15 minutes by combining BTS
flight data with real weather data, and figuring out which scheduling and weather factors
actually drive the delays. A Random Forest reached F1 ≈ 0.76 and ROC-AUC ≈ 0.85, with time
of day, route, and weather all coming through as meaningful predictors.

## Data

- **Flights:** BTS On-Time Performance data, 2019 Q1.
- **Weather:** Iowa State Environmental Mesonet, around the top 20 airports.
- **Merge:** joined on airport location and timestamp, converted to UTC.
- The raw datasets aren't in the repo (too large).

## Approach

Cleaning and feature work:

- Dropped leakage features like `DEP_DELAY`.
- Aggregated weather into 30-minute windows and merged on airport + time.
- Built temporal features (departure hour, weekend flag, peak-hour flag) and weather
  features (temperature, wind speed, precipitation, pressure), plus route and categorical
  encodings.
- Used random oversampling to balance delayed vs non-delayed flights.

Models tried: Logistic Regression, Decision Tree, Random Forest, Extra Trees, Gradient
Boosting, AdaBoost, and Linear SVM.

## Results

| Model | Accuracy | Precision | Recall | F1 | ROC-AUC |
|---|---|---|---|---|---|
| **Random Forest** | 0.764 | 0.797 | 0.707 | **0.749** | **0.852** |
| Decision Tree | 0.738 | 0.746 | 0.721 | 0.733 | 0.821 |
| Extra Trees | 0.731 | 0.770 | 0.660 | 0.711 | 0.817 |
| Gradient Boosting | 0.653 | 0.660 | 0.631 | 0.645 | 0.712 |
| Logistic Regression | 0.632 | 0.638 | 0.611 | 0.624 | 0.682 |
| Linear SVM | 0.632 | 0.639 | 0.609 | 0.624 | N/A |
| AdaBoost | 0.622 | 0.629 | 0.592 | 0.610 | 0.674 |

Random Forest gave the best precision/recall balance, so I used it as the final model
(F1 ≈ 0.76, ROC-AUC ≈ 0.85).

## What the data showed

**Time of day**

![Delay vs Time](delay_vs_time.png)

Delay probability climbs during peak travel hours, so congestion is a big part of it.

**Weather**

![Wind vs Delay](wind_vs_delay.png)
![Temperature vs Delay](temp_vs_delay.png)
![Dewpoint vs Delay](dewpoint_vs_delay.png)
![Gust vs Delay](gust_vs_delay.png)

Adverse weather lines up with delays, with wind and precipitation features mattering most.

**Airport-specific rain**

![Airport Rain Heatmap](airport_rain_heatmap.png)

Some airports are far more sensitive to rain than others; it amplifies delay probability at
the major hubs.

## Takeaways

- Time of day and weather are the strongest drivers.
- Tree-based models beat the linear ones, which fits since the relationships are nonlinear.
- Handling the class imbalance was necessary to get realistic performance.

## Running it

```bash
pip install -r requirements.txt
jupyter notebook Project.ipynb
```

Raw data isn't included (size), so the data paths may need adjusting for a local run.

## Next

- Add real-time flight tracking and air-traffic congestion metrics.
- Try sequence models.

---

Jayant Rathi, MS Robotics, Northeastern University. Done for EECE 5644, Spring 2026.
