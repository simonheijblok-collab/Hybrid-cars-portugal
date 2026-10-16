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

## Live from Interactive Brokers

When the page is opened as a claude.ai artifact, **Connect IBKR** reads your account through your
Interactive Brokers connector in claude.ai (read-only: account summary, positions, balances,
performance history, asset-class allocation and this year's trades). claude.ai asks for permission
the first time. No credentials or account data are stored in the page; it remembers only that you chose IBKR,
and loads the account again on your next visit if access is still allowed.

What IBKR provides and how it is used:

- **Return and chart**: IBKR's own performance series (`7D`, `MTD`, `1M`, `YTD`, `1Y`), time-weighted
  unless your account is set to money-weighted. Deposits and withdrawals are derived from each day's NAV
  change beyond that day's return, which gives total PnL and net deposits.
- **Positions**: quantities, average cost and unrealized PnL; amounts in other currencies are converted
  to your base currency at today's rate.
- **Realized**: this year's trades (currency conversions left out), with IBKR's realized PnL for sales.
- **Income**: the connector only reports a dividend total, not individual payments, so the monthly chart
  needs a CSV import.

## DEGIRO

Export **Transacties** (Transactions) and **Rekeningoverzicht** (Account statement) from DEGIRO as CSV,
from the day the account was opened, and choose both files at once under **Import your data**.

- Buys and sells come from Transactions, using DEGIRO's euro totals (AutoFX and transaction costs included).
- Deposits and withdrawals come from the iDEAL, SEPA and flatex lines in the account statement. Internal
  cash-sweep transfers, currency conversions and trade lines are skipped (they are already in Transactions).
- Dividends are netted with their dividend tax and converted to euros at DEGIRO's own exchange rate.
- Daily market prices: the page looks up each ISIN through the Interactive Brokers connector
  (`search_contracts`, `get_price_history`), keeps a listing only when its prices match what you paid,
  and converts US listings at each day's EUR/USD rate. Without the connector, values use your last trade prices.
- The Return tile shows the time-weighted return and, next to it, the money-weighted return (Modified Dietz).

## All accounts

With DEGIRO (or another import) loaded and IBKR connected, an account switch appears: **All accounts**,
**DEGIRO** and **IBKR**. All accounts adds both daily value series and their deposits together and computes
one time-weighted and money-weighted return over the combined portfolio. Positions and sales are listed
with the account they belong to. IBKR's dividend total is included in net income; monthly income shows
DEGIRO payments only, because the IBKR connector does not report individual payments.

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
