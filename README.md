# Naviel Capital Investment Database

A comprehensive SQLite-based investment portfolio management system built in Google Colab that tracks companies, financials, investments, and SaaS metrics for Naviel Capital.

## 📊 Overview

This project demonstrates how to build a robust investment tracking database using SQLite and Python, complete with realistic data for African tech and venture-backed companies. The database enables sophisticated financial analysis, portfolio performance tracking, and investment valuation monitoring.

## 🎯 Key Features

- **Multi-table relational database** with foreign key relationships
- **Portfolio tracking** for companies at different stages (Early, Growth, Mature)
- **Financial metrics** including revenue, EBITDA, profitability, and debt analysis
- **Investment performance** monitoring with entry valuations and current valuations
- **SaaS-specific metrics** (ARR, MRR, churn rate, CAC, LTV, NRR)
- **Advanced SQL queries** for financial analysis and reporting

## 📋 Database Schema

### Tables

#### 1. **companies**
Stores information about portfolio companies.

| Column | Type | Description |
|--------|------|-------------|
| company_id | INTEGER PRIMARY KEY | Unique company identifier |
| company_name | TEXT NOT NULL | Company name |
| sector | TEXT | Industry sector |
| country | TEXT | Country of operation |
| founded_year | INTEGER | Year company was founded |
| stage | TEXT | Growth stage (Early, Growth, Mature) |
| status | TEXT | Investment status (Portfolio, Exited, Prospect) |

#### 2. **financials**
Contains financial performance data by fiscal year.

| Column | Type | Description |
|--------|------|-------------|
| financial_id | INTEGER PRIMARY KEY | Unique record identifier |
| company_id | INTEGER FK | Reference to companies table |
| fiscal_year | INTEGER | Financial year |
| revenue | REAL | Revenue in millions USD |
| ebitda | REAL | EBITDA in millions USD |
| net_income | REAL | Net income in millions USD |
| total_debt | REAL | Total debt in millions USD |
| cash | REAL | Cash position in millions USD |
| employees | INTEGER | Number of employees |

#### 3. **investments**
Tracks individual investment transactions and valuations.

| Column | Type | Description |
|--------|------|-------------|
| investment_id | INTEGER PRIMARY KEY | Unique investment identifier |
| company_id | INTEGER FK | Reference to companies table |
| investment_date | TEXT | Date of investment |
| investment_amount | REAL | Amount invested in millions USD |
| equity_stake | REAL | Percentage of company owned |
| entry_valuation | REAL | Company valuation at investment in millions USD |
| current_valuation | REAL | Current estimated valuation in millions USD |
| lead_partner | TEXT | Lead partner managing the investment |

#### 4. **saas_metrics**
SaaS-specific performance metrics by quarter.

| Column | Type | Description |
|--------|------|-------------|
| metric_id | INTEGER PRIMARY KEY | Unique metric record identifier |
| company_id | INTEGER FK | Reference to companies table |
| fiscal_year | INTEGER | Fiscal year |
| fiscal_quarter | INTEGER | Quarter (1-4) |
| arr | REAL | Annual Recurring Revenue in millions USD |
| mrr | REAL | Monthly Recurring Revenue in millions USD |
| churn_rate | REAL | Customer churn rate percentage |
| nrr | REAL | Net Revenue Retention percentage |
| cac | REAL | Customer Acquisition Cost in USD |
| ltv | REAL | Customer Lifetime Value in USD |
| customers | INTEGER | Number of customers |

## 🚀 Getting Started

### Prerequisites
- Python 3.7+
- pandas
- sqlite3 (included with Python)

### Installation & Setup

```python
import sqlite3
import pandas as pd

# Create database connection
conn = sqlite3.connect('investment_firm.db')
cursor = conn.cursor()
```

### Create Tables

The notebook includes sections to create all four tables with proper schema and relationships.

### Populate with Data

The project includes sample data for 8 companies across multiple sectors:
- TechFlow SaaS (Technology, Kenya)
- AfriPay Solutions (Fintech, Nigeria)
- HealthBridge (Healthcare, South Africa)
- LogiChain Africa (Logistics, Kenya)
- SolarGrid Kenya (Clean Energy, Kenya)
- EduTech Nairobi (Education, Kenya)
- AgriData Systems (Agriculture, Ghana)
- CloudBase Africa (Technology, Rwanda)

## 📊 Analysis Queries

### 1. Portfolio Companies
Lists all companies currently in the portfolio with their stage and sector:

```sql
SELECT company_name, sector, country, stage, status
FROM companies
WHERE status = 'Portfolio'
ORDER BY sector
```

**Output:** 6 portfolio companies across Technology, Fintech, Healthcare, Logistics, and Clean Energy sectors.

