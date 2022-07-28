# ArgoCD Cli installation

For test purpose, you can use minikube. Otherwise, use a rancher cluster.

On the operator machine, one must have the argocd tool installed:
```
curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
chmod +x /usr/local/bin/argocd
```

# ArgoCD installation

On the cluster:
```
export NS=argocd
kubectl create namespace $NS
kubectl apply -n $NS \
   -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## minikube

### Access Argo CD API Server

By default, the Argo CD API server is not exposed with an external IP. To access the API
server, expose the Argo CD API server through the service type Load Balancer:

```
kubectl patch svc argocd-server -n $NS -p '{"spec": {"type": "LoadBalancer"}}'
```

### port-forward

```
kubectl port-forward services/argocd-server 8080:443 -n $NS
```

### ingress controller

You will also need to enable the ingress controller:
```
$ minikube addons enable ingress
```

## Standard

We'll use an ingress to access the dashboard:
```
kubectl apply -n $NS -f dashboard/ingress.yaml
```

Use one of the dns records (associated to the ip below) to access your dashboard:
```
kubectl get ingress -A
NAMESPACE   NAME            CLASS    HOSTS   ADDRESS                       PORTS   AGE
argocd      argocd-server   <none>   *       $ip_0,$ip_2,...,$ip_n   80      29m
```

# Store ArgoCD admin dashboard

Copy the password in your clipboard (and try and connect to the dashboard:
```
kubectl -n $NS get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d | xsel --input --clipboard
```

# Change ArgocCD admin login password

```
# examples:
# - ARGOCD_SERVER=localhost:8080
# - ARGOCD_SERVER=argo-worker01.internal.admin.swh.network:443
argocd login $ARGOCD_SERVER
argocd account update-password
```

Note:
The password must be a string of characters between [8-32] characters.

# Custom configuration

We need to retrieve the cluster configurations we want argocd to discuss with out of the
rancher ui [1].

For each cluster, we want to create the following yaml file:

```
apiVersion: v1
kind: Secret
metadata:
  # must match $NS at the top of the readme
  namespace: argocd
  name: $environment-cluster-config
  labels:
    argocd.argoproj.io/secret-type: cluster
type: Opaque
stringData:
  name: $CLUSTER_NAME
  server: $CLUSER_URL
  config: |
    {
      "bearerToken": "$CLUSTER_TOKEN",
      "tlsClientConfig": {
        "insecure": false,
        "caData": "$CLUSTER_CERTIFICATE"
      }
    }

```

where the $variable are extracted out of the retrieve yaml configuration:
- CLUSTER_NAME: e.g. deployment-internship
- CLUSTER_URL: e.g. https://rancher.euwest.azure.internal.softwareheritage.org/k8s/clusters/c-fvnrx
- CLUSTER_TOKEN: authentication token of the cluster (from clusters > users >
  $cluster-name > user > token)
- CLUSTER_CERTFICATE: extracted out (from clusters[0] > cluster >
  certificate-authority-data)


Custom cluster-config.yaml file to add to a new cluster:

```
kubectl apply -f cluster-config.secret.yaml
```

[1] https://rancher.euwest.azure.internal.softwareheritage.org/ ("download kubeconfig")

# Jenkinsfile in "charts_code/worker" directory

- Custom this fields
  - credentialsID
  - passwordVariable
  - usernameVariable

- Custom sh commands

# Jenkinsfile in "image_code"

- Custom stage

# Values files

- Custom values files
  - myvalues.yaml
  - ranchervalues.yaml
  - myvalues.yaml

# Custom argocd app file

- Custom stag_app_deployment.yaml
- Custom production_app_deployment.yaml

# Create stag argocd app

```
kubectl apply -f staging-app-deployment.yaml
```

# Create production argocd app

```
kubectl apply -f production-app-deployment.yaml
```
