# gitops
A collection of kustomized gitops manifests

```
export env=dev
az k8s-configuration flux create \
-g aks --cluster-name master --name flux-${env} --cluster-type managedClusters --sync-interval 30s --ns cluster-config -s cluster \
-u https://github.com/LovelaceEngineering/gitops.git --branch main \
-k name=infra path=./src/gitops/clusters/${env}/infra prune=true sync_interval=30s  \
-k name=apps path=./src/gitops/clusters/${env}/apps/ prune=true sync_interval=30s dependsOn=infra
```