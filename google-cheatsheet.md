# Google Cloud Cheatsheet

## Table of Content
- [IAM & Authentication](#iam-&-authentication)
- [Cloud Dataproc](#cloud-dataproc)
- [Cloud SQL](#cloud-sql)
- [BigQuery](#bigquery)
- [List of Repositories](#list-of-repositories)

### IAM & Authentication
```shell
# Enable OAuth for Default Application
gcloud auth application-default login
```

### Cloud Dataproc
```shell
# Create Bucket First (needed)
export PROJECT_ID=$(gcloud info --format='value(config.project)')
export BUCKET=$PROJECT_ID-${RANDOM}

gsutil mb gs://${BUCKET}
# Define cluster
export MYCLUSTER="${PROJECT_ID}-cluster"

gcloud config set dataproc/region <REGION>

# Create Dataproc with initialization action
gcloud dataproc clusters create ${MYCLUSTER} --bucket=${BUCKET} --worker-machine-type=n1-standard-2 --master-machine-type=n1-standard-2 --initialization-actions=gs://spls/gsp010/install-libgtk.sh --image-version=2.0  
```

### Cloud SQL
```shell
# Fast create bucket (Google Cloud Storage)
export PROJECT_ID=$(gcloud info --format='value(config.project)')
gsutil mb gs://${PROJECT_ID}-ml

# Create Cloud SQL Instance
gcloud sql instances create <INSTANCE_NAME> \
    --tier=db-n1-standard-1 --activation-policy=ALWAYS \
    --region=us-central1

# Set password for Cloud SQL Instance
gcloud sql users set-password root --host % --instance <INSTANCE_NAME> \
 --password Passw0rd

# Whitelisting Address to Cloud SQL Instance
export ADDRESS=$(wget -qO - http://ipecho.net/plain)/32
gcloud sql instances patch <INSTANCE_NAME> --authorized-networks $ADDRESS

# Get Cloud SQL Instances IP
MYSQLIP=$(gcloud sql instances describe \
<INSTANCE_NAME> --format="value(ipAddresses.ipAddress)")

# Run SQL Command to Cloud SQL
mysql --host=$MYSQLIP --user=root \
      --password --verbose < create_table.
      
# Connect to MySQL Server using MySQL Client
mysql --host=$MYSQLIP --user=root  --password

# Import csv to database
mysqlimport --local --host=$MYSQLIP --user=root --password \
--ignore-lines=1 --fields-terminated-by=',' <DATABASE_NAME> <CSV_NAME>.csv-*
```

### BigQuery
```shell
# Fast create BigQuery dataset
export PROJECT_ID=$(gcloud info --format='value(config.project)')
bq mk --project_id $PROJECT_ID <DATASET_NAME>

# Examine a table
bq show <PROJECT_ID>:<DATASET_NAME>.<TABLE_NAME>

# Run a Query (Legacy SQL)
bq query --use_legacy_sql=false \
'<INSERT_QUERY_HERE>'

# Create Table from TXT
bq load <DATASET_NAME>.<TABLE_NAME> <SOURCE_TXT> \
<COLUMN1>:<TYPE1>,<COLUMN2>:<TYPE2>,...,<COLUMNN>:<TYPEN>

# Delete Dataset
bq rm -r <DATASET_NAME>
```

### List of Repositories
#### Data Science
- https://github.com/GoogleCloudPlatform/data-science-on-gcp/
