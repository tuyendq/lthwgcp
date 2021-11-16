# Learn the hard Way - Google Cloud Platform

Follow Coursera Specilization

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

## Computer Engine

### Create VM
```bash
gcloud compute instances create test-vm --machine-type=n1-standard-1 --subnet=default --zone=us-central1-a
```
```bash
gcloud compute instances create nginx-vm \
--zone us-central1-a \
--machine-type f1-micro \
--metadata startup-script=sudo\ apt\ update$'\n'sudo\ apt\ install\ -y\ nginx-light$'\n'sudo\ systemctl\ enable\ nginx$'\n'sudo\ service\ nginx\ start
```

### Create VM with multiple network interfaces
```bash
gcloud compute instances create vm-appliance --zone=us-central1-c --machine-type=n1-standard-4 \
    --network-interface subnet=privatesubnet-us \
    --network-interface subnet=managementsubnet-us \
    --network-interface subnet=mynetwork
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


## VPC Network

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

## Cloud Storage

```bash
export BUCKET_NAME=$(gcloud config get-value project)
gsutil mb -b on -l us-east1 gs://$BUCKET_NAME
```

```bash
gsutil iam ch allUsers:objectViewer gs://$BUCKET_NAME
# gsutil iam ch -d allUsers:objectViewer gs://$BUCKET_NAME
```


## Deployment Manager
### Create VM
create-vm.yaml file
Replace [PROJECT_ID]

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
