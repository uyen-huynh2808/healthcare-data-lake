# (Uncompleted) Healthcare Data Lake for Real-Time Analytics (IoT Patient Monitoring)

This project builds a **real-time healthcare data lake and analytics platform** for monitoring patient vitals from simulated **IoT wearable devices**. Vital signs like heart rate, temperature, and SpO₂ are streamed via **Apache Kafka**, processed in real time using **Spark Structured Streaming**, and stored in **Delta Lake** using a Bronze–Silver–Gold architecture. Cleaned and aggregated data is loaded into **BigQuery** for analysis and visualized using **Looker**. An **anomaly detection ML model** is integrated to identify abnormal vitals in real time, simulating how healthcare providers can respond to emergencies and monitor patient health at scale.

## Project Goals

- **Ingest real-time vitals from simulated IoT patient devices**  
  Simulate wearable health devices (e.g., smartwatches, biometric sensors) that generate live vital signs like heart rate, temperature, SpO₂, respiration rate, and blood pressure. Stream this data into the system via Apache Kafka to replicate a real hospital environment.

- **Stream and process data with Spark Structured Streaming**  
  Use Apache Spark to process incoming Kafka streams in real time. Apply schema enforcement, data cleansing, timestamp alignment, and enrich the data for downstream analysis.

- **Store data in Delta Lake using Bronze, Silver, and Gold architecture**  
  Build a multi-layered Delta Lake architecture:  
  - **Bronze**: Raw Kafka ingestion  
  - **Silver**: Cleaned and structured data with derived fields  
  - **Gold**: Aggregated patient metrics and anomaly flags ready for reporting

- **Load cleaned and aggregated data into BigQuery**  
  Use scheduled or streaming data loads to push curated data from the Gold layer to Google BigQuery for scalable and serverless analytics.

- **Visualize patient health metrics and anomalies with Looker**  
  Develop interactive dashboards to monitor individual patient vitals, detect high-risk cases, and analyze hospital-wide trends using visualization tools like Looker Studio or Tableau.

- **Integrate ML models to detect abnormal vitals in real-time**  
  Train and deploy anomaly detection models (e.g., Isolation Forest, LSTM) to identify outliers in vital sign patterns. Embed these models in the streaming pipeline to score events and generate real-time alerts for clinicians.

## Architecture

IoT Devices (Simulated)  
        ↓  
Kafka (patient-vitals topic)  
        ↓  
Spark Structured Streaming (PySpark)  
        ↓  
Delta Lake (Bronze → Silver → Gold Layers)  
       ↓            ↓
  ML Training     BigQuery Load
       ↓            ↓
ML Inference    Looker/Tableau Dashboards

## Technology Stack

| Layer                   | Tools/Technologies                                         |
|------------------------|------------------------------------------------------------|
| Data Simulation         | Python (Faker, NumPy, JSON)                                |
| Ingestion               | Apache Kafka                                               |
| Streaming               | Apache Spark (Structured Streaming)                        |
| Storage                 | Delta Lake (Parquet + Transaction Logs)                    |
| ML / Anomaly Detection  | PySpark MLlib, scikit-learn, MLflow (inference + training) |
| Orchestration           | Apache Airflow (scheduled loads, ML retraining)            |
| Query Layer             | Google BigQuery                                            |
| Visualization           | Looker Studio                                              |

## Data Used
This project uses simulated patient health data generated in real-time using Python scripts. Each simulated IoT device (representing a patient) emits health vitals every few seconds in JSON format, including:
- `patient_id`: unique identifier
- `timestamp`: ISO8601 format
- `heart_rate`: integer
- `temperature`: float
- `spo2`: float (oxygen level)
- `respiration_rate`: integer
- `blood_pressure`: systolic/diastolic (either as string or two fields)
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

**Goal:**  
Detect abnormal patient vitals using unsupervised learning techniques.

**Algorithms Explored:**  
- **Isolation Forest** – Lightweight and effective for high-dimensional anomaly detection  
- **AutoEncoder** – Neural network-based anomaly detection  
- **LSTM** – For detecting anomalies in time-series sequences (optional if time-series is prioritized)

**Workflow:**  
1. **Train** models on historical data stored in Delta Lake (Silver/Gold layer)  
2. **Register** model using MLflow (optional)  
3. **Deploy** model in Spark Streaming job for real-time inference  
4. **Output** anomaly scores into the Gold Delta table  
5. **Visualize** anomalies and trends through Looker Studio dashboards

## Project Files
1. `data_simulator/` – Python scripts to simulate patient IoT data  
2. `kafka_producer.py` – Sends vitals JSON messages to Kafka  
3. `delta_lake_setup/` – Scripts to define schema and Delta Lake layers (Bronze/Silver/Gold)  
4. `spark_streaming_job.py` – Spark job to process and write streaming data to Delta Lake  
5. `ml_model_training.ipynb` – Train anomaly detection model on simulated data  
6. `ml_inference_stream.py` – Applies trained model to streaming pipeline for real-time scoring  
7. `bigquery_loader.py` – Transfers Gold-layer data to BigQuery  
8. `dags/` – Airflow DAGs for scheduled BigQuery loads and ML retraining  
9. `dashboards/` – Dashboard templates (Looker Studio)

---
