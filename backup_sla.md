# Backup SLA

## Coverage: 
- MySQL databases
- Influx databases
## RPO:
- full backup weekly on saturday at 11p.m. UTC
- incremental backup sunday-friday at 11p.m. UTC
## Retention:
- 56 versions
- 8 weeks retention
## Usability checks:
- once every 4 weeks
- team activity
## Restoration criteria:
- when all other means of restoration fail
## RTO:
- 6 hours