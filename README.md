# Restaurant Violations ETL Pipeline

A production-ready ETL (Extract, Transform, Load) pipeline that extracts restaurant inspection and violation data from the NYC Open Data API, transforms it through data normalization and cleaning processes, and loads it into a PostgreSQL database (Supabase) with proper schema design and referential integrity.

## Project Overview

This project demonstrates end-to-end ETL pipeline development, from API data extraction to normalized database storage. The pipeline processes NYC Department of Health restaurant violation records, transforming flat CSV-like data into a normalized relational database structure optimized for querying and analysis.

## Technologies Used

- **Python 3.13.5** - Core programming language
- **Pandas** - Data manipulation and transformation
- **Socrata (sodapy)** - NYC Open Data API client
- **SQLAlchemy** - Database ORM and connection management
- **PostgreSQL** - Relational database (via Supabase)
- **python-dotenv** - Environment variable management
- **Jupyter Notebook** - Development and execution environment

## Data Source

- **Source**: [NYC Restaurant Inspection Results Dataset](https://data.cityofnewyork.us/Health/DOHMH-New-York-City-Restaurant-Inspection-Results/43nn-pn8j)
- **API**: NYC Open Data Portal (Socrata API)
- **Volume**: Configurable (default: 10,000 records)
- **Update Frequency**: Real-time data from NYC Department of Health and Mental Hygiene

## 🗄️ Database Schema

The pipeline implements a normalized database design with three main tables:

### 1. `restaurants` Table
Stores unique restaurant information:
- **Primary Key**: `camis` (unique restaurant identifier)
- **Fields**: `dba` (restaurant name), `building`, `street`, `zipcode`, `boro`, `phone`, `cuisine_description`

### 2. `violation_codes` Table
Maintains a reference of all violation codes and descriptions:
- **Primary Key**: `violation_code`
- **Fields**: `violation_code`, `violation_description`

### 3. `rest_to_violations` Table (Mapping/Junction Table)
Links restaurants to their violations with inspection metadata:
- **Composite Primary Key**: (`camis`, `violation_code`, `inspection_date`)
- **Foreign Keys**: 
  - `camis` → `restaurants.camis`
  - `violation_code` → `violation_codes.violation_code`
- **Fields**: `inspection_date`, `action`, `critical_flag`, `grade`, `score`, `grade_date`, `record_date`, `inspection_type`

## ETL Process Flow

### 1. **Extract**
- Connects to NYC Open Data API via Socrata client
- Retrieves restaurant violation records (configurable batch size)
- Converts API response to pandas DataFrame

### 2. **Transform**
The transformation phase includes multiple cleaning and normalization steps:

- **Data Cleaning**:
  - Removes unnecessary computed region columns
  - Replaces missing borough values (`'0'` → `'Unknown'`)
  - Handles null values in violation fields
  
- **Data Type Conversion**:
  - Standardizes date formats (`inspection_date`, `grade_date`, `record_date`)
  - Converts to `YYYY-MM-DD` format for database compatibility

- **Data Normalization**:
  - Extracts unique violation codes and descriptions
  - Separates restaurant information from violation records
  - Creates junction table structure for many-to-many relationships

### 3. **Load**
- Establishes secure connection to PostgreSQL database (Supabase)
- Creates database tables with proper primary keys and foreign key constraints
- Implements upsert logic to handle duplicate records gracefully
- Uses `ON CONFLICT DO NOTHING` for idempotent operations

## Key Features

### Data Quality & Reliability
- **Duplicate Handling**: Automatic deduplication using composite primary keys
- **Error Handling**: Graceful handling of API throttling and connection issues
- **Data Validation**: Type checking and format standardization

### Database Design Best Practices
- **Normalized Schema**: 3NF normalization to eliminate data redundancy
- **Referential Integrity**: Foreign key constraints ensure data consistency
- **Composite Keys**: Proper use of composite primary keys for junction tables
- **Default Values**: Sensible defaults for missing or null data

### Production-Ready Code
- **Environment Variables**: Secure credential management via `.env` file
- **Modular Functions**: Reusable, testable functions for each ETL stage
- **Error Logging**: Connection status and data loading feedback
- **Scalable Architecture**: Easily configurable batch sizes and limits

## Setup Instructions

### Prerequisites
```bash
pip install pandas sodapy python-dotenv sqlalchemy psycopg2-binary
```

### Environment Configuration

1. Create a `.env` file in the project root:
```env
DB_PASSWORD=your_supabase_password
DB_URL=your_supabase_project_url
```

2. For improved API performance (optional):
```env
SOCRATA_APP_TOKEN=your_app_token
```

### Running the Pipeline

1. Open `Restaurant_violations_ETL.ipynb` in Jupyter Notebook
2. Execute cells sequentially to run the complete ETL process
3. The pipeline will:
   - Extract data from NYC Open Data API
   - Transform and clean the data
   - Load into PostgreSQL database

## Performance Metrics

- **Processing Speed**: ~10,000 records processed per run
- **Data Quality**: Automatic duplicate detection and removal
- **Database Efficiency**: Normalized schema reduces storage and improves query performance

## Use Cases

This ETL pipeline enables:
- **Food Safety Analytics**: Analyze violation trends across restaurants
- **Compliance Monitoring**: Track inspection scores and grades over time
- **Location-Based Analysis**: Identify violation patterns by borough, zip code, or cuisine type
- **Historical Reporting**: Maintain audit trail with inspection dates and record dates

## Security Features

- SSL-encrypted database connections (`sslmode: require`)
- Environment variable-based credential management
- No hardcoded secrets or API keys

## Future Enhancements

- [ ] Implement incremental data loading (delta updates)
- [ ] Add data validation and quality checks
- [ ] Create scheduled automation (Airflow, cron, etc.)
- [ ] Add API token authentication for improved rate limits
- [ ] Implement error handling and retry logic
- [ ] Add data quality monitoring and alerts

## License

This project is for educational and portfolio purposes.

## Author

Built as part of a data engineering portfolio demonstrating ETL pipeline development expertise.

---

**Note**: This pipeline demonstrates proficiency in:
- API integration and data extraction
- Data transformation and normalization
- Database design and schema management
- Python data engineering best practices
- Production-ready code structure

