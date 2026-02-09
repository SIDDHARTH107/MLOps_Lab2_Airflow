# Weather ETL Pipeline using Apache Airflow & Astronomer Command Line Interface

## 📋 Project Overview
An end-to-end **ETL (Extract, Transform, Load)** data pipeline built with **Apache Airflow** that automatically fetches weather data, processes it, and stores it in a PostgreSQL database. This project demonstrates workflow orchestration, API integration, data transformation, and database operations.

### Pipeline Flow
```
Open-Meteo API → Extract Weather Data → Transform Data → Load to PostgreSQL
```

## 🎯 Project Objectives
- Build an automated data pipeline using Apache Airflow
- Implement ETL best practices with task dependencies
- Integrate external APIs for real-time data extraction
- Store structured data in a relational database
- Schedule and monitor pipeline execution

## 🛠️ Technologies Used
| Technology | Purpose |
|------------|---------|
| **Apache Airflow** | Workflow orchestration and scheduling |
| **Astronomer CLI** | Local Airflow development environment |
| **PostgreSQL** | Relational database for data storage |
| **Open-Meteo API** | Free weather data source |
| **Python** | ETL logic and data transformation |
| **Docker** | Containerization for Airflow components |

## 📁 Project Structure
```
IE7374_MLOps_Lab2/
├── dags/
│   └── etlweather.py          # Main DAG file with ETL logic
├── .astro/                     # Astro CLI configuration
├── include/                    # Additional resources
├── plugins/                    # Custom Airflow plugins
├── tests/                      # Unit tests
├── .dockerignore              # Docker ignore patterns
├── .gitignore                 # Git ignore patterns
├── Dockerfile                 # Airflow Docker configuration
├── packages.txt               # System-level dependencies
├── requirements.txt           # Python dependencies
└── README.md                  # Project documentation
```

## 🚀 Setup Instructions

