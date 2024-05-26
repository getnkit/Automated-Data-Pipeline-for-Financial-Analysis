# End-to-End-Data-Pipeline-for-Consumer-Financial-Complaints
## Project Overview
This project focuses on building and automating a data pipeline with Apache Airflow. Before ingesting data into the automated data pipeline, it undergoes data profiling (review and cleansing) and is then uploaded to Cloud SQL (MySQL) using a Python. Subsequently, the automated data pipeline extracts data from MySQL, transforms the data, and loads it into Google Cloud Storage (GCS) to ingest the data into BigQuery. Finally, a Consumer Financial Complaints Dashboard is developed using Looker Studio.
## About Dataset
This dataset consists of real-world complaints about financial products and services, including details such as product type, issue description, company response, and other metadata. These complaints are published after the company responds, or after 15 days from the date of receipt, whichever comes first. By voicing their opinions and complaints, consumers help improve the quality and efficiency of the financial marketplace.
## Architecture
![image](https://github.com/getnkit/End-to-End-Data-Pipeline-for-Consumer-Financial-Complaints/blob/41e0d2c06f3f04a0bed133cfa111eaab6dbd6a02/images/Data%20Architecture.png)
## Implementation
### Step 1: Data profiling using Python on Google Colab
### Step 2: Set up a Virtual Environment in Python
**Using virtual environments (venv) in Python helps isolate project dependencies and prevent conflicts between projects or system packages.**

Creates a Python Virtual Environment named "ENV", isolating project dependencies from the global Python environment.
```bash
python -m venv ENV
```
Activates the Python Virtual Environment named "ENV", allowing you to work within that isolated environment.
```bash
ENV\Scripts\activate
```
Installs the specified Python package in the current Python environment.
```bash
pip install <python_package_name>
```
Installs all the Python packages listed in the "requirements.txt" file.
```bash
pip install -r requirements.txt
```
### Step 3: Import profiled data into Cloud SQL(MySQL) using Python
#### About Python Source Code
- **Imports necessary modules:** Uses configparser for reading configuration files and pandas for data manipulation.
- **Specifies the configuration file path:** Defines the path to the configuration file containing database connection details.
- **Parses the configuration file:** Reads the configuration file to extract database connection parameters.
- **Retrieves database connection details:** Extracts the database name, username, password, host, and port from the configuration file.
- **Constructs the database URI:** Builds the connection URI for the MySQL database using the retrieved details.
- **Reads data from CSV files:** Loads data into Pandas DataFrames.
- **Imports data into the MySQL database:** Writes the DataFrames to the corresponding tables in the MySQL database, replacing the existing data if the tables already exist.

### Step 4: Create a bucket on Google Cloud Storage (GCS) and a dataset on BigQuery for Data pipeline
### Step 5: Install and Configure Airflow Using Docker Container
Fetching docker-compose.yaml to deploy Airflow on Docker Compose
```
curl -LfO 'https://airflow.apache.org/docs/apache-airflow/2.9.1/docker-compose.yaml'
```
Setting the right Airflow user
```
mkdir -p ./dags ./logs ./plugins ./config
echo "AIRFLOW_UID=50000" > .env
```
Create a Dockerfile to describe how to create, then build a Docker image
```
docker build -t custom-airflow-image:2.9.1 .
```
Configure docker-compose yaml, then run the docker-compose command to run Docker containers based on the settings described in a docker-compose.yaml file

```
docker compose up airflow-init
docker compose up
```
Access to Airflow UI
```
http://localhost:8080/
```
### Step 6: Set up Connection & Variables in Airflow
### Step 7: Create Python DAG in Airflow
**Step 7.1: Importing Modules**
- Import necessary modules and libraries for the DAG.

**Step 7.2: Extract Data from MySQL**
- Connects to a MySQL database using MySqlHook.
- Fetches data from two tables and loads them into Pandas DataFrames.
- Saves these DataFrames as CSV files in the specified directory.

**Step 7.3: Transform Data**
- Reads the previously saved CSV files into Pandas DataFrames.
- Converts necessary columns to string type to ensure proper merging.
- Merges the two DataFrames.
- Drops redundant columns and rearranges the columns.
- Saves the transformed DataFrame as a CSV file in the specified directory.

**Step 7.4: Load Data to Google Cloud Storage (GCS)**
- Prepares Google Cloud Storage credentials from Airflow Variables.
- Initializes a Google Cloud Storage client using the credentials.
- Specifies the bucket name and file path for uploading the transformed CSV file.
- Uploads the file to the specified GCS bucket and path.

**Step 7.5: Load Data from GCS to BigQuery**
- Prepares Google BigQuery credentials from Airflow Variables.
- Initializes a BigQuery client using the credentials.
- Specifies the BigQuery table ID and job configuration for loading the CSV file from GCS.
- Loads the CSV file from GCS to the specified BigQuery table, using the configured job settings (e.g., skipping the first row, auto-detecting schema, etc.).

**Step 7.6: Airflow DAG Definition**
- Defines the DAG name.
- Sets the start date to one day ago and schedules the DAG to run once (@once).
- Tags the DAG with "financial", "mysql", and "bigquery".
- Create task specifies an individual step in a workflow.
- Set up dependencies or the order in which tasks should be executed.

### Step 8: Copy a DAG file to Airflow container
```
docker cp <source_path> <container_id>:/opt/airflow/dags/
```
### Step 9: Run a DAG file in the Airflow UI
![image](https://github.com/getnkit/Automated-ETL-Pipeline-for-Consumer-Financial-Complaints-Analysis/blob/353f894f5ee36914d745e6f241f7edcf0df9276e/images/Data%20Pipeline%20with%20Airflow.png)
### Step 10: Create Consumer Financial Complaints Dashboard using Looker Studio
![image](https://github.com/getnkit/Automated-ETL-Pipeline-for-Consumer-Financial-Complaints-Analysis/blob/761e209eb5ff580afadef3da504393fcd835949a/images/Consumer%20Financial%20Complaints%20Dashboard.jpg)
#### Insight Gained
- 100,000 is the total number of complaints received.
- 97.4% of responses by the bank were delivered on time.
- Around 19,800 complaints were related to Customer Dispute Cases, which is approximately 20% of the total.
- The top issue for consumers was 'Incorrect information on credit report', followed closely by 'Loan modification, collection, foreclosure' at a similar rate.
- The products/services with the highest number of complaints were 'Mortgage', 'Debt collection', and 'Credit reporting'.
- Around 70% of the complaints were submitted via 'the website'.
- The majority of company responses to consumers had a status of 'Closed with explanation'.
- For complaints arising from different states, a 'Bubble map' was created, where larger bubbles represent states with a higher number of complaints, and smaller bubbles represent states with fewer complaints. The bubble sizes range from larger to smaller, corresponding to the number of complaints.


