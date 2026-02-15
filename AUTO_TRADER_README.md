# Auto-Trader Module

Automated trading strategy system for the Discord hedge bot with configurable exit conditions, strategy management, and comprehensive performance analytics.

## Features

### 1. Configurable Trading Strategies
- **Flexible Exit Conditions**: Define when to exit positions using various rules
- **Strategy Templates**: Pre-built strategies for different trading styles
- **Custom Strategies**: Create and save your own trading rules
- **Position Sizing**: Automatic position sizing based on confidence levels

### 2. Exit Condition Types

| Condition Type | Description | Parameters |
|----------------|-------------|------------|
| `hold_to_resolution` | Hold until market resolves | none |
| `target_profit_pct` | Exit when profit reaches X% | `profit_pct` |
| `target_roi_threshold` | Exit when ROI exceeds X% | `roi_threshold` |
| `time_based_exit` | Exit after X hours | `holding_hours` |
| `stop_loss` | Exit if loss exceeds X% | `loss_threshold` |
| `trailing_stop` | Trailing stop X% below peak | `trail_pct` |
| `combined_and` | Exit when ALL conditions met | `conditions[]` |
| `combined_or` | Exit when ANY condition met | `conditions[]` |

### 3. Strategy Templates

Five pre-configured strategies are included:

#### 🛡️ Conservative
- **Risk Profile**: Low
- **Exit Rules**: +5% profit target, -10% stop loss
- **Position Size**: 5% of bankroll
- **Filters**: 98%+ coverage, Tier 1 only

#### 🚀 Aggressive
- **Risk Profile**: High
- **Exit Rules**: +25% profit target, 10% trailing stop
- **Position Size**: 15% of bankroll
- **Filters**: 90%+ coverage, up to Tier 3

#### ⚖️ Balanced
- **Risk Profile**: Medium
- **Exit Rules**: +15% profit OR 48 hours, -15% stop loss
- **Position Size**: 10% of bankroll
- **Filters**: 95%+ coverage, up to Tier 2

#### ⚡ Scalper
- **Risk Profile**: Quick trades
- **Exit Rules**: +3% profit OR 6 hours max
- **Position Size**: 8% of bankroll
- **Filters**: 97%+ coverage, Tier 1 only

#### 🌊 Swing
- **Risk Profile**: Medium-term holds
- **Exit Rules**: +20% profit AND 72 hours
- **Position Size**: 12% of bankroll
- **Filters**: 93%+ coverage, up to Tier 2

## Database Schema

### Tables

#### `strategies`
Stores strategy configurations and performance data.

```
- id: Primary key
- name: Unique strategy name
- description: Strategy description
- author: Who created the strategy
- config: JSON strategy configuration
- exit_conditions: JSON array of exit conditions
- total_trades: Total trades executed
- profitable_trades: Count of profitable trades
- total_roi: Cumulative ROI across all trades
- avg_holding_time_hours: Average holding time
- created_at: Creation timestamp
- last_used_at: Last used timestamp
- updated_at: Last update timestamp
```

#### `auto_trades`
Links paper positions to strategies and records execution data.

```
- id: Primary key
- strategy_id: Foreign key to strategies
- paper_position_id: Foreign key to paper_positions
- strategy_config: Strategy config snapshot at open time
- exit_conditions: Exit conditions snapshot
- scenario_name: Optional scenario label
- exit_reason: Why the position was closed
- holding_time_hours: How long position was held
- opened_at: Opening timestamp
- closed_at: Closing timestamp
```

#### `exit_condition_types`
Reference table for exit condition types.

#### `performance_snapshots`
Time-series data for tracking strategy performance over time.

## Discord Commands

### Strategy Management

```
!auto_strategy list
```
List all available strategies with performance summary.

```
!auto_strategy load <name>
```
Set a strategy as active for auto-trading.

```
!auto_strategy show <name>
```
Display detailed strategy configuration.

```
!auto_strategy delete <name>
```
Delete a strategy (cannot delete active strategy).

```
!auto_strategy template <name>
```
Load a pre-built template (conservative, aggressive, balanced, scalper, swing).

### Trading Operations

```
!auto_trade open <target_id> <target_price> <cover_id> <cover_price> <coverage> <tier> [scenario]
```
Open a position using the active strategy.

