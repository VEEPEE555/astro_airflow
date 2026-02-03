# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an Apache Airflow project built with Astronomer (managed Airflow platform). It uses Astro Runtime 3.1-11 as the base Docker image, which includes pre-installed Airflow providers.

## Common Commands

### Local Development
```bash
astro dev start    # Start local Airflow (5 containers: Postgres, Scheduler, DAG Processor, API Server, Triggerer)
astro dev stop     # Stop local environment
astro dev restart  # Restart after code changes
astro dev run <cmd>  # Run command in Airflow container
```

### Testing
```bash
astro dev pytest tests/  # Run all tests
astro dev pytest tests/dags/test_dag_example.py  # Run specific test file
astro dev pytest tests/dags/test_dag_example.py::test_dag_tags  # Run single test
```

### DAG Validation
```bash
astro dev parse  # Validate DAG syntax (uses .astro/test_dag_integrity_default.py)
```

### Access Points
- Airflow UI: http://localhost:8080/
- Postgres: localhost:5432/postgres (user: postgres, password: postgres)

## Architecture

### Directory Structure
- `dags/` - DAG definitions (Python files)
- `include/` - Additional files to include in the project
- `plugins/` - Custom Airflow plugins
- `tests/dags/` - DAG tests using pytest
- `.astro/` - Astronomer CLI managed files (don't edit manually)

### DAG Standards (enforced by tests)
- All DAGs must have tags
- All DAGs must have `retries >= 2` in default_args
- DAGs must import without errors

### DAG Pattern
DAGs use the TaskFlow API with decorators:
```python
from airflow.sdk import Asset, dag, task
from pendulum import datetime

@dag(
    start_date=datetime(2025, 1, 1),
    schedule="@daily",
    default_args={"owner": "Astro", "retries": 3},
    tags=["example"],
)
def my_dag():
    @task
    def my_task():
        pass

    my_task()

my_dag()
```

### Configuration
- `requirements.txt` - Python dependencies
- `packages.txt` - OS-level packages
- `airflow_settings.yaml` - Local connections, variables, pools (not deployed)
- `.env` - Environment variables
