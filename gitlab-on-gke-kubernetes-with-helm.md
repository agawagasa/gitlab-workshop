**Prerequisites**

- Google Cloud Engine Kubernetes cluster
- Ingress controller configured for external access

Nginx Ingress Controller

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-ingress-controller
spec:
  selector:
    matchLabels:
      app: nginx-ingress-controller
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx-ingress-controller
    spec:
      containers:
        - name: nginx-ingress-controller
          image: quay.io/kubernetes-ingress-controller/nginx-ingress-controller:0.30.0
          ports:
            - name: http
              containerPort: 80
            - name: https
              containerPort: 443
          env:
            - name: POD_NAME
              valueFrom:
                fieldRef:
                  fieldPath: metadata.name
            - name: POD_NAMESPACE
              valueFrom:
                fieldRef:
                  fieldPath: metadata.namespace
          args:
            - -nginx-configmaps=$(POD_NAMESPACE)/nginx-config
            - -default-server-tls-secret=$(POD_NAMESPACE)/default-server-secret
```            
            
   **Install Helm**

`curl -o get_helm.sh https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get
chmod 700 get_helm.sh`

`./get_helm.sh`

`helm init`
            
 
**Storage class manifest**

```
cat > pd-ssd-storage.yaml <<EOF
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: pd-ssd
provisioner: kubernetes.io/gce-pd
parameters:
  type: pd-ssd
EOF
kubectl apply -f pd-ssd-storage.yaml
```

**Install GitLab chart**

`wget https://raw.githubusercontent.com/terraform-google-modules/terraform-google-gke-gitlab/master/values.yaml.tpl`

**Customize values.yaml file**

```
# Values for gitlab/gitlab chart on GKE
global:
  edition: ce
  hosts:
    domain: xip.io
    https: true
    gitlab: {}
    externalIP: 35.225.196.151 #Replace by your Nginx Ingress ExternalIP
    ssh: ~
    gitlab:
      name: gitlab.xip.io 
      https: true
    registry:
      name: gitlab-registry.xip.io
      https: true
    minio:
      name: gitlab-minio.xip.io
      https: true
  minio:
    enabled: true
  ## doc/charts/globals.md#configure-ingress-settings
  ingress:
    configureCertmanager: false
    class: "nginx"
    enabled: true
    tls:
      enabled: true
certmanager:
  install: false
nginx-ingress:
  enabled: false
prometheus:
  install: true
redis:
  install: true
postgresql:
  install: true
gitlab-runner:
  install: true
registry:
  enable: true
```

**Install the chart**

`helm repo add gitlab https://charts.gitlab.io/
helm install -f values.yaml --version 2.3.7 -n gitlab gitlab/gitlab`

**Verify the deployment**

