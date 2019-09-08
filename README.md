# Learn the hard Way - Google Cloud Platform


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


