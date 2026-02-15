# Hedge Dashboard Upgrade - Complete

**Date:** 2026-02-15
**Dashboard URL:** http://100.86.18.30:8501 or http://107.174.92.36:8501

---

## What's New

### 🔧 System Health Tab (New!)
Your single source of truth for project monitoring:

**System Status Cards:**
- ✅/❌ Database health (shows active hedge count)
- ✅/❌ Cron jobs (scan + monitor schedules)
- ✅/❌ Dashboard process status
- ✅/❌ Log files availability

**Scan Status:**
- Last scan time (with "X hours ago" format)
- Markets scanned count
- Hedges found count
- Next scheduled scan countdown (hours + minutes)
- Scan schedule displayed (every 6 hours: 00:00, 06:00, 12:00, 18:00 UTC)

**Recent Errors & Warnings:**
- **Time filtering:** Choose to see errors from last 1, 2, 6, 12, or 24 hours
- **Deduplication:** Same error merged with occurrence count (e.g., "3x" for 3 duplicates)
- **Smart parsing:** Only shows unique, meaningful errors (not every line with "error")
- **Source tracking:** Shows which log file the error came from
- **Expandable details:** Click to see full error trace
- **Monitors:** Both `hedge_monitor_YYYYMMDD.log` and `cron_hedge_monitor.log`
- **Default:** Shows last 2 hours

**Log Files:**
- Lists recent log files with timestamps and sizes
- Easy reference for debugging

---

### 🔄 Auto-Refresh
- **Enable checkbox:** "Enable auto-refresh" in sidebar
- **Adjust interval:** 30-300 seconds via slider
- **Auto-reload:** Page automatically reloads after the interval
- **Toggle on/off:** Uncheck to disable auto-refresh
- **Manual:** "Refresh Now" button always available

**Note:** Simple, reliable auto-refresh using `window.parent.location.reload()` - no countdown display needed, just set it and forget it

---

### 📊 Other Tabs (Enhanced)
All existing tabs remain:
- **Active Hedges:** View current positions
- **Closed Hedges:** Historical performance
- **Analytics:** Win rates, ROI charts, P&L over time
- **Details:** Individual hedge breakdowns

---

## Quick Commands

### Command Line Status Report
```bash
# Generate quick status report
/home/luxinterior/.openclaw/skills/polyclaw/.venv/bin/python /home/luxinterior/.openclaw/workspace/hedge_status_report.py
```

### Dashboard Restart
```bash
# Restart dashboard if needed
bash /home/luxinterior/.openclaw/workspace/restart_dashboard
```

### Cron Jobs (Already Configured)
```bash
# View current cron jobs
crontab -l

# Output:
# Hedge scanning every 6 hours
0 */6 * * * /home/luxinterior/.openclaw/workspace/cron_hedge_scan.sh
# Hedge monitoring every 30 minutes
*/30 * * * * /home/luxinterior/.openclaw/workspace/cron_hedge_monitor.sh
```

---

## File Locations

**Dashboard:** `/home/luxinterior/.openclaw/skills/polyclaw/testing/dashboard.py`
**Logs:** `/home/luxinterior/.openclaw/workspace/logs/`
**Database:** `/home/luxinterior/.openclaw/skills/polyclaw/db/hedge_testing.db`
**Status Script:** `/home/luxinterior/.openclaw/workspace/hedge_status_report.py`

---

## System Status (As of 2026-02-15 09:24 UTC)

- ✅ Database: Working
- ✅ Cron Jobs: Active (scan every 6h, monitor every 30m)
- ✅ Dashboard: Running
- ✅ Logs: Available
- ✅ Last Scan: 0.3 hours ago (5 markets, 0 hedges)

---

## How to Use

1. **Open Dashboard:** Go to http://100.86.18.30:8501
2. **Check System Health:** First tab shows everything at a glance
3. **Review Errors:** Expand error items in "Recent Errors & Warnings"
4. **View Hedges:** Switch to Active/Closed tabs
5. **Auto-refresh:** Check "Enable auto-refresh" in sidebar and set interval (page auto-reloads)
6. **Manual refresh:** Click "🔄 Refresh Now" button in sidebar anytime
7. **See last update time:** Displayed at top of page and in sidebar

**No Discord commands needed** — dashboard is your single source of truth!

---

## Troubleshooting

### Too many old errors showing?
Use the time filter in the "Recent Errors & Warnings" section:
- Default: Last 2 hours
- Options: 1, 2, 6, 12, or 24 hours
- This filters out historical errors from before your fixes

To completely clear old errors:
```bash
# Backup current logs (optional)
cp /home/luxinterior/.openclaw/workspace/logs/*.log ~/logs_backup/

# Clear monitor log
> /home/luxinterior/.openclaw/workspace/logs/hedge_monitor_$(date +%Y%m%d).log

# Clear cron log
> /home/luxinterior/.openclaw/workspace/logs/cron_hedge_monitor.log
```

### Dashboard not loading
```bash
# Check if process is running
ps aux | grep streamlit

# Restart dashboard
bash /home/luxinterior/.openclaw/workspace/restart_dashboard
```

### Cron jobs not running
```bash
# Check cron configuration
crontab -l

# Check logs
tail -f /home/luxinterior/.openclaw/workspace/logs/cron_hedge_monitor.log
```

### Database errors
```bash
# Check database status
/home/luxinterior/.openclaw/skills/polyclaw/.venv/bin/python /home/luxinterior/.openclaw/workspace/hedge_status_report.py
```

---

## Summary

Your dashboard is now a **complete command center** for the hedge bot project:
- See system health at a glance
- Monitor scans automatically
- Catch errors early
- No Discord commands required
- **Auto-refresh** (simple, reliable page reload - set interval and forget)
- Manual refresh (click "Refresh Now" in sidebar)

Open the dashboard and you'll know instantly if everything's healthy. No need to dig through logs or remember commands!
