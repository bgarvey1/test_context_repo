# First Key Homes Context Project

This repository contains data analysis tools and context for First Key Homes operations.

## Project Structure

- `First Key Homes Glossary.txt` - Business terminology and definitions
- `non_eviction_criteria.json` - Non-eviction turn criteria and classification rules
- `.gitignore` - Git ignore patterns for data science projects
- `README.md` - This file

## Business Definitions

### First Key Homes (FKH)
**FKH** is an abbreviation for **First Key Homes**, a Single Family Rental (SFR) Property company based in Atlanta that currently manages approximately 52,000 homes across 29 markets.

### Non-Eviction Turn Criteria

A **non-eviction turn** is defined as a property turnover where tenant departure is NOT related to eviction proceedings or involuntary removal by landlord. This classification uses an exclusion-based method on the `PastTenantMoveOutReason` field in the jobs_cache table.

#### Exclusion Criteria (Eviction-Related)
The following move-out reasons are classified as **eviction-related** and excluded from non-eviction turns:

- **Eviction** - Any move-out reason containing 'Eviction'
- **Lease Violation** - Any move-out reason containing 'Lease Violation'
- **Non-Eviction Skip** - Tenant abandonment without proper notice
- **Do Not Renew** - Landlord decision not to renew lease
- **Resident Transition Program** - Company-assisted tenant relocation program

#### Non-Eviction Categories
Non-eviction turns are categorized into five main types:

##### 1. Voluntary - Planned (33.10%)
- **Description**: Positive tenant outcomes - purchasing own home
- **Examples**: Home Purchase

##### 2. Voluntary - Life Change (23.43%)
- **Description**: Natural life transitions requiring relocation
- **Examples**: 
  - Job Change, Loss, or Retirement
  - Change in Household (marriage, divorce, new child, roommate change)
  - Military Relocation or Deployment

##### 3. Voluntary - Financial (17.57%)
- **Description**: Economic factors making current housing unaffordable
- **Examples**:
  - Rent Increase/Price is Unaffordable
  - Home is Too Expensive Relative to Similar Options
  - Home Utilities Expense or Additional Expenses Too High

##### 4. Voluntary - Preference (14.33%)
- **Description**: Lifestyle and location preferences
- **Examples**:
  - Location/Commute issues
  - Size or Layout of the Home
  - Safety or Security concerns
  - Schools
  - Neighbors

##### 5. Voluntary - Dissatisfaction (9.95%)
- **Description**: Service or property condition issues
- **Examples**:
  - Consistent Maintenance Issues not Resolved
  - Customer Service Did Not Meet Expectations
  - Quality or Condition of the Home

#### Cost Analysis
- **Average Non-Eviction Cost**: $4,496
- **Average Eviction-Related Cost**: $7,023
- **Cost Difference**: Eviction-related turns cost 56.2% more than non-eviction turns
- **Characteristics**: Lower cost volatility and more predictable seasonal patterns

#### Operational Metrics
- **Volume**: Higher frequency than eviction turns (e.g., 93.2 jobs/month in Atlanta market)
- **Seasonality**:
  - **Winter Peak**: Home purchases dominate (Dec-Feb)
  - **Summer Peak**: Job changes dominate (Jul-Aug)
  - **Spring Focus**: Affordability pressure (Apr-Jun)
- **Retention Opportunity**: High - represents voluntary departures that may be preventable

#### SQL Implementation
```sql
SELECT * FROM jobs_cache j 
JOIN static_table_cache s ON j.bkMasterProperty = s.bkMasterProperty 
WHERE j.JobType = 'Turnover' 
  AND j.PastTenantMoveOutReason IS NOT NULL 
  AND j.PastTenantMoveOutReason != '' 
  AND j.PastTenantMoveOutReason NOT LIKE '%Eviction%' 
  AND j.PastTenantMoveOutReason NOT LIKE '%Lease Violation%' 
  AND j.PastTenantMoveOutReason NOT LIKE '%Non-Eviction Skip%' 
  AND j.PastTenantMoveOutReason NOT LIKE '%Do Not Renew%' 
  AND j.PastTenantMoveOutReason NOT LIKE '%Resident Transition Program%'
```

#### Data Quality Requirements
- JobType must equal 'Turnover'
- PastTenantMoveOutReason must not be null or empty
- Apply case-insensitive pattern matching for exclusion criteria
- Verify turnover job completion date is valid

---

## Getting Started

This project is designed for data analysis and business intelligence work related to First Key Homes operations.

### Prerequisites

- Python 3.8+
- Git

### Setup

1. Clone this repository
2. Set up a Python virtual environment (recommended)
3. Install required dependencies as they are added

## Contributing

When adding new files or data:
- Follow the established naming conventions
- Update documentation as needed
- Ensure sensitive data is properly excluded via `.gitignore`

## Data Handling

This project may contain sensitive business data. Please ensure:
- No sensitive data is committed to version control
- Proper data governance practices are followed
- Access controls are maintained

---

*Analysis based on 12,555 non-eviction turns across 29 markets over 12 months (Last updated: 2025-06-27)*
