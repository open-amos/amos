# AMOS Starter

**AMOS Starter** is a dbt orchestrator package that provides a reference implementation for building a canonical data warehouse for private equity and alternative investment data with AMOS.

## What is AMOS Starter?

AMOS Starter is the orchestrator that imports and coordinates two implementation packages to deliver a complete data warehouse solution.

**As an orchestrator, AMOS Starter:**
- **Imports implementation packages** - `amos_source_example` and `amos_core`
- **Configures seed materialization** - Controls where source system data is loaded across databases
- **Manages the dbt profile** - Defines which data warehouse connection to use
- **Provides the execution context** - All dbt commands run from this package

**The complete solution provides:**
- **Multi-source integration patterns** - Combine data from CRM, fund administration, portfolio management, and accounting systems
- **Cloud-agnostic architecture** - Works with Snowflake, BigQuery, Databricks, and other data warehouses
- **Canonical data model** - Pre-built dimensional model for funds, investors, companies, instruments, and transactions
- **Entity resolution framework** - Deduplicate and merge entities across source systems
- **Production-ready patterns** - Staging, intermediate, and marts layers with proper testing and documentation

**Implementation packages:**
- `amos_source_example` - Example source system integrations with staging and intermediate transformations
- `amos_core` - Canonical dimensional model (marts layer)

## Architecture

The project uses a four-layer architecture:

1. **RAW** - Source system data (loaded via seeds or external ingestion)
2. **STAGING** - Mechanical cleanup and standardization
3. **INTERMEDIATE** - Business logic, entity resolution, and deduplication
4. **CORE** - Canonical dimensional model (facts and dimensions)

## Getting Started

### Prerequisites

- Python 3.8 or higher
- Data warehouse (Snowflake, BigQuery, Databricks, etc.)
- Database credentials with appropriate permissions

### 1. Install dbt

Install dbt with the adapter for your data warehouse:

```bash
# For Snowflake
pip install dbt-snowflake

# For BigQuery
pip install dbt-bigquery

# For Databricks
pip install dbt-databricks

# For PostgreSQL
pip install dbt-postgres
```

Verify installation:
```bash
dbt --version
```

For more installation options (including dbt Cloud), see the [official dbt documentation](https://docs.getdbt.com/docs/core/installation).

### 2. Configure Your Data Warehouse

Create four databases in your data warehouse:

```sql
-- Example for Snowflake/Databricks
CREATE DATABASE IF NOT EXISTS RAW;
CREATE DATABASE IF NOT EXISTS STAGING;
CREATE DATABASE IF NOT EXISTS INTERMEDIATE;
CREATE DATABASE IF NOT EXISTS CORE;

-- Grant appropriate permissions to your dbt role
GRANT ALL ON DATABASE RAW TO ROLE <your_role>;
GRANT ALL ON DATABASE STAGING TO ROLE <your_role>;
GRANT ALL ON DATABASE INTERMEDIATE TO ROLE <your_role>;
GRANT ALL ON DATABASE CORE TO ROLE <your_role>;
```

For BigQuery, create four datasets. For other platforms, adjust accordingly.

### 3. Set Up Environment Variables (Optional)

You may configure database names using environment variables. Otherwise, defaults will apply. 

Set the following variables in your shell profile:

```bash
export DBT_RAW_DATABASE=RAW
export DBT_STAGING_DATABASE=STAGING
export DBT_INTERMEDIATE_DATABASE=INTERMEDIATE
export DBT_CORE_DATABASE=CORE
export DBT_PROFILE=your_profile_name
```

**Optional**: Use `direnv` for automatic environment loading:
- Install `direnv` for your system (see [direnv.net](https://direnv.net/docs/installation.html))
- Configure your shell to hook into direnv (instructions vary by shell)
- Create a `.envrc` file in this directory with the variables above
- Run `direnv allow` once to authorize the `.envrc` file
- Environment variables will automatically load when you enter this directory

### 4. Configure dbt Profile

Set up your `~/.dbt/profiles.yml` with connection details for your data warehouse. Example:

```yaml
your_profile_name:
  target: dev
  outputs:
    dev:
      type: snowflake  # or bigquery, databricks, etc.
      account: your_account
      user: your_user
      # ... other connection details
```

### 5. Install Dependencies

```bash
dbt deps
```

### 6. Run the Project

```bash
# Load example source data
dbt seed

# Build all models
dbt run

# Run tests
dbt test

# Or execute everything in order
dbt build
```

## Project Structure

```
amos_runner/              # This orchestrator package
├── dbt_project.yml      # Profile and seed configurations
├── packages.yml         # Package dependencies
└── models/              # (empty - models live in imported packages)

amos_source_example/      # Source integration package
├── seeds/               # Example source system data
├── models/
│   ├── staging/        # Mechanical cleanup
│   └── intermediate/   # Business logic and entity resolution
└── macros/             # Reusable transformation macros

amos_core/               # Canonical model package
├── seeds/              # Core reference data
├── models/marts/       # Dimensional model
└── macros/             # Cross-platform compatibility macros
```

## Data Model Overview

The canonical model includes:

**Core Dimensions:**
- `dim_companies` - Portfolio companies and prospects
- `dim_funds` - Investment funds
- `dim_investors` - Limited partners and investors
- `dim_instruments` - Equity positions, loans, and other instruments
- `dim_currencies` - Currency reference data
- `dim_countries` - Country reference data
- `dim_industries` - Industry classifications

**Fact Tables:**
- `fct_transactions` - All financial transactions
- `fct_fund_snapshots` - Fund-level NAV and performance metrics
- `fct_instrument_snapshots` - Investment-level valuations
- `fct_company_valuations` - Company valuation history
- `fct_company_financials` - Company financial statements

## Customization

To adapt AMOS Starter for your organization:

1. **Replace example seeds** with your actual source system connections
2. **Modify staging models** to match your source system schemas
3. **Adjust entity resolution logic** in intermediate models
4. **Extend the canonical model** with additional dimensions or facts
5. **Update database names** in environment variables to match your naming conventions

## Support

AMOS Starter is a reference implementation. Adapt it to your specific needs and data warehouse platform.
