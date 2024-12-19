## About

Educational project on how to build an ETL (Extract, Transform, Load) data pipeline, orchestrated with Airflow.

An AWS s3 bucket is used as a Data Lake in which json files are stored. The data is extracted from a json and parsed (cleaned). It is then transformed/processed with Spark (PySpark) and loaded/stored in either a Mongodb database or in an Amazon Redshift Data Warehouse.
