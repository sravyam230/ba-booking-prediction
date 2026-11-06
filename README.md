# British Airways – Lounge Eligibility and Booking Prediction

Work completed for the [British Airways Data Science Job Simulation](https://www.theforage.com/simulations/british-airways/data-science-yqoz) on Forage (February 2026).

The simulation has two tasks. Task 1 is a modelling exercise in Excel; Task 2 is a machine learning task in Python. The datasets provided by Forage are for simulation use only and are not included here; the column descriptions below are enough to follow the approach.

## Task 1 – Modelling lounge eligibility at Heathrow Terminal 3

**Brief.** BA's Airport Planning team wanted to estimate how many passengers on a future schedule would be eligible for each of three lounge tiers (Concorde Room, First Lounge, Club Lounge) without knowing exact flight numbers or aircraft. The deliverable was a reusable lookup table of eligibility percentages by flight grouping, plus a written justification.

**Data.** A summer schedule of around 10,000 departures from LHR with departure time, time-of-day band, arrival country and region, haul (short or long), aircraft type, and seat counts by cabin.

**Approach.**
- Grouped flights on two features that are known for any future schedule: route type (short-haul vs long-haul) and time of day (early, mid-day, evening). This gives six categories and avoids depending on aircraft type or specific destinations, which change.
- Estimated the share of passengers in each category eligible for each tier, with Tier 3 (Club) the largest because it includes business class and Silver members, Tier 2 (First) smaller and concentrated on long-haul, and Tier 1 (Concorde) very small, reflecting First Class and top-tier loyalty only.
- Applied the percentages to a sample of the schedule to sanity-check the implied lounge numbers.

**Output.** [`lounge_eligibility_lookup.csv`](lounge_eligibility_lookup.csv)

| Grouping | Tier 1 % | Tier 2 % | Tier 3 % | Notes |
|---|---|---|---|---|
| Short-haul Early | 2 | 8 | 20 | Low premium, some business travel |
| Short-haul Mid-day | 1 | 6 | 15 | Leisure-heavy mix |
| Short-haul Evening | 1 | 7 | 18 | After-work business travel |
| Long-haul Early | 5 | 15 | 40 | High business and premium mix |
| Long-haul Mid-day | 4 | 12 | 35 | Balanced premium/leisure mix |
| Long-haul Evening | 6 | 18 | 45 | Peak premium and loyalty usage |

**Why this scales.** The model only needs departure time and route type, so it can be applied to any future schedule regardless of aircraft or destination changes.

## Task 2 – Predicting customer buying behaviour

**Brief.** Build a model that predicts whether a booking enquiry will be completed and explain which variables drive that outcome, then summarise the findings on one slide for a manager.

**Data.** 50,000 enquiries with 13 features: passengers, sales channel, trip type, lead time, length of stay, flight hour and day, route, booking origin, three add-on flags and flight duration. About 15% of enquiries complete, so the target is imbalanced.

**Approach.** [`booking_prediction.ipynb`](booking_prediction.ipynb)
- Explored the data (no missing values, 719 duplicate rows removed, 799 distinct routes, 104 booking origins) and built a preprocessing pipeline with imputation and one-hot encoding.
- Trained a class-weighted random forest as a baseline and evaluated it with five-fold stratified cross-validation on accuracy, precision, recall, F1 and ROC-AUC, then on a held-out test split.
- Diagnosed the baseline: accuracy of about 0.85 matched the 85% majority class and recall on bookers was low, because fully grown trees on roughly 900 one-hot columns were overfitting.
- Second iteration: dropped `route`, grouped rare booking origins, capped tree depth and leaf size. Recall rose to about 0.70 at ROC-AUC 0.76, with precision 0.29, a deliberate trade-off for a marketing use case.
- Used feature importances to identify the main drivers and summarised the results on one slide for a non-technical manager.

**Results.** Lead time, length of stay and flight hour are the strongest drivers in the baseline; once route is removed, the origin market comes to the front (Malaysian enquiries convert at 34% against 5% for Australia). Add-ons carry moderate signal; sales channel and day of week very little.

## Tools

Excel, Python (pandas, scikit-learn, matplotlib)
