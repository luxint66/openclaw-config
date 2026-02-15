# Paper Trading Module - Documentation

## Overview

The paper trading module simulates realistic hedge trading execution with accurate cost estimation. It bridges the gap between hedge discovery and actual trading by allowing you to test strategies with realistic costs (gas, slippage, fees).

## Components

### 1. cost_calculator.py
Calculates realistic trading costs including:
- **Gas costs**: Split and sell operations on Polygon
- **Slippage**: Price impact based on order book depth
- **Trading fees**: Standard maker fees from CLOB
- **Proxy costs**: Optional proxy service costs

**Key Features:**
- Walks through order book to calculate realistic slippage
- Configurable gas price multipliers
- Supports proxy service cost estimation

### 2. position_sizer.py
Determines optimal position sizes based on:
- Bankroll and confidence level
- Kelly Criterion for advanced sizing
- Correlation adjustment for multiple positions
- Risk/reward analysis

**Key Features:**
- Three confidence levels (low: 2%, medium: 5%, high: 10%)
- Maximum position limits (15% of bankroll)
- Kelly Criterion implementation
- Position validation

### 3. paper_trades.py
Main engine for paper trading operations:
- Opens and closes paper positions
- Simulates CLOB order execution
- Tracks P&L with and without costs
- Database persistence for all trades

**Key Features:**
- Real-time P&L tracking
- Execution logging
- Portfolio snapshots
- Detailed cost breakdowns

### 4. discord_hedge_bot.py (Updated)
Discord bot commands for paper trading:
- `!paper_trade open` - Open new position
- `!paper_trade close` - Close position
- `!paper_trade portfolio` - View portfolio stats
- `!paper_trade positions` - List positions
- `!paper_help` - Show help

## Usage

### Opening a Paper Position

```
!paper_trade open <target_market_id> <target_price> <cover_market_id> <cover_price> <coverage> <tier> [confidence] [strategy]
```

**Example:**
```
!paper_trade open 0x123 0.52 0x456 0.48 0.95 1 medium to_resolution
```

**Parameters:**
- `target_market_id`: Target market ID (YES token)
- `target_price`: Entry price for target position
- `cover_market_id`: Cover market ID (NO token)
- `cover_price`: Entry price for cover position
- `coverage`: Hedge coverage (0.0 to 1.0, e.g., 0.95 for 95%)
- `tier`: Hedge tier (1, 2, or 3)
- `confidence`: Position confidence (low, medium, high) - default: medium
- `strategy`: Holding strategy (to_resolution, early_exit) - default: to_resolution

### Closing a Paper Position

```
!paper_trade close <position_id> <exit_target_price> <exit_cover_price>
```

**Example:**
```
!paper_trade close 1 0.60 0.40
```

**Parameters:**
- `position_id`: ID of position to close
- `exit_target_price`: Exit price for target position
- `exit_cover_price`: Exit price for cover position

### Viewing Portfolio

```
!paper_trade portfolio
```

Shows:
- Current bankroll
- Open positions count
- Closed trades count
- Gross and Real P&L
- Win rate
- Average ROI
- Total costs incurred

### Listing Positions

```
!paper_trade positions [open|closed]
```

Shows detailed list of positions with:
- Position ID and market details
- Coverage and size
- Entry/exit prices
- P&L and results

## Cost Estimation

### Realistic Cost Components

When opening a position, the system calculates:

1. **Gas Split Cost**: ~$0.05 per split operation
   - Required to split YES tokens into YES+NO

2. **Gas Sell Cost**: ~$0.03 per sell operation
   - Required to sell both sides of hedge

3. **Slippage**: Based on order book depth
   - Walks through order book to find realistic fill price
   - Typically 2-5% depending on liquidity

4. **Trading Fees**: 0.35% maker fee
   - Standard CLOB maker fee

5. **Proxy Cost**: ~$0.10 per operation (if using proxy)
   - Optional, for IP rotation

### Example Cost Breakdown

For a $50 position with medium confidence:
```
Gas split:     $0.05
Gas sell:      $0.06
Slippage:      $0.52 (4.00%)
Trading fees:  $0.09
Proxy cost:    $0.00
Total:         $0.72 (1.44% of position)
```

