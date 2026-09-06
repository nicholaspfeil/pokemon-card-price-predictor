# Pokemon Card Price Predictor

A web app that forecasts Pokémon card price trends 15–90 days into the future using a trained ML model, and helps collectors find the best current deals.

🔗 **Live app:** [pokemon-predictor-fork.vercel.app](https://pokemon-predictor-fork.vercel.app)

## Features
- **Price forecasting** — predicts price movement for a card over a user-selected window (15–90 days out)
- **Portfolio tracking** — users can save cards to track predicted value over time
- **Live deal finder** — pulls real-time eBay listings and surfaces the top 3 best current prices for a given card
- **Quick buy** — direct link to the card's TCGplayer page
- **Filtering** — sort/filter cards by Pokémon type

## How it works
- Historical price data pulled from the PokemonPriceTracker API, cross-referenced with card metadata from pokemontcg.io
- An XGBoost regression model, trained and validated (cross-validation) on historical price data, generates the forecasts
- Live deal data comes from the eBay API
- Model training and data work done in Google Colab; deployed as a static site via Vercel

## Tech stack
Python, pandas, scikit-learn, XGBoost, JavaScript, HTML/CSS, Vercel

## Team
Built collaboratively by [Nicholas Pfeil](https://github.com/nicholaspfeil) and [Dominic](https://github.com/d3au-4)) as a final project for UCSD SPIS, with even split of work across the ML pipeline and frontend/deal-finder features.
