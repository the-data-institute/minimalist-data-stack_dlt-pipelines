# DLT Moco Pipeline

A data pipeline using [dlt (data load tool)](https://dlthub.com/) to extract data from the Moco API and load it to a filesystem destination.

This repository goes hand in hand with our [Pipelines Perspective story](https://medium.com/@mkamysz/the-minimalists-data-stack-integration-pipelines-af3e00037aa7
) on how to build a minimalist data stack.

## Overview

This pipeline extracts data from the Moco time tracking and project management API, including:
- Users
- Activities (time entries)
- Invoice payments  
- Purchase budgets
- Purchase payments
- Companies

The data is loaded to a local filesystem in CSV format with incremental loading support for time-based resources.

## Prerequisites

- Python 3.13+
- uv package manager
- Moco API access with bearer token

## Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd dlt-pipeline
```

2. Install dependencies using uv:
```bash
uv sync
```

## Configuration

1. Copy the secrets example file:
```bash
cp .dlt/secrets-example.toml .dlt/secrets.toml
```

2. Edit `.dlt/secrets.toml` and add your Moco API key:
```toml
[sources.moco_pipeline]
api_key = "your-moco-api-key-here"

[destination.filesystem]
bucket_url = "data"
```

## Usage

Run the pipeline:
```bash
uv run python sources/moco_pipeline.py
```

Or activate the virtual environment first:
```bash
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
python sources/moco_pipeline.py
```

## Data Loading Strategy

- **Users**: Full refresh on each run
- **Activities**: Incremental loading based on `updated_at` timestamp (from 2024-04-01)
- **Invoice Payments**: Incremental loading based on `updated_at` timestamp (from 2024-04-01)
- **Purchase Budgets**: Full refresh on each run
- **Purchase Payments**: Incremental loading based on `updated_at` timestamp (from 2024-04-01)
- **Companies**: Full refresh on each run

## Output

Data is saved to the `data/` directory in CSV format with the following structure:
```
data/
- users.csv
- activities.csv
- invoice_payments.csv
- purchase_budgets.csv
- purchase_payments.csv
- companies.csv
```

## Pipeline Configuration

The pipeline is configured in `sources/moco_pipeline.py` with:
- Base URL: `https://thedatainstitute.mocoapp.com/api/v1`
- Authentication: Bearer token
- Destination: Local filesystem
- File format: CSV
- Dataset name: `moco`

## Customization

To modify the pipeline:
1. Edit the `config` dictionary in `sources/moco_pipeline.py`
2. Add or remove resources as needed
3. Adjust date ranges or incremental loading parameters
4. Change the destination or file format in the `load_moco()` function

## Troubleshooting

- Ensure your API key has proper permissions for all endpoints
- Check that the date ranges are appropriate for your data
- Verify the output directory is writable
- Review dlt logs for detailed error information
