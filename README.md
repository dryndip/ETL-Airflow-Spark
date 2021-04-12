# ETL Pipeline with Airflow, Spark, s3 and MongoDB
## Description
Educational project to buid an ETL (Extract, Transform, Load) data pipeline, orchestrated with Airflow.
An AWS s3 bucket is used as a Datalake in which json files are stored. The data is extracted from a json and parsed (cleaned). It is then transformed/processed with Spark (PySpark) and loaded/stored in a Mongodb database which has the role of the Data Warehouse.

#### Note: This project was built for learning purposes and as an example, as such, it functions for only a single scenario and data schema.

The project is built in Python and it has 2 main parts:
  1. The Airflow DAG file, [**dags/dagRun.py**](https://github.com/renatootescu/ETL-pipeline/blob/main/dags/dagRun.py), which orchestrates the data pipeline tasks.
  2. The PySpark data transformation/processing script, located in [**sparkFiles/sparkProcess.py**](https://github.com/renatootescu/ETL-pipeline/blob/main/sparkFiles/sparkProcess.py)

The code and especially the comments in the python files are intentionally verbose for a better understanding of the functionality. 

<p align="center"><img src=https://user-images.githubusercontent.com/19210522/114319351-cd8d2880-9b19-11eb-834c-2bdf933fb0ab.png></p>

## Scenario
The Romanian counties COVID-19 data, provided by https://datelazi.ro/ and loaded in the s3 bucket, contains the total covid numbers from one day to the next, but not the difference between the days (i.e. for county X in day 1 there were 7 cases, in day 2 there were 37 cases).
Find the differences between days for all counties (i.e. for county X there were 30 more cases from day 1 to day 2). If the difference is smaller than 0 (e.g. because of a data recording error), then that day has a difference of 0.

## Base concepts
 - [ETL](https://en.wikipedia.org/wiki/Extract,_transform,_load)
 - [Pipeline](https://en.wikipedia.org/wiki/Pipeline_(computing))
 - [Data Lake](https://en.wikipedia.org/wiki/Data_lake)
 - [Data Warehouse](https://en.wikipedia.org/wiki/Data_warehouse)
 - [Data Schema](https://en.wikipedia.org/wiki/Database_schema)
 - [Apache Airflow](https://airflow.apache.org/docs/apache-airflow/stable/index.html) ([wikipedia page](https://en.wikipedia.org/wiki/Apache_Airflow))
 - [DAG](https://airflow.apache.org/docs/apache-airflow/stable/concepts.html#dags)
 - [Apache Spark](https://spark.apache.org/), speciffically the [PySpark](https://spark.apache.org/docs/latest/api/python/getting_started/quickstart.html) api ([wikipedia page](https://en.wikipedia.org/wiki/Apache_Spark))
 - [Amazon Web Services (AWS)](https://aws.amazon.com/) ([wikipedia page](https://en.wikipedia.org/wiki/Amazon_Web_Services))
 - [s3](https://aws.amazon.com/s3/) ([wikipedia page](https://en.wikipedia.org/wiki/Amazon_S3))
 - [mongoDB](https://www.mongodb.com/) ([wikipedia page](https://en.wikipedia.org/wiki/MongoDB))

## Prerequisites
- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/)

## Settting everything up
Download / pull the repo to your desired location.

You will have to create an AWS s3 user specifficaly for Airflow to interact with the s3 bucket.
The credentials for that user will have to be saved in the file [s3](https://github.com/renatootescu/ETL-pipeline/blob/main/airflow-data/creds/s3) found the directory **/airflow-data/creds**:

    [airflow-spark1]
    aws_access_key_id = 
    aws_secret_access_key = 

You will also have to enter the mongoDB connection string in the [**dags/dagRun.py**](https://github.com/renatootescu/ETL-pipeline/blob/main/dags/dagRun.py) script, line 16:

    client = pymongo.MongoClient('mongoDB_connection_string')

In the repo directory, execute the following command that will create the .env file containig the Airflow UID and GID needed by docker-compose:

    echo -e "AIRFLOW_UID=$(id -u)\nAIRFLOW_GID=0" > .env

## Installation
Start the installation with:

    docker-compose up -d

This command will pull and create Docker images and containers for Airflow, according to the instructions in the docker-compose.yml file:

<p align="center"><img src=https://user-images.githubusercontent.com/19210522/114414670-b43ab980-9bb7-11eb-8ea8-061385b14980.gif></p>

After everything has been installed, you can check the status of your containers (if they are healthy) with:

    docker ps

**Note**: it might take 30 seconds for the containers to have the **healthy** flag after starting.

<p align="center"><img src=https://user-images.githubusercontent.com/19210522/114403953-ed6e2c00-9bad-11eb-9e6e-8f85d7fced2e.png></p>


## Usage

You can now access the Airflow web interface by going to http://localhost:8080/. If you have not changed them in teh docker-compose.yml file, the default user is **airflow** and password is **airflow**:

<p align="center"><img src=https://user-images.githubusercontent.com/19210522/114421290-d5060d80-9bbd-11eb-842e-13a244996200.png></p>

The first page you see is the DAGs list page. 
Any dag python script saved in the dags folder will show up on this page (e.g. the first DAG, analyze_json_data, is the one built for this project).
If you modify the dag code, this page has to be refreshed

<p align="center"><img src=https://user-images.githubusercontent.com/19210522/114454069-dbf34700-9be2-11eb-8040-f57407adf856.png></p>

Click on the name of the dag to open the DAG details page:

<p align="center"><img src=https://user-images.githubusercontent.com/19210522/114457291-8882f800-9be6-11eb-9090-1f45af9f92ea.png></p>

On the Graph View page you can see the dag running through its tasks after it has been unpaused and trigerred:

<p align="center"><img src=https://user-images.githubusercontent.com/19210522/114459521-50c97f80-9be9-11eb-907a-3627a21d52dc.gif></p>

A detailed breakdown on how the code works can be found in the python scripts [**dags/dagRun.py**](https://github.com/renatootescu/ETL-pipeline/blob/main/dags/dagRun.py) and [**sparkFiles/sparkProcess.py**](https://github.com/renatootescu/ETL-pipeline/blob/main/sparkFiles/sparkProcess.py)

## Learning resources:
 - [Marc Lambreti](https://marclamberti.com/)
 - [Medium](https://medium.com/@itunpredictable/apache-airflow-on-docker-for-complete-beginners-cf76cf7b2c9a)
 - [Towards Data Science](https://towardsdatascience.com/getting-started-with-airflow-using-docker-cd8b44dbff98)
 - [Spark by examples](https://sparkbyexamples.com/pyspark-tutorial/)
 - [DataScience Made Simple](https://www.datasciencemadesimple.com/pyspark-string-tutorial/)
 - [Precocity](https://precocityllc.com/blog/airflow-and-xcom-inter-task-communication-use-cases/)
 - [Databricks](https://databricks.com/blog/2015/07/15/introducing-window-functions-in-spark-sql.html)

## License
You can check out the full license [here](https://github.com/renatootescu/ETL-pipeline/blob/main/LICENSE)

This project is licensed under the terms of the **MIT** license.
