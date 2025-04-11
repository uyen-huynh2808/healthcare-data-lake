# Healthcare Data Lake for Real-Time Analytics (IoT Patient Monitoring)

This project builds a real-time data lake and analytics platform to monitor patient health using IoT wearable devices. It enables hospitals and clinics to perform real-time anomaly detection, alerting, and long-term trend analysis.

## Project Goals

- Ingest real-time vitals from simulated IoT patient devices
- Stream and process data with Spark Structured Streaming
- Store data in Delta Lake using Bronze, Silver, and Gold architecture
- Load cleaned and aggregated data into BigQuery
- Visualize patient health metrics and anomalies with Looker/Tableau
- Integrate ML models to detect abnormal vitals in real-time

## Architecture

IoT Devices (Simulated) → Kafka (patient-vitals topic)
                             ↓
               Spark Structured Streaming (PySpark)
                             ↓
          Delta Lake (Bronze → Silver → Gold Layers)
                             ↓
              BigQuery (external/scheduled load)
                             ↓
           Looker/Tableau (Dashboards and Alerts)
                             ↓
   ML Model for Anomaly Detection (Isolation Forest / LSTM)

## Technology Stack

| Layer	| Tools/Technologies |
|-------|--------------------|
| Data Simulation	| Python (Faker, NumPy, JSON)
| Ingestion	| Apache Kafka
| Streaming	| Apache Spark (Structured Streaming)
| Storage	| Delta Lake (Parquet + Transaction Logs)
| Query Layer	| Google BigQuery
| Visualization	| Looker Studio / Tableau
| ML / Anomaly Detection	| PySpark MLlib, scikit-learn, MLflow
| Orchestration	| Apache Airflow (for scheduled loads)

## Data Used
This project uses simulated patient health data generated in real-time using Python scripts. Each simulated IoT device (representing a patient) emits health vitals every few seconds in JSON format, including:
- patient_id: unique identifier
- timestamp: ISO8601 format
- heart_rate: integer
- temperature: float
- spo2: float (oxygen level)
- respiration_rate: integer
- blood_pressure: systolic/diastolic (either as string or two fields)
The data is streamed to a Kafka topic (patient-vitals) and ingested using Spark Streaming.

## Data Model
**Delta Lake Tables**
`bronze_patient_vitals`
- Raw data ingested from Kafka
- Schema: unvalidated, JSON
`silver_patient_vitals`
- Cleaned and parsed records
- Derived fields and simple alert flags
`gold_patient_summary`
- Aggregated vitals per patient
- Includes real-time anomaly detection score
- Partitioned by date and patient_id

**BigQuery Tables**
- Mirrors gold_patient_summary
- Additional views:
  - `patient_risk_scores`
  - `ward_alert_counts`
  - `vital_signs_hourly_trend`
 
## ML Model
**Goal:** Detect abnormal health patterns using unsupervised learning
**Options:**
- Isolation Forest
- AutoEncoder
- LSTM (if time series needed)
**Pipeline:**
- Train model on historical data
- Serve model in Spark Streaming for real-time scoring
- Output score to Gold Delta table and dashboard

## Project Files
1. `data_simulator` - Python scripts to simulate patient IoT data
2. `kafka_producer.py` - Sends vitals JSON messages to Kafka
3. `spark_streaming_job.py` - Spark job to process and write to Delta Lake
4. `delta_lake_setup/` -	Scripts to define schema and layers (Bronze/Silver/Gold)
5. `ml_model_training.ipynb` -	Training anomaly detection model on simulated data
6. `ml_inference_stream.py` -	Applies trained model in streaming job to score anomalies
7. `bigquery_loader.py` -	Transfers Gold table to BigQuery
8. `dashboards/` -	Dashboard JSON templates 
9. `requirements.txt` -	Project dependencies

---
