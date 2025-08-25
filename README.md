# Case Study: Data Engineering (Process Mining)

---

**⚠️ PLEASE DO NOT FORK THIS REPO AS OTHERS MAY SEE YOUR CODE. INSTEAD YOU CAN USE THE
[USE THIS TEMPLATE](https://github.com/new?template_name=case-study-data-engineering-process&template_owner=MDPI-AG)
BUTTON TO CREATE YOUR OWN REPOSITORY.**

---

## Targeted Workflow

Stored raw data (parquet) → Preprocess → Store processed data → Load to PostgreSQL → Transform with dbt → Analyze

## Getting Started

### Python environment

The environment can be initialized either using pip or uv.

#### 1. Using pip

A `requirements.txt` file is provided in order to install the required packages using pip. It is recommended to use a virtual environment
to avoid conflicts with other projects.

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

#### 2. Using uv

The case study also provides a `pyproject.toml` and `uv.lock` file to initialize the environment with uv.

```bash
uv sync
source .venv/bin/activate
```

### Environment variables

A template that contains all the required environment variables is provided in `.env_template`, which you can copy to a `.env` file and source it with the following commands:

```bash
set -a
source .env
set +a
```

## Data

> Exported data ressembles our production system 
data (it has been scrambled and any ressemblance to actual MDPI data is purely
coincidental).

The raw dataset include 2 files:

- `log_export.parquet` contains an extract of event logs.
- `db_export.parquet` contains an extract of a database table.

### 1. Event logs

Each process instance is identified by a `hash_key` that is unique
for each manuscript. The event logs looks like the following:

```json
{
  "hash_key": "00318206b58d24b8b53361ed3fa120a3",
  "event_type": "submit_manuscript",
  "timestamp": 1728894511,
}
```

The data contains only traces of process instances that have been completed (i.e., from
the start event `submit_manuscript` to the end event `publish_manuscript`). However, these
events may not be unique in the data for a given manuscript_hash (there could be several
instances of `submit_manuscript` or `publish_manuscript`). You may want to deduplicate
the data before loading it into the database (using the earliest event for `submit_manuscript`
and the latest event for `publish_manuscript`).

### 2. Database export

The raw dataset also contains an export of the metadata linked to event logs. Data from both files can be joined using the `hash_key` column.

## dbt

The third part of the assessments includes the development of dbt models to provide analytical insights.
The case study already provides the skeleton of a default dbt project.

If you are not familiar with dbt, you can check their [sandox project](https://github.com/dbt-labs/jaffle-shop/)
on GitHub to get started. You can also check the [dbt documentation](https://docs.getdbt.com/docs/introduction)
for more information.
