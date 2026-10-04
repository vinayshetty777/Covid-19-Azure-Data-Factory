# COVID-19 Azure Data Factory Pipeline

A comprehensive cloud-native data engineering solution that ingests, transforms, and analyzes COVID-19 epidemiological data using Azure Data Factory, demonstrating enterprise-grade data pipeline architecture.

## 🎯 Project Overview

This project demonstrates a complete data engineering workflow for COVID-19 analysis using Microsoft Azure cloud services. It ingests data from public health sources (ECDC and Eurostat), applies sophisticated transformations via PySpark, and delivers analytics-ready datasets for business intelligence and research.

**Learning Objectives:**
- Master Azure Data Factory pipeline orchestration
- Implement multi-layered data architecture (Bronze-Silver-Gold)
- Process large-scale public health datasets
- Integrate cloud storage with analytics platforms
- Build CI/CD automation with GitHub

## 🏗️ Architecture

```
[Public Data Sources]
├→ ECDC (European COVID-19 Data)
└→ Eurostat (Demographics & Economics)
    ↓
[Azure Data Factory]
├→ Linked Services (Connection Management)
├→ Datasets (Schema Definition)
├→ Pipelines (ETL Orchestration)
└→ Triggers (Automation)
    ↓
[Azure Data Lake Storage - Layered]
├→ Bronze Layer (Raw data)
├→ Silver Layer (Cleaned & validated)
└→ Gold Layer (Analytics-ready)
    ↓
[Processing Layer]
├→ HDInsight (Spark Clusters)
├→ Databricks Notebooks
└→ Data Transformation
    ↓
[Analytics & Visualization]
├→ Azure SQL Database
└→ Power BI Reports
```

## 🛠️ Tech Stack

- **Cloud Platform:** Microsoft Azure
- **Core Services:**
  - Azure Data Factory (ADF)
  - Azure Data Lake Storage Gen2
  - Azure SQL Database
  - HDInsight with Spark
- **Languages:** Python, PySpark, SQL, PowerShell
- **CI/CD:** GitHub Actions, Azure DevOps
- **Visualization:** Power BI, Excel
- **Data Sources:** ECDC API, Eurostat

## 📁 Project Structure

```
Covid-19-Azure-Data-Factory/
├── src/
│   ├── adf-pipelines/          # ADF Pipeline definitions
│   │   ├── ingest_pipeline.json
│   │   ├── transform_pipeline.json
│   │   └── load_pipeline.json
│   ├── linked-services/        # Azure Service connections
│   │   ├── adls_linked_service.json
│   │   ├── sql_linked_service.json
│   │   └── storage_linked_service.json
│   ├── datasets/               # Data schema definitions
│   │   ├── ecdc_dataset.json
│   │   ├── eurostat_dataset.json
│   │   └── sql_dataset.json
│   ├── notebooks/              # Spark transformation logic
│   │   ├── data_cleaning.ipynb
│   │   ├── data_enrichment.ipynb
│   │   └── aggregations.ipynb
│   └── scripts/
│       ├── setup_hdinsight.sh
│       └── configure_adf.ps1
├── data/
│   ├── lookup_tables/          # Reference data
│   │   ├── countries.csv
│   │   ├── regions.csv
│   │   └── date_dimensions.csv
│   └── sample/
│       └── sample_covid_data.csv
├── reports/                     # Power BI & Analysis
│   ├── covid_dashboard.pbix
│   └── regional_analysis.xlsx
├── terraform/                  # Infrastructure as Code
│   ├── variables.tf
│   ├── main.tf
│   └── outputs.tf
├── github/workflows/           # CI/CD workflows
│   ├── deploy_adf.yaml
│   └── run_tests.yaml
├── docs/                       # Documentation
│   ├── architecture.md
│   ├── data_dictionary.md
│   └── runbook.md
└── README.md
```

## 📊 Data Sources

### ECDC (European Centre for Disease Prevention and Control)

