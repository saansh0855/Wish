# NIFTY / BANK NIFTY Algo Trading Starter

**Purpose:** broker-independent starter project for research, backtesting, and paper trading.  
**Safety default:** paper mode only. No broker credentials, live order route, or live-order implementation is included.

## Initial configurable parameters

- Instruments: NIFTY / BANK NIFTY option contracts (CE/PE)
- Signal timeframe: 5-minute candles
- EMA fast/slow: 12 / 26
- RSI period: 14
- CE signal: bullish EMA crossover and RSI > 55
- PE signal: bearish EMA crossover and RSI < 45
- Risk budget per trade: ₹250
- Daily loss limit: ₹1,000
- Maximum trades/day: 5
- Option premium stop-loss: 20% below entry premium for long option positions

These are starting assumptions for testing, not a profitable strategy claim. The signal is computed on the underlying index candle series; option contract selection and execution require a separate, validated contract master and market-data source.

## Project layout

- `backend/` — Python strategy, risk engine, paper broker, API and tests.
- `android/` — minimal Kotlin Android client shell with paper-mode status and kill-switch UI concept.
- `docs/IMPLEMENTATION_SPEC.md` — requirements, architecture, state machine, controls and acceptance criteria.
- `docs/COMPLIANCE_CHECKLIST.md` — checklist for broker/exchange/SEBI review before any live deployment.

## Run backend locally

Requires Python 3.10+.

```bash
cd backend
python -m venv .venv
# macOS/Linux:
source .venv/bin/activate
# Windows:
# .venv\Scripts\activate
pip install -r requirements.txt
pytest -q
uvicorn app.main:app --reload
```

API endpoints:
- `GET /health`
- `GET /config`
- `POST /signal` — evaluates a supplied candle series; does not place orders.
- `GET /paper/status`
- `POST /paper/kill-switch` — stops new paper entries and requests simulated position closure.
- `POST /paper/reset-kill-switch` — resets paper kill switch for testing only.

Example `POST /signal` body:
```json
{
  "symbol": "NIFTY",
  "candles": [
    {"timestamp":"2026-01-01T09:15:00+05:30","open":25000,"high":25010,"low":24990,"close":25005,"volume":1000}
  ]
}
```
At least 27+ candles are needed for a meaningful EMA crossover, and more history is recommended for stable RSI initialization.

## Important limitations

1. This is a starter scaffold, not a production-ready trading system or a compiled APK.
2. The backend's paper broker is a deterministic simulator, not a live exchange connection.
3. No real orders can be sent by this project as provided.
4. Backtests must model option premiums, bid/ask spread, slippage, charges, expiry/strike selection, partial fills and gaps. Underlying-index signals alone are not sufficient to claim options strategy performance.
5. A stop-loss order cannot guarantee its trigger price during gaps, illiquidity or fast markets.
6. Do not store broker passwords, API secrets or TOTP seeds in source code or the Android app. Use an approved secure server-side secret store if live integration is later authorized.
