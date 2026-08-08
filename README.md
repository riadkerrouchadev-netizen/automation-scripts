# Automation Scripts ⚙️

Collection of real-world Python automation scripts for common business tasks: file processing, API synchronization, monitoring, and report generation.

## Scripts

### 1. File Processor (`/file_processor`)
Batch process files: rename, convert, organize, and transform data files automatically.

```python
# Example: Watch a folder for new CSVs → clean → merge → export
python file_processor/watch.py --input ./incoming --output ./processed --format xlsx
```

- Watches directories for new files (watchdog)
- Handles CSV, JSON, Excel, PDF text extraction
- Data cleaning: dedup, normalize, validate
- Scheduled or event-driven execution

### 2. API Sync (`/api_sync`)
Keep two systems in sync by polling APIs and pushing changes.

```python
# Example: Sync CRM contacts to email platform every 30 min
python api_sync/sync.py --source crm --target mailchimp --interval 30
```

- Generic source/target adapter pattern
- Incremental sync (only changed records)
- Conflict resolution strategies
- Detailed sync logs with error tracking

### 3. Health Monitor (`/health_monitor`)
Monitor websites and APIs — get alerted when something goes down.

```python
# Example: Check 10 endpoints every 5 minutes
python health_monitor/monitor.py --config endpoints.yaml
```

- HTTP health checks with configurable thresholds
- Response time tracking and anomaly detection
- Multi-channel alerts (email, webhook, Slack)
- Dashboard with uptime history

### 4. Report Generator (`/report_generator`)
Pull data from multiple sources, transform, and generate formatted reports.

```python
# Example: Weekly sales report from database + API
python report_generator/generate.py --template weekly_sales --output pdf
```

- Jinja2 templates for HTML/PDF reports
- Multiple data sources (SQL, API, CSV)
- Scheduled generation via cron
- Email delivery with attachments

## Project Structure

```
automation-scripts/
├── file_processor/
│   ├── watch.py            # Directory watcher
│   ├── processors.py       # File type handlers
│   ├── cleaners.py         # Data cleaning functions
│   └── config.yaml
├── api_sync/
│   ├── sync.py             # Main sync orchestrator
│   ├── adapters/           # Source/target adapters
│   ├── resolver.py         # Conflict resolution
│   └── config.yaml
├── health_monitor/
│   ├── monitor.py          # Health check runner
│   ├── alerter.py          # Notification system
│   ├── dashboard.py        # FastAPI status dashboard
│   └── endpoints.yaml
├── report_generator/
│   ├── generate.py         # Report orchestrator
│   ├── templates/          # Jinja2 report templates
│   ├── datasources.py      # Data fetching layer
│   └── delivery.py         # Email/export
├── common/
│   ├── logger.py           # Structured logging
│   ├── scheduler.py        # Cron/interval wrapper
│   └── config_loader.py    # YAML config management
├── requirements.txt
└── README.md
```

## Design Principles

**Every script follows the same pattern:**

1. **Config-driven** — No hardcoded values. Everything in YAML.
2. **Idempotent** — Safe to re-run. Won't duplicate data or actions.
3. **Observable** — Structured logging, clear error messages, exit codes.
4. **Testable** — Core logic separated from I/O. Easy to unit test.

### Example: Config-Driven Design
```python
from dataclasses import dataclass
from pathlib import Path
import yaml

@dataclass
class MonitorConfig:
    endpoints: list[dict]
    interval_seconds: int = 300
    timeout_seconds: int = 10
    alert_channels: list[str] = None

    @classmethod
    def from_yaml(cls, path: Path) -> "MonitorConfig":
        with open(path) as f:
            data = yaml.safe_load(f)
        return cls(**data)
```

### Example: Structured Logging
```python
import logging
import json
from datetime import datetime

class JSONFormatter(logging.Formatter):
    def format(self, record):
        return json.dumps({
            "timestamp": datetime.utcnow().isoformat(),
            "level": record.levelname,
            "message": record.getMessage(),
            "module": record.module,
            **getattr(record, "extra", {}),
        })

# Usage
logger.info("Sync completed", extra={"extra": {"records": 42, "duration_ms": 1230}})
```

## Tech Stack

- **Python 3.11+**
- **FastAPI** — Dashboard and webhook endpoints
- **Schedule / APScheduler** — Task scheduling
- **Watchdog** — File system monitoring
- **Jinja2** — Report templates
- **PyYAML** — Configuration
- **requests / httpx** — API clients

## Installation

```bash
git clone https://github.com/riadkerrouchadev-netizen/automation-scripts.git
cd automation-scripts
pip install -r requirements.txt
```

## License

MIT
