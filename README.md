# Is the Weather to Blame for My BF's Headaches?

**Sounds plausible. But data first.**

My boyfriend gets recurring headaches and wondered whether the weather might play a role. I combined his headache diary with daily weather data to explore the question.

This is a **single-person observational study**, not an A/B test, a medical diagnosis, or a validated prediction model.

## Research question

Is there an association between daily weather conditions and the occurrence of headaches in this person's records?

## Data

- **Headache diary:** exported from the Migrebot Telegram bot.
- **Weather:** daily historical data from [Open-Meteo](https://open-meteo.com/en/docs/historical-weather-api) for Batumi, Georgia, using the `Asia/Tbilisi` time zone.
- **Period:** May 3 to October 25, 2026.
- **Unit of analysis:** one calendar day.

| Measure | Value |
| --- | ---: |
| Days analysed | 176 |
| Days with a headache | 38 |
| Days without a headache | 138 |
| Share of days with a headache | 21.6% |

The original diary contained 156 recorded dates. The participant confirmed that he skipped only headache-free days and recorded every headache day. Therefore, the 20 unlogged dates within the observation period were coded as **no headache**. This is a self-reported assumption, not independently verified ground truth.

The analysis uses a binary outcome: `headache = 1` for a headache day and `0` otherwise. It does not distinguish headache intensity, attack onset, or different headache types.

## Analysis workflow

1. Parse dates, check duplicates and missing values, and merge the datasets by date.
2. Examine headache frequency, monthly patterns, and weather distributions.
3. Run preliminary comparisons between headache and non-headache days.
4. Apply Holm adjustment to the complete family of 17 screening tests.
5. Describe wind direction patterns and run a follow-up model that includes a simple time trend.

### Statistical methods

- **Welch t-tests:** compare means for 15 selected quantitative weather variables.
- **Wheeler–Watson test:** compare circular wind direction distributions using the original angles.
- **Pearson's chi-square test:** assess the association between headache occurrence and three weather categories: clear/cloudy, drizzle, and rain.
- **Holm adjustment:** account for multiple comparisons across these 17 tests, using a significance level of **0.05**.

Weather codes were treated as categories, not continuous measurements. Duplicate columns and constant snow variables were excluded from screening. Wind directions were also grouped into eight compass sectors for descriptive summaries only.

The screening tests are **preliminary**: their usual independence assumptions may not hold for consecutive daily observations. Holm adjustment does not correct temporal dependence or seasonal confounding.

## Results

### Initial screening

Wind direction was the only screening result that remained statistically significant after Holm adjustment:

- Raw p-value: approximately **0.00000129**.
- Holm-adjusted p-value: approximately **0.0000218**.

The other 16 adjusted p-values exceeded 0.05. This means there was insufficient evidence to reject their null hypotheses under the screening methods used, **not that the variables were shown to have no association with headaches**.

Descriptively, south and southeast winds made up a larger share of headache days, while west winds made up a smaller share. These patterns were explored after viewing the data and are not independent confirmations.

### Follow-up analysis of wind direction

A binary logistic regression represented wind direction with sine and cosine terms and included a linear and quadratic time trend. HAC standard errors were used with a primary maximum lag of 7 days; lags of 3 and 14 days were sensitivity checks.

A joint Wald test of the two wind terms yielded:

| HAC maximum lag | Joint p-value |
| --- | ---: |
| 3 days | 0.154 |
| **7 days — primary setting** | **0.205** |
| 14 days | 0.194 |

The follow-up model did not provide sufficient evidence of a smooth circular association between wind direction and headache occurrence.

This model and the initial circular test examine different hypotheses under different assumptions. The change in results **cannot be attributed solely to adjustment for time**. The follow-up is exploratory and is not an independent validation of the initial finding.

## Conclusion

**An interesting clue, not a firm verdict.**

The initial wind direction result was not consistently supported by the follow-up model. These analyses do not establish a reliable weather–headache relationship for this person, but they also do not demonstrate that weather is irrelevant.

The project does not identify a cause of headaches or produce a validated forecast of future headache days.

## Limitations and next steps

- Only one person and 176 days were observed, including 38 headache days.
- Unlogged diary dates were interpreted using the participant's recollection.
- Consecutive days may belong to the same headache episode and may have related weather conditions.
- A simple quadratic trend is not a complete adjustment for seasonal or other time-related differences.
- Daily weather summaries can include hours after a headache began, limiting interpretation as preceding exposures.
- The diary does not capture sleep, stress, medication use, strong smells, physical activity, or other potentially relevant context.
- Follow-up analyses were selected after data exploration, so their results are hypothesis-generating rather than confirmatory.

A useful next step is a richer diary with headache onset, duration, intensity, associated symptoms, medication, sleep, and activities or exposures before the pain started. Any predictive model would need evaluation on later observations not used during development.

## Tools and reproduction

Python, Jupyter, pandas, NumPy, Matplotlib, Seaborn, SciPy, statsmodels, and pycircstat2.

To reproduce the notebook analysis in a local environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install jupyter pandas numpy matplotlib seaborn scipy statsmodels pycircstat2
jupyter notebook
```

Open the analysis notebook, update the input file paths to your local diary and weather exports, and run the cells from top to bottom. The weather CSV export used in this analysis has three metadata rows before its header and is loaded with `skiprows=3`.

## Privacy

This project concerns personal health information. Share raw diary records only with the participant's explicit permission, and remove identifying details before publication. If raw records are not shared, reproducing the exact numerical results requires access to the underlying data.

*Personal analytics project. Not medical advice.*
