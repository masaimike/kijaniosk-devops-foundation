# KijaniKiosk API Server - Triage Report
**Date:** [28/09]
**Investigated by:** [MIchael Masai]
**Server:**[michael-HP-Envy-x360-2-in-1-Laptop-14-es0xxx]
**Incident start (approximate):** [04:07 on 2024-01-15]

## Summary[2-3 sentences: what you found and whether you identified a likely root cause]
THe database. Memory cpu and disk are fine.
## Process and Resource State[What you found. Be specific: process names, PIDs, memory/CPU figures]
pid 8054  3.4% RAM  05 CPU is the main consumer of memory but is idle
## Filesystem and Disk[Disk usage findings. Any large or unexpected files]
/var/log/kijanikiosk is the largest item with 271MB
## Log Analysis[Key log entries. When errors started. Error frequency. Any patterns]
6 errors . errors startes at 03:45 ,also at 04;07 and  06:22
## Network and Service State[Port binding status. HTTP response time. TCP connection state]
TCP states: 7 LISTEN, 4 CLOSE-WAIT, 2 ESTAB, 1 TIME-WAIT. nginx listening on port80
## Assessment
[the databse pool may have filled up]

## Recommended Next Steps[What should be done next. Maximum 3 actions, most important first]
