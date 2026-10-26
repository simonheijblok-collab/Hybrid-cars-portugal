# IBKR Dividend Tracker

Live dashboard on claude.ai (Dashboard artifact): https://claude.ai/artifact/G1ihu9iHf4xMhAfYDtuViK

`index.html` is the page stored in the dashboard (`files/index.html`); the data comes live from the
Interactive Brokers connector with each reader's own access.

## Sources (live IBKR calls)

| Source | Tool |
|---|---|
| Account summary | `get_account_summary` |
| Open positions | `get_account_positions` |
| Cash and exchange rates | `get_account_balances` |
| Account value history | `get_pa_performance_all_periods` |
| Trades, past 15 months | `get_account_trades` (this year + the four completed quarters before) |
| Dividend history per holding | `get_account_positions`, then `get_price_history` with corporate actions per position |

## How the dividend figures are worked out

- **Paid / announced**: each cash dividend with an ex-date in the past 12 months or later, times the
  shares held on the ex-date (today's position minus trades from that day on).
- **Forecast**: each payment of the past 12 months repeated a year later, scaled by the latest
  amount per share over the year's median (handles rate cuts and raises), for the shares held now.
  Announced payments replace a forecast that falls within 10 days of them.
- Amounts are converted to the base currency at today's rate from IBKR balances; **net** assumes
  15% US withholding tax (W-8BEN treaty rate) on US dividends.
