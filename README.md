# Case Study: Data Engineering (Process Mining)

---

**⚠️ PLEASE DO NOT FORK THIS REPO AS OTHERS MAY SEE YOUR CODE. INSTEAD YOU CAN USE THE
[USE THIS TEMPLATE](https://github.com/new?template_name=case-study-data-engineering-process&template_owner=MDPI-AG)
BUTTON TO CREATE YOUR OWN REPOSITORY.**

---

## Targeted Workflow

Stored raw data (parquet) → Extract → Preprocess → Store processed data → Load to PostgreSQL → Transform with dbt → Analyze

## Getting Started

Install the required packages using pip. It is recommended to use a virtual environment
to avoid conflicts with other projects.

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Start the Postgres database using Docker:

```bash
docker-compose up -d
```

This will start a Postgres database on port 5432. You can access the database using
any Postgres client.

## Data

The raw data contains an extract of event logs that ressembles our production system
data (the data has been scrambled and any ressemblance to actual MDPI data is purely
coincidental). Each process instance is identified by a `hash_key` that is unique
for each manuscript. The event logs looks like the following:

```json
{
  "hash_key": "00318206b58d24b8b53361ed3fa120a3",
  "journal_name": "Applied Sciences",
  "st": 1,
  "et": "submit_manuscript",
  "tm": 1728894511,
  "timestamp": "2024-10-14T10:28:31"
}
```

The data contains only traces of process instances that have been completed (i.e., from
the start event `submit_manuscript` to the end event `publish_manuscript`). However, these
events may not be unique in the data for a given manuscript_hash (there could be several
instances of `submit_manuscript` or `publish_manuscript`). You may want to deduplicate
the data before loading it into the database (using the earliest event for `submit_manuscript`
and the latest event for `publish_manuscript`).

## Suggested Project Structure

```plaintext
.
├── data                        # Local storage for ingested data
│   ├── raw                     # Raw event logs as a Parquet file for ingestion
│   │   └── data.parquet
│   └── processed               # Cleaned/preprocessed files (if needed)
│
├── src                         # All Python source code
│   ├── extract                 # Code to call APIs and fetch raw data
│   │   ├── __init__.py
│   │   └── extractor.py
│   │
│   ├── preprocess              # Normalize / clean / deduplicate raw data
│   │   ├── __init__.py
│   │   └── normalize.py
│   │
│   ├── load                    # Load preprocessed data into Postgres
│   │   ├── __init__.py
│   │   └── loader.py
│   │
│   ├── utils                   # Config, logging, etc.
│   │   ├── __init__.py
│   │   ├── config.py
│   │   └── logger.py
│   │
│   └── pipeline.py             # Orchestrates all the steps end-to-end
│
├── dbt                         # dbt project directory
│   ├── models
│   │   ├── staging             # Raw to cleaned staging models
│   │   └── marts               # Final models / business logic
│   ├── seeds
│   └── snapshots
│
├── docker-compose.yml          # Docker Compose file to run Postgres
├── main.py                     # Entrypoint that runs the pipeline
├── README.md
└── requirements.txt
```

## dbt

If you are not familiar with dbt, you can check their [sandox project](https://github.com/dbt-labs/jaffle-shop/)
on GitHub to get started. You can also check the [dbt documentation](https://docs.getdbt.com/docs/introduction)
for more information.
