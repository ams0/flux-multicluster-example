# Install ArgoCD

helm repo add argo https://argoproj.github.io/argo-helm
helm upgrade -i \
-n argod --create-namespace \
-f helm/argocd-values.yaml \
argocd argo/argo-cd 