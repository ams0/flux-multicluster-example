# GitOps Kustomization

This folder (`src/gitops`) contains the common and per-cluster configuration applied by Flux v2. It's organized like this:

`bases/apps/` and `bases/infra/` contain the basic, common manifest to apply various pieces of infrastructure components and applications.

`environments/` container per-environment definitions of what to apply (and with what kustomization, i.e. a specific ingress name for dev or prd). 

To add/remove components from an environment, add/remove configurations under `clusters/(env)/(stage)` (where stage is `apps` or `infra`, more can be added too); you can also kustomize the individual charts on a per-environment basis changing the `clusters/(env)/(stage)/(component)/(component)-values.yaml`

The local environment/component `values.yaml` will be merged and override the base parameters of the Helm chart. It's also possible to have a different Helm chart version in the local kustomization before it's applied to all clusters (by changing the base in `src/gitops/(stage)/(component)/(component).yaml`).

This requires to be deployed as an Arc  `k8sconfiguration` via a command similar to:

```
env=dev
az k8s-configuration flux create \
-g (rg) --cluster-name (cluster_name) --name flux-${env} --cluster-type managedClusters --sync-interval 30s --ns cluster-config -s cluster \
-u (git repo) --branch main \
--ssh-private-key $SSH_PRIV_KEY_BASE64 \
-k name=infra path=./src/gitops/clusters/${env}/infra prune=true sync_interval=30s  \
-k name=apps path=./src/gitops/clusters/${env}/apps prune=true sync_interval=30s dependsOn=infra
```