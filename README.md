# Learn the hard Way - Google Cloud Platform

Follow Coursera Specializations:  
- [Architecting with Google Compute Engine Specialization](https://www.coursera.org/specializations/gcp-architecture)  
- [Networking in Google Cloud Specialization](https://www.coursera.org/specializations/networking-google-cloud-platform)  
- [Security in Google Cloud Specialization](https://www.coursera.org/specializations/security-google-cloud-platform)  
- [Architecting with Google Kubernetes Engine Specialization](https://www.coursera.org/specializations/architecting-google-kubernetes-engine)  
- [Architecting Hybrid Cloud Infrastructure with Anthos Specialization](https://www.coursera.org/specializations/architecting-hybrid-cloud-infrastructure-anthos)  
- [Developing Applications with Google Cloud Specialization](https://www.coursera.org/specializations/developing-apps-gcp)  


=======
## Common environment variable
### Project ID
```bash
export PROJECT_ID=$(gcloud config get-value project)
```

### Compute Engine default service account

```bash
export SA_EMAIL=$(gcloud iam service-accounts list --filter="displayName:Compute Engine default service account" --format='value(email)')
```

## IAM

User-managed service accounts:  
```
# Default Compute Engine service account
PROJECT_NUMBER-compute@developer.gserviceaccount.com
# Default App Engine service account
PROJECT_ID@appspot.gserviceaccount.com
# Google APIs service account
PROJECT_NUMBER@cloudservices.gserviceaccount.com
```

Create a service account:  
```bash
gcloud iam service-accounts create my-sa-123 --display-name "my service account"

gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID \
    --member serviceAccount:my-sa-123@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com --role roles/editor
```

```bash
gcloud iam service-accounts create bigquery-qwiklab --display-name "bigquery-qwiklab service account"

gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID \
    --member serviceAccount:bigquery-qwiklab@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com --role roles/bigquery.dataViewer

gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID \
    --member serviceAccount:bigquery-qwiklab@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com --role roles/bigquery.user
```

```bash
gcloud iam service-accounts create cseklab --display-name "customer-supplied encryption key demo"

gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID \
    --member serviceAccount:cseklab@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com --role roles/storage.admin
```

```bash
gcloud projects add-iam-policy-binding ${GOOGLE_CLOUD_PROJECT} \
--member=serviceAccount:team-a-dev@${GOOGLE_CLOUD_PROJECT}.iam.gserviceaccount.com  \
--role=roles/container.clusterViewer
```

Remove role:  
```bash
gcloud projects remove-iam-policy-binding $GOOGLE_CLOUD_PROJECT \
--member='user:student-03-7828661fb9a2@qwiklabs.net' \
--role='roles/viewer'
```


List all roles:  
```bash
gcloud iam roles list --project $DEVSHELL_PROJECT_ID
gcloud iam roles list --project $DEVSHELL_PROJECT_ID \
--show-deleted
```

Create role:  
```bash
gcloud iam roles create privacy_reviewer --project \
$DEVSHELL_PROJECT_ID --file privacyreviewer.yaml

gcloud iam roles create app_viewer --project \
$DEVSHELL_PROJECT_ID --file role.yaml

```

```bash
gcloud iam roles describe app_viewer --project \
$DEVSHELL_PROJECT_ID

```

Update role:  
```bash
gcloud iam roles update app_viewer --project \
$DEVSHELL_PROJECT_ID --file update-role.yaml

```

Disable role:  
```bash
gcloud iam roles update app_viewer --project \
$DEVSHELL_PROJECT_ID --stage DISABLED

```

Delete and Undelte role:  
```bash
gcloud iam roles delete app_viewer --project \
$DEVSHELL_PROJECT_ID

gcloud iam roles undelete app_viewer --project \
$DEVSHELL_PROJECT_ID

```


## API Services

```bash
gcloud services list --available
gcloud services list --available | grep dataproc
gcloud services list --enabled
```

```bash
gcloud services enable deploymentmanager.googleapis.com

gcloud services enable sqladmin.googleapis.com

gcloud services enable appengine.googleapis.com

gcloud services enable cloudscheduler.googleapis.com

gcloud services enable dataproc.googleapis.com

gcloud services enable cloudkms.googleapis.com

gcloud services enable iap.googleapis.com

gcloud services enable secretmanager.googleapis.com

```

## Computer Engine

### Create VM
```bash
gcloud compute instances create vm-premium --machine-type=n1-standard-1 --subnet=default --zone=us-central1-a

gcloud compute instances create vm-standard --machine-type=n1-standard-1 --subnet=default --zone=us-central1-c \
--network-tier STANDARD

```

Create an install, install nginx-light  
```bash
gcloud compute instances create nginx-vm \
--zone us-central1-a \
--machine-type f1-micro \
--metadata startup-script=sudo\ apt\ update$'\n'sudo\ apt\ install\ -y\ nginx-light$'\n'sudo\ systemctl\ enable\ nginx$'\n'sudo\ service\ nginx\ start
```

Create an instance, instal apache2  
```bash
gcloud compute instances create web-server \
--subnet vpc-subnet \
--zone us-central1-a \
--machine-type f1-micro \
--tags http-server \
--metadata=startup-script='#! /bin/bash
  apt update
  apt -y install apache2
  cat <<EOF > /var/www/html/index.html
  <!doctype html><html><body><h1>Hello World!</h1></body></html>'

```

Create a new instance and authorize it to run as a custom service account:  
```bash
gcloud compute instances create example-vm \
    --service-account 123-my-sa@my-project-123.iam.gserviceaccount.com \
    --scopes https://www.googleapis.com/auth/cloud-platform
```

### Create VM with multiple network interfaces
```bash
gcloud compute instances create vm-appliance --zone=us-central1-c --machine-type=n1-standard-4 \
    --network-interface subnet=privatesubnet-us \
    --network-interface subnet=managementsubnet-us \
    --network-interface subnet=mynetwork
```

### Get internal and external IP Address

```bash
gcloud compute instances describe web-server \
  --zone us-central1-a \
  --format='get(networkInterfaces[0].networkIP)'

gcloud compute instances describe web-server \
  --zone us-central1-a \
  --format='get(networkInterfaces[0].accessConfigs[0].natIP)'
```

Delete a VM instance  
```bash
gcloud compute instances delete web-server \
  --zone us-central1-a
```

SSH to VM  
```bash
gcloud compute ssh web-server \
  --zone us-central1-a
```


```bash
export DISK_NAME=encrypted-disk-1
export DISK_SIZE=500
export DISK_TYPE=pd-standard
export ZONE=us-central1-a

gcloud beta compute disks create $DISK_NAME \
  --zone $ZONE \
  --size $DISK_SIZE \
  --type $DISK_TYPE \
  --csek-key-file rsawrapencodedkey.json
```


```bash
export REGION=us-central1
export INSTANCE_TEMPLATE_NAME="us-central1-template"
export SUBNET_NAME="default"
export STARTUP_SCRIPT_URL="gs://cloud-training/gcpnet/httplb/startup.sh"

gcloud compute instance-templates create $INSTANCE_TEMPLATE_NAME \
--description "us-central1-template" \
--region $REGION \
--subnet $SUBNET_NAME \
--tags allow-health-checks \
--metadata startup-script-url=$STARTUP_SCRIPT_URL

export INSTANCE_GROUP_NAME=us-central1-mig
export REGION=us-central1
export ZONE=us-central1-a
export INSTANCE_TEMPLATE_NAME="us-central1-template"

gcloud compute instance-groups managed create $INSTANCE_GROUP_NAME \
--region $REGION \
--zone $ZONE \
--template $INSTANCE_TEMPLATE_NAME \
--size 1

gcloud compute instance-groups managed set-autoscaling $INSTANCE_GROUP_NAME \
--scale-based-on-cpu \
--target-cpu-utilization 0.8 \
--min-num-replicas 1 \
--max-num-replicas 5 \
--cool-down-period 45 \
--region $REGION


```



## Kubernetes Engine (GKE)

```bash
export my_zone=us-central1-a
export my_cluster=standard-cluster-1

gcloud container clusters create $my_cluster --num-nodes 3 --zone $my_zone --enable-ip-alias

gcloud container clusters resize $my_cluster --zone $my_zone --num-nodes=4

```

```bash
# Configure tab completion for the kubectl command-line tool.
source <(kubectl completion bash)
# Configure access to your cluster for kubectl:
gcloud container clusters get-credentials $my_cluster --zone $my_zone

kubectl create deployment hello-app \
  --image=gcr.io/google-samples/hello-app:2.0

kubectl expose deployment hello-app --name=hello-app-service --type=LoadBalancer --port 80 --target-port 8080

kubectl get namespace
kubectl create namespace team-a
kubectl get nodes
kubectl get pods
kubectl get services
kubectl get services --namespace=kube-system

```

```bash
kubectl create role pod-reader \
--resource=pods --verb=watch --verb=get --verb=list
```

Resource Quotas:  
```bash
kubectl create quota test-quota \
--hard=count/pods=2,count/services.loadbalancers=1 --namespace=team-a
```


## VPC Network

Create auto-mode network  
```bash
gcloud compute networks create mynetwork --subnet-mode=auto
```

### Create two custom VPC networks managementnet and privatenet
```bash
gcloud compute networks create managementnet --subnet-mode=custom
gcloud compute networks subnets create managementsubnet-us --network=managementnet --region=us-central1 --range=10.130.0.0/20

gcloud compute networks create privatenet --subnet-mode=custom
gcloud compute networks subnets create privatesubnet-us --network=privatenet --region=us-central1 --range=172.16.0.0/24
gcloud compute networks subnets create privatesubnet-eu --network=privatenet --region=europe-west1 --range=172.20.0.0/20

gcloud compute networks create vpc-net --subnet-mode=custom
gcloud compute networks subnets create vpc-subnet --network=vpc-net \
--region=us-central1 --range=10.1.3.0/24 \
--enable-flow-logs

```

Enable VPC Flow logs  
```
gcloud compute networks subnets update default \
--region us-central1 --enable-flow-logs \
--logging-metadata=include-all

gcloud compute networks subnets update default \
--region europe-west1 --no-enable-flow-logs
```

Delete a VPC network  
```bash
gcloud compute networks delete default
gcloud compute networks delete default --quiet
```

### VPC Network Peering

```bash
export project_a=$(gcloud config get-value project)
export project_b=$(gcloud config get-value project)

gcloud compute networks peerings create peering-1-2 \
    --network mynetwork \
    --peer-project $project_b \
    --peer-network privatenet \
    --import-custom-routes \
    --export-custom-routes

gcloud compute networks peerings create peering-2-1 \
     --network privatenet \
     --peer-project $project_a \
     --peer-network mynetwork \
     --import-custom-routes \
     --export-custom-routes
```

### Cloud NAT

```bash
export NETWORK_NAME="my-internal-app"
export REGION=us-central1
export NAT_ROUTER=nat-router-us-central1
export NAT_CONFIG=nat-config

gcloud compute routers create $NAT_ROUTER \
    --network $NETWORK_NAME \
    --region $REGION

gcloud compute routers nats create $NAT_CONFIG \
    --router=$NAT_ROUTER \
    --auto-allocate-nat-external-ips \
    --nat-all-subnet-ip-ranges \
    --enable-logging \
    --region=$REGION

```

### Cloud Router

Create a cloud router in the vpc-demo network:  
```bash
gcloud compute routers create vpc-demo-router1 \
    --region us-central1 \
    --network vpc-demo \
    --asn 65001
```

## Firewall rules

### List firewall rules

```bash
gcloud compute firewall-rules list

gcloud compute firewall-rules list \
--filter="network:mynetwork"

```

```bash
gcloud compute firewall-rules create "default-allow-http" \
      --allow tcp:80 \
      --source-ranges="0.0.0.0/0" \
      --description="Allow Web Server traffic" \
      --target-tags="http-server"
```

```bash
gcloud compute firewall-rules create "default-allow-health-check" \
      --allow tcp \
      --source-ranges="130.211.0.0/22,35.191.0.0/16" \
      --description="Allow health check traffic" \
      --target-tags="http-server"
```

```bash
export NETWORK_NAME="default"
export FWRULE_NAME="fw-allow-health-checks"

gcloud compute firewall-rules create $FWRULE_NAME \
--network $NETWORK_NAME \
--action allow \
--target-tags allow-health-checks \
--source-ranges 130.211.0.0/22,35.191.0.0/16 \
--rules tcp:80
```

### Create firewall rule to allow ALL internal traffic within the network 'on-prem'
```bash
gcloud compute firewall-rules create on-prem-allow-internal \
  --network on-prem \
  --allow tcp:0-65535,udp:0-65535,icmp \
  --source-ranges 192.168.0.0/16
```

### Create a firewall rule to allow SSH and ICMP traffic to the instances in 'on-prem' network
```bash
gcloud compute firewall-rules create on-prem-allow-ssh-icmp \
    --network on-prem \
    --allow tcp:22,icmp
```

Update priority of firewall rule  
```
gcloud compute firewall-rules create \
mynetwork-ingress-deny-icmp-all --network \
mynetwork --action DENY --direction INGRESS --rules icmp \
--priority 500
```

## Cloud Storage

### Create a multi_region bucket
```bash
export BUCKET_NAME=$(gcloud config get-value project)
gsutil mb gs://$BUCKET_NAME
# gsutil rm -r gs://$BUCKET_NAME
```

```bash
export BUCKET_NAME=$(gcloud config get-value project)
gsutil mb -b on -l us-east1 gs://$BUCKET_NAME
```

```bash
gsutil iam ch allUsers:objectViewer gs://$BUCKET_NAME
# gsutil iam ch -d allUsers:objectViewer gs://$BUCKET_NAME
```

```bash
gsutil ls -l gs://$BUCKET_NAME
gsutil cp ada.jpg gs://$BUCKET_NAME
gsutil rm ada.jpg gs://$BUCKET_NAME
gsutil cp gs://$BUCKET_NAME .
```

```bash
gsutil acl ch -u AllUsers:R gs://$BUCKET_NAME/ada.jpg
# gsutil acl ch -d AllUsers gs://$BUCKET_NAME/ada.jpg
```

Enable logging within a bucket:  
```bash
export BUCKET_NAME=$(gcloud config get-value project)
gsutil mb gs://$BUCKET_NAME
gsutil mk gs://$BUCKET_NAME-logs
gsutil acl ch -g cloud-storage-analytics@google.com:W gs://$BUCKET_NAME-logs
gsutil defsack set project-private gs://$BUCKET_NAME-logs
gsutil logging set on -b gs://$BUCKET_NAME-logs gs://$BUCKET_NAME
```

## Pub/Sub

```bash
export TOPIC=MyTopic
gcloud pubsub topics create $TOPIC
gcloud pubsub topics publish $TOPIC --message="Hello World!" --attribute=KEY1=VAL1,KEY2=VAL2

export SUBSCRIPTION=MySub
gcloud pubsub subscriptions create $SUBSCRIPTION --topic=$TOPIC
gcloud pubsub subscriptions pull --auto-ack $SUBSCRIPTION
```

## MySQL

```bash
gcloud services enable sqladmin.googleapis.com

export REGION=us-central1
export SQL_INSTANCE_ID=sql-instance
export MYSQL_DATABASE=wordpress
export MYSQL_USER=wp_user
export MYSQL_PASSWORD=wp_storm

gcloud sql instances create $SQL_INSTANCE_ID --tier=db-n1-standard-2 --region=$REGION

gcloud sql instances create $SQL_INSTANCE_ID --tier=g1-small --region=$REGION

export INSTANCE_CONNECTION_NAME=\${DEVSHELL_PROJECT_ID}:\${REGION}:\${SQL_INSTANCE_ID}

gcloud sql databases create $MYSQL_DATABASE --instance=$SQL_INSTANCE_ID

gcloud sql users create $MYSQL_USER --host="%" --instance=$SQL_INSTANCE_ID --password="$MYSQL_PASSWORD"

gcloud sql connect $SQL_INSTANCE_ID --user=root --quiet  # Default root's password is blank


```

## Deployment Manager

List 'network' resource types  
```bash
gcloud deployment-manager types list | grep network
gcloud deployment-manager types list | grep subnetwork
gcloud deployment-manager types list | grep firewall
```

customnetwork-template.jinja:  
```yaml
resources:
- name: {{ env["name"] }}
  type: compute.v1.network
  properties:
    autoCreateSubnetworks: false

```

subnetwork-template.jinja:  
```yaml
resources:
- name: {{ env["name"] }}
  type: compute.v1.subnetwork
  properties:
    ipCidrRange: {{ properties["ipCidrRange"] }}
    network: {{ properties["network"] }}
    region: {{ properties["region"] }}

```

firewall-template.jinja:  
```yaml
resources:
- name: {{ env["name"] }}
  type: compute.v1.firewall
  properties:
    network: {{ properties["network"] }}
    sourceRanges: ["0.0.0.0/0"]
    allowed:
    - IPProtocol: {{ properties["IPProtocol"] }}
      ports: {{ properties["Port"] }}


### Create VM
create-vm.yaml file
Replace [PROJECT_ID]

autonetwork-template.jinja:  
```yaml
resources:
- name: {{ env["name"] }}
  type: compute.v1.network
  properties:
    # RESOURCE properties go here
    autoCreateSubnetworks: true
```

```yaml
resources:
- name: myfirstvm
  type: compute.v1.instance
  properties:
    #basic configuration comes here
        zone: us-central1-a
        machineType: https://www.googleapis.com/compute/v1/projects/[PROJECT_ID]/zones/us-central1-a/machineTypes/f1-micro
        disks:
        - deviceName: boot
          type: PERSISTENT
          boot: true
          autoDelete: true
          initializeParams:
            sourceImage: https://www.googleapis.com/compute/v1/projects/debian-cloud/global/images/debian-9-stretch-v20180105
        networkInterfaces:
        - network: https://www.googleapis.com/compute/v1/projects/[PROJECT_ID]/global/networks/default
          accessConfigs:
          - name: External NAT
            type: ONE_TO_ONE_NAT
```
 
```bash
 gcloud deployment-manager deployments create create-vm --config create-vm.yaml
```

## Logging

List all logs  
```bash
gcloud logging logs list
```

## Cloud KMS

```bash
gcloud services enable cloudkms.googleapis.com

export KEYRING_NAME=lab-keyring
export CRYPTOKEY_1_NAME=labkey-1
export CRYPTOKEY_2_NAME=labkey-2

gcloud kms keyrings create $KEYRING_NAME --location us

gcloud kms keys create $CRYPTOKEY_1_NAME --location us \
--keyring $KEYRING_NAME --purpose encryption

gcloud kms keys create $CRYPTOKEY_2_NAME --location us \
--keyring $KEYRING_NAME --purpose encryption

gcloud kms keys list --keyring $KEYRING_NAME --location us

gsutil kms authorize -p $DEVSHELL_PROJECT_ID -k \
projects/$DEVSHELL_PROJECT_ID/locations/us/keyRings\
/$KEYRING_NAME/cryptoKeys/$CRYPTOKEY_1_NAME
gsutil kms authorize -p $DEVSHELL_PROJECT_ID -k \
projects/$DEVSHELL_PROJECT_ID/locations/us/keyRings\
/$KEYRING_NAME/cryptoKeys/$CRYPTOKEY_2_NAME


gsutil kms encryption -k \
projects/$DEVSHELL_PROJECT_ID/locations/us/keyRings\
/$KEYRING_NAME/cryptoKeys/$CRYPTOKEY_1_NAME \
gs://$DEVSHELL_PROJECT_ID-kms

gsutil kms encryption gs://$DEVSHELL_PROJECT_ID-kms

```

## BigQuery


```bash
bq --location=US mk -d \
--default_table_expiration 3600 \
--description "This is my dataset." \
source_data

bq load --autodetect $DEVSHELL_PROJ:source_data.events gs://cloud-training/gcpsec/labs/bq-authviews-source.csv

bq --location=US mk -d \
--default_table_expiration 3600 \
--description "This is my dataset." \
analyst_views

```

```bash
bq query --use_legacy_sql=false \
'SELECT
   COUNT(*)
 FROM
   `bigquery-public-data`.samples.shakespeare'
```

## Health Check

```bash
gcloud compute health-checks create http http-health-check \
    --port 80

gcloud beta compute health-checks create tcp http-health-check-1 \
  --project=qwiklabs-gcp-00-fed15f8afb6e \
  --port=80 --proxy-header=NONE \
  --no-enable-logging \
  --check-interval=10 \
  --timeout=5 \
  --unhealthy-threshold=3 \
  --healthy-threshold=2
```

## Cloud Function

Deploy a nodejs cloud function from cloud shell (index.js, package.json files are in current directory):  
```bash
gcloud functions deploy memories-thumbnail-generator \
--entry-point thumbnail \
--runtime nodejs14 \
--trigger-resource $BUCKET_NAME \
--trigger-event google.storage.object.finalize

```

## IAP - Identity-Aware Proxy

```bash


```

## Secret Manager

```bash
gcloud secrets create my-secret
gcloud secrets versions access 1 --secret="password"
gcloud secrets versions access 2 --secret="password"
```

## Cloud Armor

```bash
gcloud compute security-policies rules create 1000 \
    --security-policy $POLICY_NAME \
    --description "deny traffic from access-test VM" \
    --src-ip-ranges "35.244.71.114" \
    --action "deny-404"

gcloud compute backend-services update web-backend \
    --security-policy $POLICY_NAME \
    --global

```
