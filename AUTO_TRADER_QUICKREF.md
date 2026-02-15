# Auto-Trader Quick Reference

## Discord Commands (All start with `!`)

### Strategy Management
```
!auto_strategy list                              # List all strategies
!auto_strategy load <name>                       # Set active strategy
!auto_strategy show <name>                       # Show strategy config
!auto_strategy delete <name>                     # Delete strategy
!auto_strategy template <name>                   # Load template
!auto_strategy performance                       # Compare all strategies
!auto_strategy stats <name>                     # Strategy details
!auto_strategy history                           # Trade history
!auto_strategy reasons                           # Exit reason stats
```

### Trading
```
!auto_trade open <tid> <tp> <cid> <cp> <cov> <tier> [scenario]
!auto_trade status                               # Show active positions
!auto_trade check                                # Check exit conditions
!auto_trade close <pid> <exit_tp> <exit_cp>       # Manual close
```

### Help
```
!auto_help                                       # Show help
```

## Strategy Templates

| Command | Description | Profit | Stop Loss | Position |
|---------|-------------|--------|-----------|----------|
| `!auto_strategy template conservative` | Low risk | +5% | -10% | 5% |
| `!auto_strategy template aggressive` | High risk | +25% | Trail 10% | 15% |
| `!auto_strategy template balanced` | Medium | +15% OR 48h | -15% | 10% |
| `!auto_strategy template scalper` | Quick | +3% OR 6h | None | 8% |
| `!auto_strategy template swing` | Medium-term | +20% AND 72h | None | 12% |

## Quick Start

```bash
# 1. Initialize (first time only)
python3 auto_trader.py

# 2. In Discord, load a strategy
!auto_strategy template balanced

# 3. Open a position
!auto_trade open 0x123 0.52 0x456 0.48 0.95 1 test_scenario

# 4. Monitor
!auto_trade status
!auto_trade check

# 5. Compare strategies
!auto_strategy performance
```

## Files

- `auto_trader.py` - Main module
- `discord_hedge_bot.py` - Discord commands (updated)
- `AUTO_TRADER_README.md` - Full documentation
- `test_auto_trader.py` - Test script
- `db/auto_trader.db` - Auto-trader database

## Testing

```bash
# Run tests
python3 test_auto_trader.py
```

## Exit Conditions

| Type | Description |
|------|-------------|
| `hold_to_resolution` | Wait for market resolution |
| `target_profit_pct` | Exit at X% profit |
| `target_roi_threshold` | Exit when ROI > X% |
| `time_based_exit` | Exit after X hours |
| `stop_loss` | Exit if loss > X% |
| `trailing_stop` | Trail X% below peak |
| `combined_and` | ALL conditions met |
| `combined_or` | ANY condition met |