## Position Sizing

### Confidence Levels

| Confidence | Position Size | Max Loss (95% coverage) |
|------------|---------------|-------------------------|
| Low        | 2% of bankroll | 0.1% of bankroll |
| Medium     | 5% of bankroll | 0.25% of bankroll |
| High       | 10% of bankroll | 0.5% of bankroll |

### Risk Management

- **Maximum position**: 15% of bankroll (safety limit)
- **Minimum position**: $10 USD
- **Coverage impact**: Higher coverage = lower max loss
- **Kelly Criterion**: Optional advanced sizing

## Database Schema

### paper_positions
Main position records with:
- Market identifiers and tokens
- Entry/exit prices
- Cost breakdowns
- P&L calculations
- Status tracking

### execution_log
Execution history for each trade:
- Operation type (open/close)
- Order book data
- Execution prices
- Cost details

### portfolio_snapshots
Portfolio value over time:
- Bankroll tracking
- Position counts
- P&L snapshots

## Testing the System

### Test Cost Calculator
```bash
python3 cost_calculator.py
```

### Test Position Sizer
```bash
python3 position_sizer.py
```

### Test Paper Trading Engine
```bash
python3 paper_trades.py
```

### Test Discord Bot
```bash
python3 discord_hedge_bot.py
```

## Integration with Hedge Discovery

The paper trading system is designed to work with the existing hedge testing framework:

1. **Scan for hedges**: Use `!hedge_scan` or `!find_hedges`
2. **Review hedge details**: Check coverage, tier, and relationship
3. **Open paper position**: Use `!paper_trade open` with discovered hedge
4. **Monitor**: Use `!paper_trade portfolio` to track P&L
5. **Analyze results**: Close positions and review what works vs. what doesn't

## Example Workflow

```
1. !hedge_scan 20
   → Discovers hedge with 95% coverage, tier 1

2. !paper_trade open 0xabc 0.55 0xdef 0.45 0.95 1 high
   → Opens $100 paper position
   → Calculates costs: $1.45 real cost

3. !paper_trade portfolio
   → Shows open position and portfolio stats

4. [Wait for market movement]

5. !paper_trade close 1 0.65 0.35
   → Closes position
   → Shows P&L: +$8.55 gross, +$7.10 real (after costs)
   → Result: PROFITABLE

6. !paper_trade positions closed
   → Review all closed positions and patterns
```

## Benefits of Paper Trading

1. **Risk-Free Testing**: No real money at risk
2. **Realistic Costs**: Learn the true cost of trading
3. **Strategy Validation**: Prove strategies before risking capital
4. **Data Collection**: Build a track record with real execution data
5. **Cost Awareness**: Understand the impact of gas, slippage, and fees

## Key Insights to Track

When using paper trading, pay attention to:

- **Cost Impact**: How much do costs eat into profits?
- **Coverage Correlation**: Do higher coverage hedges actually perform better?
- **Tier Performance**: Which tiers are most profitable?
- **Timing**: When is the best time to enter/exit?
- **Strategy Comparison**: to_resolution vs. early_exit

## Troubleshooting

### Order Book Fetch Fails

If you see "Could not fetch order book" warnings:
- The system automatically falls back to mock order book data
- This is normal for testing without full CLOB setup
- Real trading would require proper credentials

### Database Locked

If you get database errors:
- Only one process can access the database at a time
- Stop any running bot instances before testing
- Check that no other scripts are using the database

### Import Errors

If you see import errors:
- Ensure all files are in the same directory
- Check that polyclaw skill is properly installed
- Verify Python path includes the workspace directory

## Future Enhancements

Potential improvements:
- Real-time price fetching for P&L updates
- Automated exit triggers (profit targets, stop losses)
- Portfolio diversification metrics
- Performance analytics dashboard
- Backtesting against historical data
- Integration with actual CLOB for real trading

## Support

For issues or questions:
- Check this documentation first
- Review the code comments in each module
- Test individual modules in isolation
- Use the help commands in Discord: `!paper_help`