Example:
```
!auto_trade open 0x123 0.52 0x456 0.48 0.95 1 test_scenario
```

```
!auto_trade status
```
Show all active auto-trades.

```
!auto_trade check
```
Check exit conditions for all active positions.

```
!auto_trade close <position_id> <exit_target_price> <exit_cover_price>
```
Manually close a position.

### Performance Analysis

```
!auto_strategy performance
```
Compare all strategies side-by-side.

```
!auto_strategy stats <name>
```
Get detailed performance statistics for a specific strategy.

```
!auto_strategy history [strategy_name] [limit]
```
View trade history.

```
!auto_strategy reasons
```
View statistics grouped by exit reason.

## Usage Workflow

### Basic Workflow

1. **Initialize the database** (first time only):
   ```python
   from auto_trader import init_db, initialize_predefined_strategies
   init_db()
   initialize_predefined_strategies()
   ```

2. **Load a strategy**:
   ```
   !auto_strategy template balanced
   ```
   or
   ```
   !auto_strategy load conservative
   ```

3. **Open positions**:
   ```
   !auto_trade open 0x123 0.52 0x456 0.48 0.95 1 scenario_A
   ```

4. **Monitor positions**:
   ```
   !auto_trade status
   !auto_trade check
   ```

5. **Review performance**:
   ```
   !auto_strategy performance
   ```

### Testing Multiple Strategies

1. Load a strategy and make trades with a scenario label:
   ```
   !auto_strategy load conservative
   !auto_trade open 0x123 0.52 0x456 0.48 0.95 1 test_conservative
   ```

2. Switch to a different strategy:
   ```
   !auto_strategy load aggressive
   !auto_trade open 0x123 0.52 0x456 0.48 0.95 1 test_aggressive
   ```

3. Compare performance:
   ```
   !auto_strategy performance
   ```

## Creating Custom Strategies

### Python API

```python
from auto_trader import AutoTrader, Strategy, ExitCondition

# Create custom strategy
strategy = Strategy(
    name='my_custom',
    description='My custom trading approach',
    author='me',
    exit_conditions=[
        ExitCondition(
            condition_type='target_profit_pct',
            params={'profit_pct': 12.5},
            enabled=True
        ),
        ExitCondition(
            condition_type='stop_loss',
            params={'loss_threshold': -8.0},
            enabled=True
        )
    ],
    position_sizing={
        'confidence': 'medium',
        'max_position_pct': 0.10
    },
    filters={
        'min_coverage': 0.96,
        'max_tier': 2
    }
)

# Save strategy
with AutoTrader() as trader:
    trader.save_strategy(strategy)
```

### Combined Conditions

```python
# Exit when profit reaches 20% OR after 72 hours
strategy = Strategy(
    name='custom_or',
    description='Exit on profit OR time',
    author='me',
    exit_conditions=[
        ExitCondition(
            condition_type='combined_or',
            params={
                'conditions': [
                    {
                        'condition_type': 'target_profit_pct',
                        'params': {'profit_pct': 20.0},
                        'enabled': True
                    },
                    {
                        'condition_type': 'time_based_exit',
                        'params': {'holding_hours': 72.0},
                        'enabled': True
                    }
                ]
            },
            enabled=True
        )
    ],
    position_sizing={'confidence': 'medium', 'max_position_pct': 0.10},
    filters={'min_coverage': 0.95, 'max_tier': 2}
)
```

```python
# Exit when profit reaches 15% AND has been held 24 hours
strategy = Strategy(
    name='custom_and',
    description='Exit on profit AND time',
    author='me',
    exit_conditions=[
        ExitCondition(
            condition_type='combined_and',
            params={
                'conditions': [
                    {
                        'condition_type': 'target_profit_pct',
                        'params': {'profit_pct': 15.0},
                        'enabled': True
                    },
                    {
                        'condition_type': 'time_based_exit',
                        'params': {'holding_hours': 24.0},
                        'enabled': True
                    }
                ]
            },
            enabled=True
        )
    ],
    position_sizing={'confidence': 'medium', 'max_position_pct': 0.10},
    filters={'min_coverage': 0.95, 'max_tier': 2}
)
```