### Prerequisites
Before starting, ensure you have:
- ✅ **Docker Desktop** (running)
- ✅ **Astro CLI** ([Installation Guide](https://docs.astronomer.io/astro/cli/install-cli))
- ✅ **Python 3.8+**
- ✅ **Git** (for version control)

### Installation Steps

#### 1. Clone the Repository
```bash
git clone https://github.com/SIDDHARTH107/MLOps_Lab2_Airflow.git
cd MLOps_Lab2_Airflow
```

#### 2. Start Airflow with Astro CLI
```bash
astro dev start
```
This will:
- Build Docker containers
- Start Airflow webserver, scheduler, and PostgreSQL
- Initialize the Airflow database
- Takes ~2-3 minutes on first run

#### 3. Access Airflow UI
Open your browser and navigate to:
```
http://localhost:8080
```

**Default Credentials:**
- Username: `admin`
- Password: `admin`

#### 4. Configure Connections

##### API Connection (Open-Meteo)
1. Navigate to: **Admin → Connections**
2. Click **"+"** to add new connection
3. Fill in the details:
   - **Connection Id**: `open_meteo_api`
   - **Connection Type**: `HTTP`
   - **Host**: `https://api.open-meteo.com`
4. Click **Save**

##### Database Connection (PostgreSQL)
1. Add another connection:
   - **Connection Id**: `postgres_default`
   - **Connection Type**: `Postgres`
   - **Host**: `postgres`
   - **Schema**: `postgres`
   - **Login**: `postgres`
   - **Password**: `postgres`
   - **Port**: `5432`
2. Click **Save**

#### 5. Enable and Trigger the DAG
1. Go to the **DAGs** page
2. Find `weather_etl_pipeline`
3. Toggle the switch to **ON** (unpause)
4. Click the **▶ Trigger** button to run manually

## 📊 Pipeline Details

### DAG Configuration
```python
DAG ID: weather_etl_pipeline
Schedule: @daily (runs every day at midnight)
Start Date: 2026-02-01
Catchup: False
```

### Task Breakdown

#### 1️⃣ **extract_weather_data**
- **Purpose**: Fetch current weather data from Open-Meteo API
- **Location**: London, UK (Latitude: 51.5074, Longitude: -0.1278)
- **API Endpoint**: `/v1/forecast?latitude={lat}&longitude={lon}&current_weather=true`
- **Output**: Raw JSON weather data

#### 2️⃣ **transform_weather_data**
- **Purpose**: Transform raw API response into structured format
- **Operations**:
  - Extract relevant fields
  - Structure data for database insertion
  - Data type conversion
- **Output**: Dictionary with cleaned data

#### 3️⃣ **load_weather_data**
- **Purpose**: Insert transformed data into PostgreSQL
- **Database**: `postgres`
- **Table**: `weather_data`
- **Operation**: Creates table if not exists, inserts new records

### Database Schema
```sql
CREATE TABLE weather_data (
    latitude FLOAT,
    longitude FLOAT,
    temperature FLOAT,
    windspeed FLOAT,
    winddirection FLOAT,
    weathercode INT,
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Task Dependencies
```
extract_weather_data >> transform_weather_data >> load_weather_data
```
- Tasks run **sequentially**
- If any task fails, downstream tasks are **skipped**
- Ensures data integrity throughout the pipeline

## 🔍 Monitoring & Troubleshooting

### View Logs
1. Click on any task in the Graph view
2. Select **"Log"** tab
3. View real-time execution logs

### Check Task Status
- 🟢 **Green**: Success
- 🔴 **Red**: Failed
- 🟡 **Yellow**: Running
- 🟠 **Orange**: Upstream failed

### Common Issues

| Issue | Solution |
|-------|----------|
| Docker not running | Start Docker Desktop |
| Connection errors | Verify connection settings in Admin → Connections |
| Import errors | Run `astro dev restart` to reload dependencies |
| Port 8080 in use | Stop other services using port 8080 |

## 📈 Viewing Results

### Option 1: Using DBeaver / pgAdmin
1. Connect to PostgreSQL:
   - Host: `localhost`
   - Port: `5432`
   - Database: `postgres`
   - User: `postgres`
   - Password: `postgres`

2. Run query:
```sql
SELECT * FROM weather_data ORDER BY timestamp DESC;
```

### Option 2: Using Airflow CLI
```bash
astro dev bash
psql -U postgres -d postgres -c "SELECT * FROM weather_data;"
```

## 🎓 Learning Outcomes
This project demonstrates:
- ✅ Building production-ready data pipelines
- ✅ Working with Apache Airflow operators and hooks
- ✅ API integration and error handling
- ✅ Data transformation and validation
- ✅ Database operations with PostgreSQL
- ✅ DAG scheduling and monitoring
- ✅ Docker containerization
- ✅ Version control with Git

## 🔄 Future Enhancements
- [ ] Add data quality checks
- [ ] Implement error notifications (Slack/Email)
- [ ] Add more data sources (multiple cities)
- [ ] Create data visualization dashboard
- [ ] Implement incremental loading
- [ ] Add unit tests for tasks
- [ ] Set up CI/CD pipeline

## 📚 Additional Resources
- [Apache Airflow Documentation](https://airflow.apache.org/docs/)
- [Astronomer Documentation](https://docs.astronomer.io/)
- [Open-Meteo API Docs](https://open-meteo.com/en/docs)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)

## 👨‍💻 Author
**Siddharth**
- GitHub: [@SIDDHARTH107](https://github.com/SIDDHARTH107)
- Course: IE 7374 - MLOps Lab 2
- Institution: Northeastern University

## 📝 License
This project is part of academic coursework and is available for educational purposes.

## 🙏 Acknowledgments
- Apache Airflow community
- Astronomer team for the CLI tool
- Open-Meteo for providing free weather API
- Northeastern University - IE 7374 Course Team

---

**⭐ If you found this project helpful, please give it a star!**
