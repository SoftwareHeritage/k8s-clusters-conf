
# ArgoCD Cli installation 

 - curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
 - chmod +x /usr/local/bin/argocd

# ArgoCD installation 

 - kubectl create namespace argocd
 - wget https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml -O argocd_installation.yaml
 - kubectl apply -f argocd_installation.yaml

# Access The Argo CD API Server
 - By default, the Argo CD API server is not exposed with an external IP. To access the API server, choose one of the following techniques to expose the Argo CD API server:
  
  - Service type Load Balancer
    - kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'

  - Custom Ingress file named ingress.yaml

# Store ArgoCD admin dashboard 

 - kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d >  password.txt

# Change ArgocCD admin login password

 - argocd login <ARGOCD_SERVER> # example : argocd login localhost:8080 
 - argocd account update-password


# Custom the secret.yaml file to add a new cluster

 - kubectl apply -f secret.yaml

# Jenkinsfile in "charts_code/worker" directory

 - Custom this fields
   - credentialsID
   - passwordVariable
   - usernameVariable
 - Custum sh commands

# Jenkinsfile in "image_code"

 - Custum stage


# Values files

 - Custom values files
  - myvalues.yaml
  - ranchervalues.yaml
  - myvalues.yaml

# Custom argocd app file

 - Custom stag_app_deployment.yaml
 - Custom production_app_deployment.yaml

# Create stag argocd app

 - kubectl apply -f stag_app_deployment.yaml

# Create production argocd app

 - kubectl apply -f production_app_deployment.yaml