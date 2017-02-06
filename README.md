# cdh_historian
## Pre-Requisites
1. CDH 5.10, including the following services:
 * Impala
 * Kafka
 * KUDU
 * Spark 2.0
 * Solr (Optional)
 * HUE
2. StreamSets Data Collector 2.2.1.0
3. Anaconda Parcel for Cloudera

## Setup
1. Run the setup script to install Python libraries
```bash setup/install.sh```
2. Edit Streamsets Configuration in Cloudera Manager:
 * Data Collector Advanced Configuration Snippet (Safety Valve) for sdc-env.sh:
```export STREAMSETS_LIBRARIES_EXTRA_DIR="/opt/sdclib/"```
 * Data Collector Advanced Configuration Snippet (Safety Valve) for sdc-security.policy:
```grant codebase "file:///opt/sdclib/-" { permission java.security.AllPermission; };```
3. Restart Streamsets

## Initialize
1. Edit config.ini with the desired data generator (# wells, sensors, history etc.) and hadoop settings (kafka and kudu servers)
2. Create tables to store sensor data in Kudu and generate static lookup information:
```python datagen/historian.py config.ini static```
3. Generate historic data and store in Kudu
```python datagen/historian.py config.ini historic```
4. Open Kudu web UI and navigate to the tables that were created, extract Impala DDL statements and run them in HUE
5. Open Streamsets, import pipeline by uploading pipeline config file: streamsets/historian_ingest.json
6. Open Streamsets pipeline, edit constants to indicate kafka broker servers and kudu master server

## Run
1. Start Streamsets pipeline
2. Start sensor data generator:
```python datagen/historian.py config.ini realtime```
