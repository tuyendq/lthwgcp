# Learn the hard Way - Google Cloud Platform

Follow Coursera Specilization

=======
## Common environment variable
### Project ID
<pre>
<code>
export PROJECT_ID=$(gcloud config get-value project)
</code>
</pre>

### Compute Engine default service account
<pre>
<code>
export SA_EMAIL=$(gcloud iam service-accounts list --filter="displayName:Compute Engine default service account" --format='value(email)')
</code>
</pre>

## Firewall rules

### List all firewall rules
<pre>
<code>
$ gcloud compute firewall-rules list
</code>
</pre>

<pre>
<code>
$ gcloud compute firewall-rules create "default-allow-http" \
      --allow tcp:80 \
      --source-ranges="0.0.0.0/0" \
      --description="Allow Web Server traffic" \
      --target-tags="http-server"
</code>
</pre>


<pre>
<code>
$ gcloud compute firewall-rules create "default-allow-health-check" \
      --allow tcp \
      --source-ranges="130.211.0.0/22,35.191.0.0/16" \
      --description="Allow health check traffic" \
      --target-tags="http-server"
</code>
</pre>

## Deployment Manager
### Create VM
create-vm.yaml file
Replace [PROJECT_ID]
<pre>
<code>
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
 </code>
 </pre>
 
 <pre>
 <code>
 gcloud deployment-manager deployments create create-vm --config create-vm.yaml
 </code>
 </pre>
