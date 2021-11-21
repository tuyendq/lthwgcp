# Learn the hard Way - Google Cloud Platform

Follow Coursera Specializations:  
- [Architecting with Google Compute Engine Specialization](https://www.coursera.org/specializations/gcp-architecture)  
- [Networking in Google Cloud Specialization](https://www.coursera.org/specializations/networking-google-cloud-platform)  
- [Security in Google Cloud Specialization](https://www.coursera.org/specializations/security-google-cloud-platform)  
- [Architecting with Google Kubernetes Engine Specialization](https://www.coursera.org/specializations/architecting-google-kubernetes-engine)  
- [Architecting Hybrid Cloud Infrastructure with Anthos Specialization](https://www.coursera.org/specializations/architecting-hybrid-cloud-infrastructure-anthos)  

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

## API Services

```bash
gcloud services list --available
```

```bash
gcloud services enable deploymentmanager.googleapis.com
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

## VPC Network

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

### List all firewall rules
```bash
gcloud compute firewall-rules list
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


## Pub/Sub

```bash
export TOPIC=MyTopic
gcloud pubsub topics create $TOPIC
gcloud pubsub topics publish $TOPIC --message="Hello World!" --attribute=KEY1=VAL1,KEY2=VAL2

export SUBSCRIPTION=MySub
gcloud pubsub subscriptions create $SUBSCRIPTION --topic=$TOPIC
gcloud pubsub subscriptions pull --auto-ack $SUBSCRIPTION
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
