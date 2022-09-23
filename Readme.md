# Kubernetes clusters configuration

Contains the kubernetes clusters configuration.

Organization:
- One directory per cluster
- The directory name must match the cluster name in rancher


# ArgoCD management

## Bootstrap

On the cluster:
```
export NS=argocd
kubectl create namespace $NS
kubectl apply -n $NS \
   -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Follow the step  in https://docs.softwareheritage.org/sysadm/deployment/argocd.html

## Store ArgoCD admin dashboard

Copy the password in your clipboard (and try and connect to the dashboard:
```
kubectl -n $NS get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d | xsel --input --clipboard
```

## Change ArgocCD admin login password

```
# examples:
# - ARGOCD_SERVER=localhost:8080
# - ARGOCD_SERVER=argo-worker01.internal.admin.swh.network:443
argocd login $ARGOCD_SERVER
argocd account update-password
```

Note:
The password must be a string of characters between [8-32] characters.