```
kubectl get pods -n gitlab -o wide
NAME                                        READY   STATUS      RESTARTS   AGE   IP          NODE                                    NOMINATED NODE   READINESS GATES
gitlab-gitaly-0                             1/1     Running     0          1d   10.0.1.18   gke-jx-dbi-default-pool-01891a7e-2g7j              
gitlab-gitlab-exporter-7cf6b77d78-89f46     1/1     Running     0          1d   10.0.2.10   gke-jx-dbi-default-pool-01891a7e-1rb5              
gitlab-gitlab-shell-c548bd566-g6p7b         1/1     Running     0          1d   10.0.1.14   gke-jx-dbi-default-pool-01891a7e-2g7j              
gitlab-gitlab-shell-c548bd566-plg4t         1/1     Running     0          1d   10.0.2.13   gke-jx-dbi-default-pool-01891a7e-1rb5              
gitlab-migrations.1-tf5jl                   0/1     Completed   0          1d   10.0.1.10   gke-jx-dbi-default-pool-01891a7e-2g7j              
gitlab-minio-75567fcbb6-j9z9z               1/1     Running     0          1d   10.0.1.15   gke-jx-dbi-default-pool-01891a7e-2g7j              
gitlab-minio-create-buckets.1-2c8th         0/1     Completed   0          1d   10.0.1.11   gke-jx-dbi-default-pool-01891a7e-2g7j              
gitlab-postgresql-66d8d9574b-r66fm          2/2     Running     0          1d   10.0.2.12   gke-jx-dbi-default-pool-01891a7e-1rb5              
gitlab-prometheus-server-6fb685b9c7-q4jl5   2/2     Running     0          1d   10.0.1.16   gke-jx-dbi-default-pool-01891a7e-2g7j              
gitlab-redis-7668c4d476-nqddk               2/2     Running     0          1d   10.0.1.17   gke-jx-dbi-default-pool-01891a7e-2g7j              
gitlab-registry-5675459cbd-gjnx6            1/1     Running     0          1d   10.0.1.13   gke-jx-dbi-default-pool-01891a7e-2g7j              
gitlab-registry-5675459cbd-rpcn2            1/1     Running     0          1d   10.0.2.11   gke-jx-dbi-default-pool-01891a7e-1rb5              
gitlab-sidekiq-all-in-1-5fdc8f5868-8fnxl    1/1     Running     1          1d   10.0.1.9    gke-jx-dbi-default-pool-01891a7e-2g7j              
gitlab-task-runner-5cb689799-pzz5r          1/1     Running     0          1d   10.0.1.12   gke-jx-dbi-default-pool-01891a7e-2g7j              
gitlab-unicorn-9f9bf8574-cjqdh              2/2     Running     0          1d   10.0.2.9    gke-jx-dbi-default-pool-01891a7e-1rb5              
gitlab-unicorn-9f9bf8574-mfqrn              2/2     Running     0          1d   10.0.0.11   gke-jx-dbi-default-pool-01891a7e-715p
```

```
kubectl get svc -n gitlab
NAME                       TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE
gitlab-gitaly              ClusterIP   None                   8075/TCP,9236/TCP   1d
gitlab-gitlab-exporter     ClusterIP   10.3.254.216           9168/TCP            1d
gitlab-gitlab-shell        ClusterIP   10.3.242.235           22/TCP              1d
gitlab-minio-svc           ClusterIP   10.3.244.99            9000/TCP            1d
gitlab-postgresql          ClusterIP   10.3.240.220           5432/TCP            1d
gitlab-prometheus-server   ClusterIP   10.3.255.170           80/TCP              1d
gitlab-redis               ClusterIP   10.3.240.239           6379/TCP,9121/TCP   1d
gitlab-registry            ClusterIP   10.3.247.123           5000/TCP            1d
gitlab-unicorn             ClusterIP   10.3.253.173           8080/TCP,8181/TCP   1d
```

```
kubectl get ing -n gitlab
NAME              HOSTS                          ADDRESS          PORTS     AGE
gitlab-minio      gitlab-minio.xip.io            35.225.196.151   80, 443   1d
gitlab-registry   gitlab-registry.xip.io         35.225.196.151   80, 443   1d
gitlab-unicorn    gitlab.35.225.196.151.xip.io   35.225.196.151   80, 443   1d
```

**Connect to GitLab Web UI**

- Get the URL for your GitLab Server
```
export GITLAB_HOSTNAME=$(kuexport GITLAB_HOSTNAME=$(kubectl get ingresses.extensions gitlab-unicorn \
    -o jsonpath='{.spec.rules[0].host}')
echo "Your GitLab URL is: https://${GITLAB_HOSTNAME}"
 
Your GitLab URL is: https://gitlab.35.225.196.151.xip.io
```

- Get the root password
```
kubectl get secret gitlab-gkubectl get secret gitlab-gitlab-initial-root-password \
    -o go-template='{{.data.password}}' | base64 -d && echo
fZHnoz0H5p4exjBbUCqHvB13JIvIwepOOgolCtaS8XhdXbeL0akUJT6ZtViksnYF
```

- Visit the GitLab URL from step 1 in your browser and enter password from step 2

            
