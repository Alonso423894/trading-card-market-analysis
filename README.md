# Pokémon Card Price Prediction

A machine learning project that uses historical Pokémon card market data to predict prices approximately 30 days into the future.

The dataset combines historical price records with Pokémon card metadata. After data cleaning and entity matching, the final dataset contains more than 2.1 million price observations covering 14,689 unique cards.

The main goal is to test whether historical price patterns can outperform a simple baseline that assumes a card's future price will remain the same as its current price.

## Dataset

Historical price data was matched with Pokémon card metadata using card set and card number.

- 2.19M+ historical price observations
- 14,689 unique cards
- 126 sets
- 98.2% entity matching rate
- Price history from 2022–2024
- Multiple card conditions and print types

Card metadata includes features such as rarity, print type, and condition.

## Data Preparation

Pokémon card prices are highly skewed because a small number of cards have extremely high market values. Prices were therefore log-transformed before modeling.

Historical observations were sorted by card, condition, and date before creating time-based features.

The main features include:

- `prev_price` — previous observed price
- `days_since_prev` — days since the previous observation
- `rolling_avg_5` — average price over the previous five observations
- `rolling_std_5` — recent price volatility
- `momentum_5` — recent price momentum
- `current_log_price` — log-transformed current price
- `rarity`
- `print_type`
- `card_condition`

## Prediction Target

The target is the observed market price approximately 30 days after each historical observation.

Since price records are not available every day, future observations between 30 and 45 days were accepted as valid targets.

This resulted in approximately 1.5 million observations with valid prediction targets and 68.6% target coverage.

## Train-Test Split

A time-based split was used instead of a random train-test split.

Earlier observations were used for training and later observations were reserved for testing. This prevents future price information from leaking into the training data and provides a more realistic forecasting evaluation.

## Baseline

The naive baseline assumes that the future price of a card is equal to its current price.

| Metric | Baseline |
| --- | ---: |
| MAE | $170.89 |
| RMSE | $672.46 |
| Log MAE | 0.8269 |

## Model Results

Several feature configurations were tested. Model 2 produced the strongest overall results.

| Metric | Baseline | Model 2 | Change |
| --- | ---: | ---: | ---: |
| MAE | $170.89 | **$147.27** | **13.82% improvement** |
| Log MAE | 0.8269 | **0.6867** | **16.95% improvement** |
| RMSE | $672.46 | $929.43 | 38.21% worse |

The model improves both MAE and Log MAE, but performs worse on RMSE.

Further analysis showed that this difference is mainly caused by a small number of very expensive cards with unusually large prediction errors. Because RMSE squares individual errors, these observations have a much larger effect on the metric.

## Performance by Price Range

| Card Price | Baseline MAE | Model MAE | Improvement |
| --- | ---: | ---: | ---: |
| $0–100 | $84.82 | **$36.40** | **57.09%** |
| $100–500 | $105.62 | **$84.25** | **20.24%** |
| $500–1K | $234.89 | **$210.23** | **10.50%** |
| $1K–5K | $516.61 | **$428.16** | **17.12%** |
| $5K–10K | $1,856.65 | **$1,670.09** | **10.05%** |
| $10K–50K | $3,917.14 | $5,689.78 | -45.25% |
| $50K+ | $17,508.02 | $63,962.93 | -265.33% |

The model performs well across most lower and mid-priced cards, with the largest improvement occurring in the $0–100 range.

Performance becomes less reliable above $10,000, where the number of observations is much smaller and individual price movements are more extreme.

## Feature Importance

Permutation importance was used to examine which variables contributed most to the final model.

The most important features were:

1. Rolling 5-observation average
2. Current log price
3. Rolling price volatility
4. Price momentum
5. Previous price

Recent historical pricing behavior was much more useful for prediction than static characteristics such as rarity, condition, and print type.

## Main Findings

Historical pricing information improved 30-day price predictions compared with the naive baseline.

The final model reduced MAE by 13.82% and Log MAE by 16.95%. Performance was particularly strong for cards below $100, where MAE improved by approximately 57%.

The results also show that Pokémon card price prediction becomes much more difficult for very high-value cards. These cards are relatively rare in the dataset and can experience much larger price movements.

## Limitations

- Historical price observations are not evenly spaced.
- Some cards have much more price history than others.
- Very high-value cards have relatively few observations.
- External factors such as market trends, new releases, competitive play, and collector demand are not included.
- The available historical period is relatively short.

## Possible Improvements

Future versions could include broader market trends, card age, set release dates, Pokémon popularity, and additional card-level features.

Separate models for high-value cards could also be explored because their price behavior appears substantially different from the rest of the market.

## Tools

- Python
- pandas
- NumPy
- scikit-learn
- Matplotlib
- Google Colab
- Git / GitHub

## Repository

The main notebook contains the complete workflow:

```text
pokemon-card-price-prediction.ipynb
```

It includes data preparation, entity matching, exploratory analysis, feature engineering, target construction, model training, evaluation, and feature importance analysis.
