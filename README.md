# ArgoCD Cli installation

On the operator machine:
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

# Access The Argo CD API Server

By default, the Argo CD API server is not exposed with an external IP. To access the API
server, expose the Argo CD API server through the service type Load Balancer:

```
kubectl patch svc argocd-server -n $NS -p '{"spec": {"type": "LoadBalancer"}}'
```

# Store ArgoCD admin dashboard

```
kubectl -n $NS get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d > password.txt
```

# Change ArgocCD admin login password

```
# example: argocd login localhost:8080
argocd login $ARGOCD_SERVER
argocd account update-password
```

Note:
With minikube, you will need the following port-forward:

```
kubectl port-forward services/argocd-server 8080:443 -n $NS
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
kubectl apply -f stag_app_deployment.yaml
```

# Create production argocd app

```
kubectl apply -f production_app_deployment.yaml
```
