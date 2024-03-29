# Google Cloud Cheatsheet

## Table of Content
- [IAM & Authentication](#iam-&-authentication)
- [VPC Network & Firewalls](#vpc-network-&-firewalls)
- [Compute Engine](#compute-engine)
- [Cloud Dataproc](#cloud-dataproc)
- [Cloud SQL](#cloud-sql)
- [BigQuery](#bigquery)
- [AppEngine] (#appengine)
- [Kubernetes] (#kubernetes)
- [List of Repositories](#list-of-repositories)

### IAM & Authentication
```shell
# Enable OAuth for Default Application
gcloud auth application-default login
```

### VPC Network & Firewalls
```shell
# Create a new network
gcloud compute networks create <NETWORK_NAME> --subnet-mode=<custom OR auto>

# Create a new subnet
gcloud compute networks subnets create <SUBNET_NAME> --network=<NETWORK_NAME> --region=<GCP_REGION> --range=<IPADDR/SUBNET>

# List of created networks
gcloud compute networks list

# List of subnet sorted by network name
gcloud compute networks subnets list --sort-by=NETWORK

# Create a new Firewall rules
# This example is whitelisting the icmp, tcp port 22, and tcp port 3389
gcloud compute firewall-rules create <FIREWALL_NAME> --direction=<INGRESS / EGRESS> --priority=<NUMBER> --network=<NETWORK_NAME> --action=<ALLOW / DENY> --rules=icmp,tcp:22,tcp:3389 --source-ranges=<IPADDR/SUBNET>

# List of firewall rules
gcloud compute firewall-rules list --sort-by=NETWORK
```

### Compute Engine
```shell
# Create VM Instance (a.k.a Compute Engine)
gcloud compute instances create <VM_NAME> --zone=<ZONE_NAME> --machine-type=<MACHINE_TYPE> --subnet=<SUBNET_NAME> --image-family=debian-10 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=<NAME_OF_BOOT_DISK_FOR_VM>
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

### AppEngine
```shell
# Create the AppEngine (IRREVERSIBLE PER PROJECT !)
gcloud app create --project=<PROJECT_NAME>

# Prepare the apps ...
# Prepare environment for the code ... (python virutalenv install requirement)
# This code not provided here

# Need to prepare the .yaml file (app.yaml file)
# with the runtime
# e.g. `runtime: python39` or `runtime: nodejs14`

# Deployeing the apps
gcloud app deploy

# Open the link of the deployed apps
gcloud app browse
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

### Kubernetes
```shell
# Fast create clusters
# insert zone here (e.g. us-central1-a)
export MY_ZONE=<ZONE_NAME>
gcloud container clusters create webfrontend --zone $MY_ZONE --num-nodes 2

# See the cluster GKE version
kubectl version

# Deploying a container
kubectl create deploy <DEPLOYMENT_NAME> --image=<IMAGE_NAME>:<VERSION>

# Viewing Kubernetes Resources (e.g. pods, deployment)
kubectl get <RESOURCE_NAME>

# Exposing deployment to the internet
# LoadBalancer is the type for exposing deployment to the internet
# Will be expose as a service
kubectl expose deployment <DEPLOYMENT_NAME> --port <PORT_NUMBER> --type LoadBalancer

# Scaling the deployment
kubectl scale deployment <DEPLOYMENT_NAME> --replicas <REPLICAS_NUMBER>

```

### List of Repositories
#### Data Science
- https://github.com/GoogleCloudPlatform/data-science-on-gcp/
