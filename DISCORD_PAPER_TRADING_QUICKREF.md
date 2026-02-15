# Discord Paper Trading - Quick Reference

## Quick Start

```
!paper_help                          # Show all paper trading commands
!paper_trade portfolio              # View your portfolio & P&L
!paper_trade positions open         # List open positions
```

## Commands

### Open a Paper Position

```
!paper_trade open <target_id> <target_price> <cover_id> <cover_price> <coverage> <tier> [confidence] [strategy]
```

**Example:**
```
!paper_trade open 0xabc 0.52 0xdef 0.48 0.95 1 medium to_resolution
```

**Parameters:**
| Parameter | Description | Example |
|-----------|-------------|---------|
| target_id | Target market ID | 0xabc |
| target_price | Entry price for target | 0.52 |
| cover_id | Cover market ID | 0xdef |
| cover_price | Entry price for cover | 0.48 |
| coverage | Hedge coverage (0.0-1.0) | 0.95 = 95% |
| tier | Hedge tier (1-3) | 1 |
| confidence | Position confidence | low/medium/high (default: medium) |
| strategy | Holding strategy | to_resolution/early_exit (default: to_resolution) |

### Close a Paper Position

```
!paper_trade close <position_id> <exit_target_price> <exit_cover_price>
```

**Example:**
```
!paper_trade close 1 0.60 0.40
```

**Parameters:**
| Parameter | Description |
|-----------|-------------|
| position_id | ID of position to close (check with `!paper_trade positions`) |
| exit_target_price | Current/exit price for target position |
| exit_cover_price | Current/exit price for cover position |

### View Portfolio

```
!paper_trade portfolio
```

**Shows:**
- Current bankroll
- Number of open/closed positions
- Gross P&L (before costs)
- Real P&L (after costs)
- Win rate
- Average ROI
- Total trading costs incurred

### List Positions

```
!paper_trade positions [open|closed]
```

**Examples:**
```
!paper_trade positions open    # Show open positions
!paper_trade positions closed  # Show closed positions
!paper_trade positions         # Default: show open
```

**Shows:**
- Position ID
- Market question
- Coverage and size
- Entry/exit prices
- P&L (for closed positions)
- Result (profitable/loss/breakeven)

## Confidence Levels

| Confidence | Position Size | Use Case |
|------------|---------------|----------|
| **low** | 2% of bankroll | Testing new strategies, uncertain markets |
| **medium** | 5% of bankroll | Normal trading, moderate confidence |
| **high** | 10% of bankroll | High confidence, proven strategies |

## Common Workflows

### Workflow 1: Test a Discovered Hedge

```
1. !hedge_scan 20                          # Find hedge opportunities
2. !paper_trade open 0x123 0.52 0x456 0.48 0.95 1 medium  # Open paper position
3. !paper_trade portfolio                  # Track P&L
4. !paper_trade close 1 0.60 0.40          # Close when ready
5. !paper_trade positions closed           # Review results
```

### Workflow 2: Compare Confidence Levels

```
# Open same hedge with different confidences
!paper_trade open 0x123 0.52 0x456 0.48 0.95 1 low
!paper_trade open 0x123 0.52 0x456 0.48 0.95 1 medium
!paper_trade open 0x123 0.52 0x456 0.48 0.95 1 high

# Compare P&L after closing all
!paper_trade close 1 <prices>
!paper_trade close 2 <prices>
!paper_trade close 3 <prices>
!paper_trade portfolio
```

### Workflow 3: Monitor Portfolio

```
!paper_trade portfolio                    # Check overall stats
!paper_trade positions open              # See what's open
!paper_trade positions closed            # Review past trades
```

## Understanding the Output

### Position Opening

```
📊 Opening paper position...
  Target: Will Bitcoin reach $100k in 2025?...
  Cover: Bitcoin will not reach $100k in 2025?...
  Coverage: 95.0%, Tier: 1
  💰 Calculating position size (confidence: medium)...
     Position: $50.00 (5.0% of bankroll)
  💸 Estimating trading costs...
     Gas split: $0.0500
     Gas sell: $0.0600
     Slippage: $0.5200 (4.00%)
     Trading fees: $0.0875
     Total real cost: $0.7175
  🔨 Simulating order execution...
     Mid price: 0.5000, Spread: 0.0400
     Execution price: 0.5200
  ✅ Position #1 opened!
```

**Key Metrics:**
- **Position size**: Amount allocated to this trade
- **Total real cost**: All costs (gas + slippage + fees)
- **Execution price**: Realistic fill price from order book

### Position Closing

```
📊 Closing paper position #1...
  Target entry: 0.5200, exit: 0.6000
  Cover entry: 0.4800, exit: 0.4000
  💰 Gross P&L: $0.00 (+0.00%)
  💸 Real P&L: $-0.72 (-1.44%) (after costs)
  📊 Result: LOSS
  ✅ Position #1 closed!
```

**Key Metrics:**
- **Gross P&L**: Profit/loss before costs (usually ~0 for hedges)
- **Real P&L**: Profit/loss after subtracting all trading costs
- **Result**: PROFITABLE, LOSS, or BREAKEVEN

### Portfolio Summary

```
📊 **Paper Trading Portfolio**

**Bankroll:** $1000.00
**Open Positions:** 2
**Closed Trades:** 5

**Performance:**
  Gross P&L: $0.00
  Real P&L: $-3.50
  Win Rate: 40.0%
  Avg ROI: -0.70%
  Total Costs: $3.50
  Costs vs P&L: 0.0%
```

**Key Metrics:**
- **Gross P&L**: Total profit/loss before costs
- **Real P&L**: Total profit/loss after costs (this is what matters!)
- **Win Rate**: Percentage of profitable trades
- **Total Costs**: All trading costs incurred

## Tips

1. **Start Small**: Use `low` confidence when testing new strategies
2. **Track Costs**: Pay attention to "Total Costs" - this is real money you'd lose in live trading
3. **Compare Strategies**: Open same hedge with different confidence levels to see impact
4. **Review History**: Regularly check `!paper_trade positions closed` to find patterns
5. **Understand P&L**: For hedges, gross P&L is often ~0 (market-neutral), real P&L shows the true cost

## Common Questions

**Q: Why is gross P&L often $0.00?**
A: Hedges are designed to be market-neutral. If one side goes up, the other goes down by the same amount. Real P&L (after costs) shows the true profitability.

**Q: What does coverage 0.95 mean?**
A: 95% coverage means 95% of your position is hedged. If the market moves against you, you only lose 5% of the position value (plus costs).

**Q: How do I know which position ID to close?**
A: Use `!paper_trade positions open` to see all open positions with their IDs.

**Q: What if I don't have the current prices?**
A: You can estimate exit prices based on current market conditions, or check Polymarket directly.

**Q: Why are costs so high?**
A: Real trading costs include gas (~$0.08 per trade), slippage (2-5% depending on liquidity), and fees (0.35%). These are real costs you'd pay in live trading!

## Getting Help

```
!paper_help          # Show paper trading help
!hedge_help          # Show all hedge bot commands
```

For detailed documentation, see: PAPER_TRADING_README.md
