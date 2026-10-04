# COVID-19 Azure Data Factory

Cloud-native data engineering solution analyzing COVID-19 epidemiological data. Demonstrates multi-layered data architecture and Power BI analytics.

## 📋 Overview

This project demonstrates a complete data engineering workflow for COVID-19 analysis using Microsoft Azure cloud services. It ingests data from public health sources, applies sophisticated transformations, and delivers analytics-ready datasets for business intelligence and research.

**Tech:** Azure Data Factory, Data Lake Storage, HDInsight, Power BI, Python/PySpark  
**Data Sources:** ECDC, Eurostat  
**Scope:** Public health data analytics and visualization  
**Status:** ✅ Complete Project

---

## 🏗️ Architecture

```
[Public Data Sources]
├→ ECDC COVID-19 Data
└→ Eurostat Demographics
    ↓
[Azure Data Factory]
├→ Linked Services
├→ Datasets
├→ Pipelines
└→ Triggers
    ↓
[Azure Data Lake]
├→ Bronze (Raw)
├→ Silver (Cleaned)
└→ Gold (Analytics)
    ↓
[Processing & Analytics]
└→ Power BI Reports
```

---

## 🛠️ Tech Stack

| Component | Technology |
|-----------|------------|
| **Cloud** | Microsoft Azure |
| **ETL** | Azure Data Factory |
| **Storage** | Azure Data Lake Gen2 |
| **Database** | Azure SQL Database |
| **Processing** | HDInsight, PySpark |
| **Visualization** | Power BI, Excel |

---

## 📁 Project Structure

```
Covid-19-Azure-Data-Factory/
├── src/
│   ├── adf-pipelines/         # Pipeline definitions
│   ├── linked-services/       # Service connections
│   ├── datasets/              # Schema definitions
│   ├── notebooks/             # Spark transformations
│   └── scripts/               # Setup scripts
├── data/
│   ├── lookup_tables/         # Reference data
│   └── sample/                # Sample data
├── reports/                   # Power BI dashboards
├── terraform/                 # Infrastructure as Code
├── github/workflows/          # CI/CD pipelines
└── README.md
```

---

## 🚀 Quick Start

### Prerequisites
- Azure subscription
- Azure Data Factory
- Power BI (optional)
- Terraform (for IaC)

### Deployment

```bash
# Clone repository
git clone https://github.com/vinayshetty777/Covid-19-Azure-Data-Factory.git

# Create resource group
az group create \
  --name covid-rg \
  --location eastus

# Deploy with Terraform
cd terraform/
terraform init
terraform plan
terraform apply
```

---

## 📊 Data Flow

```
ECDC Data + Eurostat Data
    ↓
[Ingestion Pipeline]
→ Azure Data Lake (Raw/Bronze)
    ↓
[Validation Pipeline]
→ Data quality checks
→ Error logging
    ↓
[Transformation Pipeline]
├→ Cleansing
├→ Deduplication
└→ Enrichment
→ Azure Data Lake (Processed/Silver)
    ↓
[Aggregation Pipeline]
├→ Daily summaries
├→ Regional analysis
└→ Trend calculation
→ Azure Data Lake (Analytics/Gold)
    ↓
[Power BI Analysis]
```

---

## ✨ Key Features

- **Automated Data Ingestion** - From public health APIs
- **Multi-Layer Architecture** - Bronze-Silver-Gold pattern
- **Data Quality** - Validation and error handling
- **PySpark Processing** - Complex transformations
- **Power BI Dashboards** - Interactive visualizations
- **CI/CD Automation** - GitHub Actions integration

---

## 📈 Key Metrics

| Metric | Description |
|--------|------------|
| **Daily Cases** | New confirmed COVID cases |
| **Case Fatality Rate** | Deaths / Cases ratio |
| **Testing Rate** | Tests per 100K population |
| **Regional Variation** | Comparative analysis |
| **Trend Analysis** | 7-day and 14-day averages |

---

## 🔧 Database Setup

```bash
# Connect to SQL Database
sqlcmd -S covid-sql-server.database.windows.net \
       -U sqlAdmin

# Run setup script
:r sql/create_tables.sql
```

---

## 📊 Data Processing Examples

### Regional COVID Statistics

```python
df = spark.read.parquet("adls/silver/covid_data")

regional_stats = df \
    .groupBy("region", "date") \
    .agg(
        sum("cases").alias("daily_cases"),
        sum("deaths").alias("daily_deaths")
    )
```

### Time-Series Analysis

```sql
SELECT 
    date,
    region,
    AVG(daily_cases) OVER (
        PARTITION BY region 
        ORDER BY date 
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ) AS cases_7day_avg
FROM covid_analytics.dbo.daily_statistics
```

---

## 🧪 Testing

```bash
# Validate pipeline
az datafactory pipeline validate \
  --factory-name covid-adf \
  --name ingest_pipeline

# Check data quality
SELECT COUNT(*) FROM processed_table
WHERE required_column IS NULL
```

---

## 🐛 Troubleshooting

| Issue | Solution |
|-------|----------|
| Pipeline timeout | Increase ADF DIU capacity |
| Schema changes | Update dataset definitions |
| Data quality issues | Review transformation logic |
| Cost overruns | Optimize retention policies |

---

## 🔐 Best Practices

✅ **Do:**
- Use managed identities
- Implement retry policies
- Monitor performance
- Version control definitions
- Document assumptions

❌ **Don't:**
- Store credentials in code
- Skip data validation
- Ignore cost monitoring
- Hardcode values

---

## 🤝 Contributing

1. Create feature branch
2. Update pipeline definitions
3. Add documentation
4. Submit PR

---

## 📚 Resources

- [Azure Data Factory Docs](https://docs.microsoft.com/azure/data-factory/)
- [Apache Spark Docs](https://spark.apache.org/docs/)
- [Power BI Guide](https://docs.microsoft.com/power-bi/)
- [ECDC Data](https://www.ecdc.europa.eu/)

---

**Last Updated:** 2026-05-20  
**Data Status:** Current  
**Project Status:** Complete