- **Data:** COVID-19 cases, deaths, testing rates
- **Granularity:** Country and region level
- **Frequency:** Daily updates
- **Format:** JSON/CSV via API

### Eurostat

- **Data:** Population, economic indicators, demographics
- **Granularity:** Country and regional levels
- **Frequency:** Quarterly/Annual
- **Format:** XLSX, CSV

### Lookup Tables

- Country codes and regions
- Date dimensions
- Geographic hierarchies
- Classification codes

## 🚀 Setup & Installation

### Prerequisites
- Azure subscription with Owner/Contributor permissions
- Azure CLI installed
- PowerShell 7+
- Terraform (for IaC deployment)
- Git for version control

### Azure Resource Deployment

1. **Create Resource Group:**
   ```bash
   az group create \
     --name covid-rg \
     --location eastus
   ```

2. **Create Storage Account (Data Lake):**
   ```bash
   az storage account create \
     --name covidadlsgen2 \
     --resource-group covid-rg \
     --kind StorageV2 \
     --hierarchical-namespace true
   ```

3. **Create SQL Database:**
   ```bash
   az sql server create \
     --name covid-sql-server \
     --resource-group covid-rg \
     --admin-user sqlAdmin \
     --admin-password YourPassword123!
   
   az sql db create \
     --server covid-sql-server \
     --name covid_analytics \
     --resource-group covid-rg
   ```

4. **Deploy with Terraform:**
   ```bash
   cd terraform/
   terraform init
   terraform plan
   terraform apply
   ```

5. **Create Azure Data Factory:**
   - Use Azure Portal UI
   - Or deploy via ARM template

### Database Setup

```bash
# Connect to SQL Database
sqlcmd -S covid-sql-server.database.windows.net \
       -U sqlAdmin \
       -P YourPassword123!

# Run setup script
:r sql/create_tables.sql
```

## 🔄 ETL Workflow

### Stage 1: Ingestion (Bronze Layer)

```
Raw ECDC Data → ADLS Raw folder
Raw Eurostat Data → ADLS Raw folder
↓
ADF Copy Activity
↓
ADLS Bronze Layer (immutable, audit trail)
```

### Stage 2: Validation & Cleaning (Silver Layer)

```
Bronze Layer Data
↓
[Spark Notebook: data_cleaning.ipynb]
├→ Schema validation
├→ Null/outlier handling
├→ Type conversion
├→ Deduplication
└→ Format standardization
↓
ADLS Silver Layer (validated, deduplicated)
```

### Stage 3: Transformation & Aggregation (Gold Layer)

```
Silver Layer Data
↓
[Spark Notebook: data_enrichment.ipynb]
├→ Join with lookup tables
├→ Geographic enrichment
├→ Time-series features
└→ Business metrics calculation
↓
[Spark Notebook: aggregations.ipynb]
├→ Daily summaries
├→ Regional aggregations
├→ Trend calculations
└→ KPI computation
↓
ADLS Gold Layer (analytics-ready)
↓
Azure SQL Database (dimensional model)
```

### Stage 4: Analytics & Visualization

```
Gold Layer Data
↓
Power BI / Excel
↓
Interactive Dashboards & Reports
```

## 📈 Data Processing Examples

### Example: Regional COVID Statistics

```python
# PySpark transformation
df = spark.read.parquet("adls/silver/covid_data")

regional_stats = df \
    .groupBy("region", "date") \
    .agg(
        sum("cases").alias("daily_cases"),
        sum("deaths").alias("daily_deaths"),
        sum("tests").alias("daily_tests")
    ) \
    .withColumn("case_fatality_rate", 
                col("daily_deaths") / col("daily_cases"))

regional_stats.write \
    .mode("overwrite") \
    .parquet("adls/gold/regional_statistics")
```

### Example: Time-Series Analysis

