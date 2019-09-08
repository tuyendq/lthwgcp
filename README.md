# Learn the hard Way - Google Cloud Platform


## Firewall rules

<pre>
<code>
$ gcloud compute firewall-rules list
</code>
</pre>

<pre>
<code>
$ gcloud compute firewall-rules create "default-allow-http" --allow tcp:80 \
      --source-ranges="0.0.0.0/0" \
      --description="Allow Web Server traffic" \
      --target-tags="http-server"
</code>
</pre>


