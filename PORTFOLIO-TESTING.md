# Walter: Testing & Validation

## Test Strategy

Unit Tests: Trading logic (RSI, VWAP, conviction, cooldown)
Integration Tests: Agent decision-making with market data
Live Paper Trading: Validate strategy on Alpaca paper account
Backtesting: Historical performance validation

## Test Coverage

Trading Logic: ~50 tests
- RSI calculation (edge cases, smoothing periods)
- VWAP calculation (different time intervals)
- Conviction scoring (30-day accumulation, weighting)
- Cooldown tracking (per-symbol state, 45-min window)
- Stop-loss logic (8% threshold, recovery)

Civic Scrapers: ~30 tests
- Family court: Storage updates, judge bias detection
- Property records: Deduplication, valuation parsing
- Pattern detection: Judge stats, GAL concentration, outliers

Publisher: ~20 tests
- Signal aggregation (fusing 3 agents)
- Approval workflow (human override)
- Content generation (brief formatting)

Total: ~100 tests, focus on correctness (trading >90% coverage)

## Paper Trading Validation

Process:
1. Run strategy on Alpaca paper account (real market data, simulated money)
2. Trade for 1-2 weeks
3. Compare: Paper P&L vs. Backtest
4. Debug parameter issues
5. Validate strategy performs as expected
6. Only then deploy to live trading

## Parameter Validation

Before deployment:
1. Backtest on past 90 days
2. Paper trade for 1 week
3. Compare metrics: frequency, win rate, avg P&L
4. Document before/after
5. Commit to DB with rationale

Example: Crypto adjustment (2026-05-18)
- Before: 48 trades/9d, $0 realized
- After: Lower frequency, better conviction
- Validation: Backtest showed improvement

## Monitoring in Production

Real-time:
- daily_summary updates (P&L tracking)
- Position monitoring (current holdings)
- Stop-loss validation (limits enforced)

Historical:
- Trade audit trail (timestamp, reason, conviction)
- Parameter evolution (documented changes)
- Conviction accumulation (30-day window)

## Risk Management Testing

Risk Limits:
- Max loss per symbol: 8% (enforced by stop-loss)
- Daily loss: -10% (circuit breaker tested)
- Cooldown: 45 min per symbol (prevent churn)
- Conviction floor: 0.3 (low-confidence filter)

Test Validation:
- Stop-loss triggers at -8% (unit test)
- Daily circuit breaker at -10% (integration test)
- Cooldown prevents re-entry (live test)
- Conviction scoring documented (parameter test)

## Conclusion

Production trading requires rigorous testing: backtesting, paper trading, parameter validation, risk limit enforcement. Error means lost money. Documentation and audit trails are critical.