## Integration with Paper Trading

The auto-trader integrates seamlessly with the paper trading module:

1. **Position Opening**: Uses `PaperTradingEngine.open_position()` with strategy-based sizing
2. **Cost Estimation**: Leverages `CostCalculator` for realistic trading costs
3. **P&L Tracking**: Maintains real-time P&L with cost accounting
4. **Exit Execution**: Uses `PaperTradingEngine.close_position()` when conditions are met

## Performance Metrics Tracked

### Per Strategy
- **Total Trades**: Number of trades executed
- **Profitable Trades**: Count of winning trades
- **Total ROI**: Cumulative return across all trades
- **Average ROI**: Mean return per trade
- **Win Rate**: Percentage of profitable trades
- **Average Holding Time**: Mean hours per position

### Per Trade
- **Entry/Exit Prices**: Target and cover prices
- **Holding Time**: Hours position was open
- **Exit Reason**: Which condition triggered the exit
- **Gross P&L**: Profit/loss before costs
- **Real P&L**: Profit/loss after costs
- **ROI**: Return on investment percentage

### Aggregated Analysis
- **Exit Reason Stats**: Performance by exit condition type
- **Scenario Performance**: Track different test scenarios
- **Strategy Comparison**: Side-by-side strategy analysis

## Example Output

```
📊 **Strategy Performance: balanced**

**Total Trades:** 15
**Profitable Trades:** 9
**Win Rate:** 60.0%
**Total ROI:** +42.5%
**Average ROI:** +2.83%
**Average Holding Time:** 18.5 hours
```

```
📊 **Strategy Comparison**

| Strategy  | Trades | Win Rate | Avg ROI | Avg Holding |
|-----------|--------|----------|---------|-------------|
| conservative | 20    | 75.0%    | +1.25%  | 8.2h        |
| balanced   | 15     | 60.0%    | +2.83%  | 18.5h       |
| aggressive | 10     | 40.0%    | +5.20%  | 32.1h       |

🏆 Best ROI: aggressive (+5.20%)
🎯 Best Win Rate: conservative (75.0%)
```

## File Structure

```
/home/luxinterior/.openclaw/workspace/
├── auto_trader.py           # Main auto-trader module
├── discord_hedge_bot.py      # Discord bot with auto-trader commands
├── paper_trades.py           # Paper trading engine (integrated)
├── AUTO_TRADER_README.md     # This file
└── db/
    ├── auto_trader.db        # Auto-trader database
    └── paper_trading.db      # Paper trading database
```

## Advanced Features

### Scenario Testing
Label trades with scenario names to compare different approaches:
```
!auto_trade open 0x123 0.52 0x456 0.48 0.95 1 high_coverage_test
!auto_trade open 0x123 0.52 0x456 0.48 0.95 1 low_coverage_test
```

### Exit Reason Analysis
Understand why trades are closing:
```
!auto_strategy reasons
```

Output:
```
📊 **Exit Reason Statistics**

**profit_target_met: +10.0%**
  Count: 8
  Avg ROI: +10.5%
  Avg Holding: 6.2h

**time_limit_reached: 48.0h**
  Count: 4
  Avg ROI: +2.1%
  Avg Holding: 48.0h
```

## Troubleshooting

### "No active strategy set"
Use `!auto_strategy load <name>` to set an active strategy before opening positions.

### "Coverage below strategy minimum"
The position doesn't meet the strategy's filter criteria. Adjust strategy filters or find higher-coverage hedges.

### Database locked
Make sure you're not running multiple instances accessing the database simultaneously.

### Position not closing on conditions
Run `!auto_trade check` to verify conditions are being evaluated. Check that:
- Active strategy is loaded
- Exit conditions are properly configured
- Current prices are being fetched correctly

## Future Enhancements

Potential improvements:
- [ ] Automated background monitoring
- [ ] Real-time price fetching from CLOB
- [ ] Webhook notifications on exit triggers
- [ ] Strategy backtesting on historical data
- [ ] Machine learning optimization of parameters
- [ ] Multi-condition strategy builder UI
- [ ] Performance export to CSV/JSON
- [ ] Strategy versioning and rollback

## License

Part of the OpenClaw workspace hedge trading system.
