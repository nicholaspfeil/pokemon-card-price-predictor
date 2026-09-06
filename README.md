# Pokemon Card Price Predictor

A web app that forecasts Pokémon card price movement 15–90 days out using trained ML models, and surfaces the best current deals for a card across eBay and TCGplayer.

**Live app:** [pokemon-predictor-fork.vercel.app](https://pokemon-predictor-fork.vercel.app)

## Features

- **Price forecasting** — predicts percent price change over a user-selected horizon (15, 30, 60, or 90 days), with a separate model trained for each horizon
- **Portfolio tracking** — save cards and track their predicted value
- **Live deal finder** — queries the eBay API and surfaces the three best current listings for a card
- **Quick buy** — direct link to the card's TCGplayer page
- **Filtering** — browse and filter by Pokémon type

## How it works

**Data pipeline.** Pulls ~180 days of daily Near Mint price and volume history for thousands of cards from the PokemonPriceTracker API, set by set, with retry/backoff handling for rate limits and server errors. Saves incrementally so a mid-run failure doesn't lose progress.

**Feature engineering.** For each card, builds 12 features from the price history before a cutoff date: short- and medium-term momentum (7/30/60-day), volatility, trend acceleration, trading volume and volume trend, price level, rarity, market maturity, position within the card's own historical range, and long-run drift.

**Training and validation.** A gradient-boosted regressor (XGBoost, with a scikit-learn GradientBoosting fallback) is trained per horizon. Performance is measured with 5-fold cross-validation and compared against a naive "no change" baseline — if the model can't beat predicting zero, it isn't learning anything useful.

**Scope of the reported accuracy.** Models are trained and cross-validated on a filtered subset of liquid, higher-value cards (≥160 days of history, ≥$8 median price, ≥2 average daily volume, and below a volatility ceiling). Predictions are then generated for the broader catalog using a more permissive filter, so the cross-validated accuracy describes performance on the training subset rather than on every card shown in the app. Forecasting card prices is genuinely hard; the model captures modest signal on liquid cards and should be read as a directional indicator, not a price guarantee.

**Deployment.** Model training and data work run in Google Colab. Predictions are exported to `card_predictions.json`, which the static front end reads directly. Deployed on Vercel.

## Repo layout

| Path | What it is |
|---|---|
| `PredictionML.ipynb` | Data pipeline, feature engineering, model training and validation |
| `Ebay_Best_Card_Deal.ipynb` | Live eBay listing lookup for the deal finder |
| `card_predictions.json` | Generated predictions consumed by the front end |
| `index.html` | Front end |
| `api/` | Serverless endpoints |
| `experiments/` | Earlier model iterations (v1–v3), kept to show how the approach evolved |

## Tech stack

Python, NumPy, scikit-learn, XGBoost, JavaScript, HTML/CSS, Vercel

## Running it yourself

API keys are read from Colab Secrets (`userdata.get`) rather than hardcoded. To reproduce:

1. Add a PokemonPriceTracker API key to Colab Secrets as `PokeData`
2. Run the data pipeline cell to generate `price_history_dataset.json`
3. Run the training cell to fit and validate the models
4. Run the prediction cell to regenerate `card_predictions.json`

## Team

Built by [Nicholas Pfeil](https://github.com/nicholaspfeil) and [Dominic](https://github.com/d3au-4) as a final project for UCSD SPIS, with work split evenly across the ML pipeline and the front end / deal finder.
