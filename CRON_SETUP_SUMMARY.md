# Cron Jobs Setup Summary
**Date:** 2026-02-15 06:00 UTC

---

## Cron Jobs Installed

### 1. Hedge Scanning (Every 6 Hours)
```cron
0 */6 * * * /home/luxinterior/.openclaw/workspace/cron_hedge_scan.sh
```
**Schedule:** 00:00, 06:00, 12:00, 18:00 UTC
**Parameters:**
- Limit: 50 markets
- Min coverage: 85%
- Max tier: 2
- Bankroll: $1000 USDC
- Scan type: cron_6h

**Log file:** `logs/hedge_scan_YYYYMMDD.log`
**Combined log:** `logs/cron_hedge_scan.log`

---

### 2. Hedge Monitoring (Every 30 Minutes)
```cron
*/30 * * * * /home/luxinterior/.openclaw/workspace/cron_hedge_monitor.sh
```
**Schedule:** Every 30 minutes (00, 30)
**Parameters:**
- Min profit threshold: 5.0%

**Log file:** `logs/hedge_monitor_YYYYMMDD.log`
**Combined log:** `logs/cron_hedge_monitor.log`

---

## Scripts Created

1. `/home/luxinterior/.openclaw/workspace/cron_hedge_scan.sh`
   - Automated hedge scanning script
   - Logs to dated files for easy rotation
   - Calls `hedge_test scan` with parameters

2. `/home/luxinterior/.openclaw/workspace/cron_hedge_monitor.sh`
   - Automated hedge monitoring script
   - Logs to dated files
   - Calls `hedge_test monitor` with parameters

---

## How to Check Status

### View current crontab:
```bash
crontab -l
```

### View recent scan logs:
```bash
tail -50 /home/luxinterior/.openclaw/workspace/logs/hedge_scan_$(date +%Y%m%d).log
```

### View recent monitor logs:
```bash
tail -50 /home/luxinterior/.openclaw/workspace/logs/hedge_monitor_$(date +%Y%m%d).log
```

### Check if jobs are running:
```bash
tail -f /home/luxinterior/.openclaw/workspace/logs/cron_hedge_scan.log
tail -f /home/luxinterior/.openclaw/workspace/logs/cron_hedge_monitor.log
```

---

## How to Modify or Remove

### To edit crontab:
```bash
crontab -e
```

### To remove all cron jobs:
```bash
crontab -r
```

### To temporarily disable jobs:
```bash
# Comment out lines in crontab -e by adding # at the start
```

---

## Testing

### Test scan script manually:
```bash
/home/luxinterior/.openclaw/workspace/cron_hedge_scan.sh
```

### Test monitor script manually:
```bash
/home/luxinterior/.openclaw/workspace/cron_hedge_monitor.sh
```

### Run single scan with small limit:
```bash
/home/luxinterior/.openclaw/workspace/hedge_test scan --limit 10 --min-coverage 0.90
```

---

## Log Directory Structure
```
/home/luxinterior/.openclaw/workspace/logs/
├── cron_hedge_scan.log          # All scan runs combined
├── cron_hedge_monitor.log       # All monitor runs combined
├── hedge_scan_20260215.log      # Today's scan logs
└── hedge_monitor_20260215.log   # Today's monitor logs
```
