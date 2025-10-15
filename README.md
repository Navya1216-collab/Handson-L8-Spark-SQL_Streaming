Real-Time Ride-Sharing Analytics with Spark Structured Streaming

This project builds a real-time data processing pipeline using Apache Spark Structured Streaming. It simulates live ride-sharing trip data via a Python generator streaming JSON messages over a socket connection (localhost:9999).

Setup Requirements

Ensure you have the following dependencies installed before execution:

Python 3.x
```
python3 --version
```

PySpark
```
pip install pyspark
```

Faker (used in the data generator)
```
pip install faker
```

Java 8+

java -version

Folder Organization

Your working directory will look like this once the scripts run:

L8_Spark_SQL_Streaming/
│
├── data_generator.py
├── task1.py
├── task2.py
├── task3.py
│
├── outputs/
│   ├── task1/
│   │   ├── row_1_xxx/
│   │   │   └── part-00000-....csv
│   │   ├── row_2_xxx/
│   │   │   └── part-00000-....csv
│   │   └── ...
│   │
│   ├── task2/
│   │   ├── batch_0/part-00000-....csv
│   │   ├── batch_1/part-00000-....csv
│   │   └── ...
│   │
│   └── task3/
│       ├── batch_0/part-00000-....csv
│       ├── batch_1/part-00000-....csv
│       └── ...
│
└── README.md


Note: The checkpoints/ directory is created automatically by Spark and should not be added to GitHub.

How to Execute
Step 1 — Start Data Stream

In Terminal 1, activate your virtual environment and run:

python data_generator.py


This script sends one JSON record per second to the socket stream.

Step 2 — Run the Spark Tasks

In Terminal 2, execute each task individually (keep generator running):

# Task 1 — Parse and write raw streaming data
```
python task1.py
```
# Task 2 — Aggregate driver statistics
```
python task2.py
```
# Task 3 — Compute window-based fare sums
```
python task3.py
```
Project Overview

The purpose of this project is to practice Spark Structured Streaming concepts by implementing three sequential data-processing tasks on streaming ride data.

Task	Objective	Output Type
Task 1	Stream ingestion & JSON parsing	Row-wise CSVs
Task 2	Aggregations by driver (sum & average)	Batch CSVs
Task 3	Time-windowed analytics (5-min rolling windows)	Window CSVs
Task 1 — Ingesting & Parsing Streaming Data

Goal:
Consume streaming JSON data and convert it into structured columns:
trip_id, driver_id, distance_km, fare_amount, timestamp.

Approach:

Input via socket stream (localhost:9999).

Parse each message using a predefined schema.

Write each micro-batch as a separate CSV file (outputs/task1/row_*).

Example Output:

distance_km,driver_id,fare_amount,timestamp,trip_id
37.31,74,82.68,2025-10-14 23:22:23,dc98071c-fe04-4cff-a789-592c823cf45f

Task 2 — Aggregating by Driver

Goal:
Calculate each driver’s total fare and average trip distance in real-time.

Steps:

Convert timestamps to Spark’s TimestampType.

Group by driver_id and compute:

SUM(fare_amount) → total_fare

AVG(distance_km) → avg_distance

Write each batch’s output to outputs/task2/batch_*.

Sample Output:

driver_id,total_fare,avg_distance
65,118.57,19.67
27,99.24,40.29
91,93.18,13.12

Task 3 — Time-Based Windowed Aggregations

Goal:
Compute rolling sums of total fare collected within a 5-minute window, sliding by 1 minute with a 1-minute watermark.

Process:

Convert timestamp to event_time.

Define windowing logic with groupBy(window(event_time, "5 minutes", "1 minute")).

Aggregate SUM(fare_amount) as sum_fare.

Write each micro-batch to outputs/task3/batch_*.

Sample Output:

window_start,window_end,sum_fare
2025-10-15T22:42:00.000Z,2025-10-15T22:47:00.000Z,19521.0

Verify Outputs

View all generated CSVs:

ls -R outputs


Preview top lines:

head outputs/task1/*/part-*.csv
head outputs/task2/batch_*/part-*.csv
head outputs/task3/batch_*/part-*.csv

🧹 Cleanup (Optional)

If you need to restart or re-run tasks:

rm -rf checkpoints/task1 checkpoints/task2 checkpoints/task3
rm -rf outputs/task1 outputs/task2 outputs/task3


✅ Result:
All three tasks together demonstrate a complete Spark Streaming workflow — from real-time ingestion to stateful aggregations and time-windowed trend analytics.
