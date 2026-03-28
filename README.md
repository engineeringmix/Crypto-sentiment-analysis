# Crypto Sentiment & Trader Behavior Analysis
**Platform:** Hyperliquid | **Index:** Fear & Greed

---

## What This Project Does

This project analyzes how **market sentiment (Fear vs Greed)** affects trader behavior and performance on the Hyperliquid decentralized exchange.

It answers three questions:
1. Do traders make more money on Fear days or Greed days?
2. Do traders change how they trade based on market mood?
3. Can we predict if a trader will be profitable tomorrow?

---

## Dataset

| File | Description | Rows |
|---|---|---|
| `historical_data.csv` | Every trade on Hyperliquid (trader, size, PnL, timestamp) | ~100k+ |
| `fear_greed_index.csv` | Daily Fear & Greed score (0–100) with classification | Daily |

**Source:** Kaggle — `engineeringmix/stock-market-dataset`

---

## How to Run

### Step 1 — Clone the repo
```bash
git clone https://github.com/YOUR_USERNAME/Crypto-sentiment-analysis.git
cd Crypto-sentiment-analysis
```

### Step 2 — Install required libraries
```bash
pip install pandas numpy matplotlib seaborn scikit-learn
```

### Step 3 — Add the datasets
Place both CSV files at this path (Kaggle input format):
```
/kaggle/input/datasets/engineeringmix/stock-market-dataset/historical_data.csv
/kaggle/input/datasets/engineeringmix/stock-market-dataset/fear_greed_index.csv
```
If running locally, update the `HL_PATH` and `FG_PATH` variables in Cell 2 of the notebook to your local file paths.

### Step 4 — Run the notebook
Open `crypto-sentiment-trader-analysis.ipynb` in Jupyter or Kaggle and run all cells top to bottom.

---

## Project Structure

```
Crypto-sentiment-analysis/
│
├── crypto-sentiment-trader-analysis.ipynb   ← Main analysis notebook
├── README.md                                ← This file
├── Images                                   ← Visual output
```

---

## Methodology

**1. Data Cleaning & Alignment**
- Loaded both datasets and documented shape, missing values, and duplicates
- Converted Hyperliquid timestamps (milliseconds) and Fear/Greed timestamps (seconds) into matching UTC dates
- Merged both tables on date — every trade row now carries the sentiment for that day

**2. Metric Creation**
Built daily metrics per trader:
- Daily PnL (sum of all trade profits/losses that day)
- Trade count, win rate, average position size
- Long/Short ratio (buy volume ÷ total volume)
- Drawdown proxy (cumulative minimum PnL per trader)

**3. Segmentation**
Traders split into 3 meaningful groups:
- **Whale vs Plankton** — top 25% position size vs the rest
- **High-Freq vs Low-Freq** — top 25% daily trade count vs the rest
- **Consistent Winner vs Inconsistent** — positive vs negative total PnL over the period

**4. Analysis**
- Compared PnL, win rate, and drawdown across all 5 sentiment phases
- Tracked behavior shifts (trade count, position size, long/short bias) across sentiment
- Visualized segment performance using bar charts and line plots

**5. Predictive Model**
- Target: Will this trader be profitable tomorrow? (1 = Yes, 0 = No)
- Used `.shift(-1)` on PnL to create the next-day label
- Features: sentiment score, trade count, win rate, avg trade size, long ratio, daily PnL
- Model: Random Forest Classifier (200 trees, max depth 8, class-balanced)
- Split: 80% train / 20% test

---

## Key Insights

**Insight 1 — Fear days produce better returns**
Average daily PnL is higher during Fear phases compared to Greed phases. Extreme Greed is associated with the worst drawdowns — especially for Plankton (small) traders who over-extend during euphoria.

**Insight 2 — Traders overtrade in Greed**
Trade count and position size both increase significantly during Greed and Extreme Greed. However, win rate does NOT improve — meaning more trades does not mean more profit. Traders are chasing the market, not reading it.

**Insight 3 — Segments behave differently**
Whales maintain consistent performance across all sentiment phases. High-Freq traders see the biggest drop in win rate during Extreme Greed. Consistent Winners sustain above-50% win rates even during Fear, while Inconsistent traders collapse below 50% during Extreme Fear.

---

## Strategy Recommendations

**Strategy 1 — "The Sentiment Brake" (Position Sizing Rule)**
> During Extreme Greed (F&G score > 75): Plankton traders should reduce maximum position size by 30% and cap leverage at 2x. Data shows they over-extend during euphoria and suffer the largest drawdowns in the correction that follows.

> During Extreme Fear (F&G score < 25): Consistent Winners should increase buy allocation by 20%. Fear phases historically offer better entry points for disciplined traders.

**Strategy 2 — "Frequency Filter" (Trade Volume Rule)**
> During Greed phases: High-Freq traders should enforce a daily trade cap of median trade count × 1.2. Exceeding this limit requires a stricter entry condition. Overtrading in euphoric markets inflates transaction costs without improving win rate.

> Low-Freq traders performing well during Fear should maintain their cadence — their selectivity is their structural edge. Do not change what is working.

---

## Output Charts

| Chart | What it shows |
|---|---|
| `chart1_performance_vs_sentiment.png` | Avg PnL, Win Rate, Drawdown across sentiment phases |
| `chart2_behavior_vs_sentiment.png` | Trade count, position size, long/short ratio shifts |
| `chart3_segment_analysis.png` | Whale vs Plankton, High-Freq vs Low-Freq, Winners vs Inconsistent |
| `chart4_model_results.png` | Feature importance + confusion matrix for predictive model |

---

## Author

**Mohanraj** — Founder, EngineeringMix | 
BCA Data Science | Mechanical Engineering Diploma
Chennai, Tamil Nadu, India