```sql
-- SQL aggregation for trends
SELECT 
    date,
    region,
    AVG(daily_cases) OVER (
        PARTITION BY region 
        ORDER BY date 
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ) AS cases_7day_avg,
    LAG(daily_cases) OVER (PARTITION BY region ORDER BY date) AS prev_day_cases
FROM covid_analytics.dbo.daily_statistics
ORDER BY date DESC;
```

## 🔧 Configuration

### Linked Service: Azure Data Lake

```json
{
  "name": "AzureDataLakeStorage",
  "type": "AzureBlobFS",
  "typeProperties": {
    "url": "https://covidadlsgen2.dfs.core.windows.net",
    "accountKey": "your-storage-key"
  }
}
```

### Dataset: ECDC Data

```json
{
  "name": "ECDC_Dataset",
  "type": "DelimitedText",
  "linkedServiceName": "AzureDataLakeStorage",
  "typeProperties": {
    "location": {
      "type": "AzureBlobFSLocation",
      "fileSystem": "ecdc",
      "folderPath": "covid-data"
    },
    "columnDelimiter": ",",
    "escapeChar": "\\"
  }
}
```

## 📊 Key Metrics & KPIs

- **Daily Case Growth Rate** - Rate of change in cases
- **Case Fatality Rate** - Deaths / Cases
- **Testing Rate** - Tests per 100,000 population
- **Regional Variation** - Comparative analysis across regions
- **Trend Analysis** - 7-day and 14-day moving averages

## 📈 Power BI Dashboard Components

1. **Overview Dashboard**
   - Global case statistics
   - Deaths by region
   - Testing metrics

2. **Regional Analysis**
   - Regional comparison charts
   - Geographic heat maps
   - Regional trends

3. **Trend Analysis**
   - Time-series charts
   - Growth rate trends
   - Forecast visualizations

4. **Data Quality Dashboard**
   - Data freshness metrics
   - Missing data indicators
   - Quality scores

## 🧪 Testing

### Data Quality Tests

```python
# Pytest for data validation
def test_data_completeness():
    df = spark.read.parquet("adls/bronze/covid_data")
    assert df.filter(col("date").isNull()).count() == 0
    assert df.filter(col("country").isNull()).count() == 0
```

### Pipeline Tests

```bash
# Validate pipeline definitions
az datafactory pipeline validate \
  --factory-name covid-adf \
  --name ingest_pipeline
```

## 🐛 Troubleshooting

### Issue: Pipeline Execution Timeout
- Increase ADF DIU (Data Integration Units)
- Optimize Spark transformation logic
- Check data volume vs. resource allocation

### Issue: Data Quality Issues
- Verify source data schema hasn't changed
- Check lookup table completeness
- Review transformation logic in notebooks

### Issue: Cost Overruns
- Monitor resource utilization
- Schedule pipelines off-peak
- Optimize storage retention policies

## 🔐 Security & Compliance

✅ **Implemented:**
- Azure AD authentication
- Data encryption at rest
- HTTPS for data transit
- Audit logging enabled
- GDPR-compliant data handling

## 📚 Best Practices

✅ **Do:**
- Use managed identities for ADF
- Implement retry policies
- Monitor pipeline performance
- Version control all definitions
- Document assumptions

❌ **Don't:**
- Store credentials in code
- Skip data validation
- Ignore cost optimization
- Hardcode values

## 🤝 Contributing

Contributions welcome! Please:
1. Create feature branch
2. Update pipeline definitions
3. Add documentation
4. Submit PR with testing details

## 📄 License

This project is open source under MIT License.

## 🔗 Related Resources

- [Azure Data Factory Documentation](https://docs.microsoft.com/azure/data-factory/)
- [Apache Spark Documentation](https://spark.apache.org/docs/latest/)
- [Power BI Guide](https://docs.microsoft.com/power-bi/)
- [ECDC Data](https://www.ecdc.europa.eu/)

---

**Project Status:** Complete / Maintained  
**Last Updated:** 2026-05-20  
**Data Sources:** ECDC, Eurostat (Current)