### 2. 2024 Revenue Ranking
Shows revenue performance and EBITDA margins for 2024:

```sql
SELECT c.company_name, f.fiscal_year, f.revenue, f.ebitda,
       ROUND(f.ebitda / f.revenue * 100, 1) AS ebitda_margin_pct
FROM financials f
JOIN companies c ON f.company_id = c.company_id
WHERE f.fiscal_year = 2024
ORDER BY f.revenue DESC
```

**Key Insights:**
- HealthBridge leads with $26.8M revenue
- TechFlow SaaS has highest margin at 29.8%
- All companies showing strong profitability

### 3. Portfolio Valuation & Returns
Tracks investment performance and valuation growth:

```sql
SELECT c.company_name, i.investment_amount, i.equity_stake,
       i.entry_valuation, i.current_valuation,
       ROUND((i.current_valuation - i.entry_valuation) / 
             i.entry_valuation * 100, 1) AS valuation_growth_pct
FROM investments i
JOIN companies c ON i.company_id = c.company_id
ORDER BY valuation_growth_pct DESC
```

**Performance Highlights:**
- HealthBridge: 237.7% valuation growth
- TechFlow SaaS: 216.7% valuation growth
- Average portfolio valuation growth: ~200%

### 4. SaaS Metrics Analysis
Quarter-over-quarter growth trends for SaaS companies:

```sql
SELECT company_name, fiscal_year, fiscal_quarter, arr, mrr, 
       churn_rate, nrr, cac, ltv, customers
FROM saas_metrics sm
JOIN companies c ON sm.company_id = c.company_id
ORDER BY fiscal_year DESC, fiscal_quarter DESC
```

**SaaS Company Trends:**
- TechFlow SaaS: ARR growing from $3.8M (Q1 2023) to $8.9M (Q4 2024)
- AfriPay Solutions: Strong NRR above 100%, sustainable growth metrics

## 📈 Portfolio Statistics

| Metric | Value |
|--------|-------|
| Total Companies | 8 |
| Portfolio Companies | 6 |
| Total Invested Capital | $24.5M |
| Average Entry Valuation | $20.4M |
| Average Current Valuation | $64.8M |
| Portfolio Valuation Growth | ~200% |

## 🔧 Technology Stack

- **Database:** SQLite3
- **Data Analysis:** pandas
- **Environment:** Google Colab (Jupyter Notebook)
- **Language:** Python 3

## 💡 Use Cases

1. **Investment Performance Tracking** - Monitor valuation growth and returns
2. **Financial Health Analysis** - Track revenue, profitability, and EBITDA trends
3. **Portfolio Reporting** - Generate investor reports with performance metrics
4. **SaaS Metrics Monitoring** - Track key SaaS KPIs like ARR, churn, and LTV
5. **Due Diligence** - Historical financial data for company analysis
6. **Partner Performance** - Evaluate lead partner track records

## 📝 Example Queries & Helper Function

The notebook includes a `query()` helper function for clean execution:

```python
def query(sql, description=""):
    if description:
        print(f"\n{'═'*50}")
        print(f"  {description}")
        print('═'*50)
    result = pd.read_sql_query(sql, conn)
    print(result.to_string(index=False))
    return result
```

## 🌍 Geographic Coverage

- **Kenya:** 4 companies (TechFlow SaaS, LogiChain Africa, SolarGrid Kenya, EduTech Nairobi)
- **Nigeria:** 1 company (AfriPay Solutions)
- **South Africa:** 1 company (HealthBridge)
- **Rwanda:** 1 company (CloudBase Africa)
- **Ghana:** 1 company (AgriData Systems)

## 📚 Data Quality & Validation

- All tables use proper PRIMARY KEY constraints
- Foreign key relationships enforce referential integrity
- INSERT OR REPLACE ensures data consistency
- Financial data spans 2022-2024 for trend analysis

## 🎓 Learning Outcomes

This project demonstrates:
- SQLite database design and normalization
- Complex SQL JOIN operations
- Data aggregation and financial calculations
- Integration with pandas for data analysis
- Google Colab notebook best practices

## 🤝 Contributing

This is a portfolio/learning project. For improvements or modifications, please create a branch and submit changes.

## 📄 License

Open source - feel free to use as a template for investment tracking databases.

## 🔗 Resources

- [SQLite Documentation](https://www.sqlite.org/docs.html)
- [Pandas Documentation](https://pandas.pydata.org/docs/)
- [Google Colab Guide](https://colab.research.google.com/notebooks/intro.ipynb)

## 📧 Contact

For questions or suggestions about this investment database project, please reach out through the GitHub repository.

---

**Project Created:** May 2026  
**Database Name:** Naviel Capital Investment Database  
**Version:** 1.0
