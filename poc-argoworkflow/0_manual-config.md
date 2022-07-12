# longhorn
- Install longhorn in the cluster
- create a [zfs] directory /srv/longhorn/data1/ on all nodes
- Configure a new data store on all nodes
- Remove the default data store on all nodes

# Argo workflow

- Install argo stack

```
export ARGO_VERSION=v3.3.8

# workflow
kubectl apply -n argo -f "https://github.com/argoproj/argo-workflows/releases/download/${ARGO_VERSION}/install.yaml"

# argo server
# kubectl apply -n argo -f "https://raw.githubusercontent.com/argoproj/argo-workflows/${ARGO_VERSION}/manifests/base/argo-server/argo-server-deployment.yaml"
```

- Create a System account

```
kubectl -n argo create sa argo-server-adm
#kubectl -n argo create clusterrolebinding argo-server-adm --clusterrole=argo-aggregate-to-admin --serviceaccount=argo:argo-server-adm
kubectl -n argo create clusterrolebinding argo-server-adm --clusterrole=argo-server-cluster-role --serviceaccount=argo:argo-server-adm

```

- Get the access token

```
# admin
SECRET_ID=$(kubectl get -n argo sa argo-server-adm -o=jsonpath='{.secrets[0].name}')
TOKEN="Bearer $(kubectl get secret ${SECRET_ID} -o=jsonpath='{.data.token}' | base64 --decode)"
echo $TOKEN
```

-

- For quick access without an ingress:

```
while true; do kubectl -n argo port-forward svc/argo-server 2746:2746; sleep 1; done
```
Connect to https://localhost:2746
