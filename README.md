# Learn the hard Way - Google Cloud Platform

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


