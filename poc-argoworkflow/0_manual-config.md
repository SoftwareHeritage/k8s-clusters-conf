# longhorn
- Install longhorn in the cluster
- create a [zfs] directory /srv/longhorn/data1/ on all nodes
- Configure a new data store on all nodes
- Remove the default data store on all nodes

# Argo workflow

- Install argo stack

```
export ARGO_VERSION=v3.3.8
export NS=argo

# workflow
kubectl apply -n $NS -f "https://github.com/argoproj/argo-workflows/releases/download/${ARGO_VERSION}/install.yaml"

# argo server
# kubectl apply -n $NS -f "https://raw.githubusercontent.com/argoproj/argo-workflows/${ARGO_VERSION}/manifests/base/argo-server/argo-server-deployment.yaml"
```

- Create a System account

```
kubectl -n $NS create sa argo-server-adm
#kubectl -n $NS create clusterrolebinding argo-server-adm --clusterrole=argo-aggregate-to-admin --serviceaccount=argo:argo-server-adm
kubectl -n $NS create clusterrolebinding argo-server-adm --clusterrole=argo-server-cluster-role --serviceaccount=argo:argo-server-adm

```

- Get the access token

```
# admin
SECRET_ID=$(kubectl get -n $NS sa argo-server-adm -o=jsonpath='{.secrets[0].name}')
TOKEN="Bearer $(kubectl get secret -n $NS ${SECRET_ID} -o=jsonpath='{.data.token}' | base64 --decode)"
echo $TOKEN
```

-

- For quick access without an ingress:

```
while true; do kubectl -n $NS port-forward svc/argo-server 2746:2746; sleep 1; done
```
Connect to https://localhost:2746
