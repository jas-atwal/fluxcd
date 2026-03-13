## Install the Flux CLI

brew install fluxcd/tap/flux

## Generate a Personal Access Token (PAT)
Create a PAT via `https://github.com/settings/tokens`
Create a ENV via `export GITHUB_TOKEN=XYZ`

## Install the Flux controllers
The recommended way of installing Flux on Kubernetes clusters is by using the bootstrap procedure

The flux bootstrap github command deploys the Flux controllers on a Kubernetes cluster and configures the controllers to sync the cluster state from a GitHub repository. Besides installing the controllers, the bootstrap command pushes the Flux manifests to the GitHub repository and configures Flux to update itself from Git.

`flux bootstrap github \
  --owner=jas-atwal \
  --repository=fluxcd \
  --branch=main \
  --path=/clusters/production \
  --personal
  --private=false
  --componenets-extra='image-reflector-controller, image-automation-controller'`

# Check flexcd pods are running
kubectl get pods -n fluxcd-system



# Clean up
The Flux CLI has a built-in command specifically designed to scrub the components from your cluster (the controllers, CRDs, and namespaces).

`flux uninstall --namespace=flux-system`

The bootstrap command did two things outside of your cluster that the uninstall command won't touch automatically:

Remove the Deployment Key: Go to your GitHub repository (jas-atwal/fluxcd) -> Settings -> Deploy keys. Delete the key labeled flux-system. If you don't do this, you'll leave a stale SSH access key sitting in your repo.

Delete the Manifests: Flux committed several YAML files to your repository under /clusters/staging. If you don't plan on using Flux again for this cluster, you should manually delete those files and commit the change so your repo stays clean.

[!IMPORTANT]
This will not delete the applications that Flux deployed (the Helm charts or Kustomizations). It only removes the "engine" (Flux) that was managing them. If you want those gone too, you should delete their namespaces manually



