```
helm repo add gitlab https://charts.gitlab.io/
helm repo update
helm upgrade --install gitlab gitlab/gitlab \
  --timeout 600s \
  --set global.hosts.domain=example.com \
  --set global.hosts.externalIP=10.10.10.10 \
  --set certmanager-issuer.email=me@example.com \
  --set postgresql.image.tag=13.6.0
  ```
  
  **Initial Login**

You can access the GitLab instance by visiting the domain specified during installation. The default domain would be gitlab.example.com, unless the global host settings were changed. If you manually created the secret for initial root password, you can use that to sign in as root user. If not, GitLab would’ve automatically created a random password for root user. This can be extracted by the following command (replace <name> by name of the release - which is gitlab if you used the command above).
  
  `kubectl get secret <name>-gitlab-initial-root-password -ojsonpath='{.data.password}' | base64 --decode ; echo`
