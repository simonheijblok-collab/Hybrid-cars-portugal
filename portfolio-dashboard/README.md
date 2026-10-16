# Rendement – portfolio performance dashboard

A single-file web app that shows how a stock portfolio really performs, inspired by the
"Ken je échte rendement" performance dashboard.

Open `index.html` in a browser. No build step, no server, no dependencies.

## What it shows

- **Return**: current value, total PnL, time-weighted return for the period (1M, 3M, 6M, YTD, 1Y, MAX),
  unrealized and realized PnL, net income (dividends + interest − fees), available cash and
  net deposits. The chart switches between return % (green above zero, red below) and value
  against net deposits.
- **Allocation**: donut by category (ETF, Stocks, Crypto, Commodities, Cash) and an open-positions table.
- **Income**: dividends and interest per month, plus every payment.
- **Realized**: every sale with average cost, proceeds and realized PnL.

## Your own data

Click **Import your data** and paste or choose a CSV:

```
date,type,ticker,quantity,price,amount,fee,name,category
2025-01-02,deposit,,,,5000,,,
2025-01-03,buy,VWRL,30,112.40,,1,Vanguard FTSE All-World,ETF
2025-03-25,dividend,VWRL,,,10.71,,,
2025-06-02,sell,VWRL,10,121.10,,1,,
2025-10-15,price,VWRL,,128.65,,,,
```

Types: `deposit`, `withdrawal`, `buy`, `sell`, `dividend`, `interest`, `fee`, `price`.
Dutch column names (`datum`, `aantal`, `koers`, `bedrag`, …), semicolons and decimal commas also work.
`price` rows set a quote for a date; between quotes the last known price is used.
Imported data is kept in the browser's local storage only.

## How the numbers are calculated

- Cost basis uses the **average cost** method, with trading fees added to cost (buys) or
  subtracted from proceeds (sells).
- **Period return** is time-weighted: each day's return is `(value − external flow) / previous value − 1`,
  chained over the period, so deposits don't inflate it.
- **Total PnL** = change in value − net deposits, which equals unrealized + realized + dividends + interest − fees.

## Files

- `dashboard.html` – the app (page body, styles and script).
- `build.sh` – wraps `dashboard.html` into the standalone `index.html`. Run it after editing.
