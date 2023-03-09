Exposing Deployment using Ingress-Nginx in kind 

1. Ensure you setup the external facing ports of 9080 > 80 of nginx (via extraPortMappings in config of cluster)
```
apiVersion: kind.x-k8s.io/v1alpha4
kind: Cluster
nodes:
  - role: control-plane
    # the ConfigPatches and ports are for Ingress mapping...
    kubeadmConfigPatches:
    - |
      kind: InitConfiguration
      nodeRegistration:
        kubeletExtraArgs:
          node-labels: "ingress-ready=true"
    extraPortMappings:
    - containerPort: 80
      hostPort: 9080
      protocol: TCP
    - containerPort: 443
      hostPort: 9443
      protocol: TCP
```
1. This will be applied during cluster creation like below:
```
KIND_EXPERIMENTAL_PROVIDER=podman kind create cluster --name second-kind --config /Users/sudranga1/.config/kind/config.yaml

```
1. Apply the nginx patch via ...
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```
1. Run sample like below using kustomize
```
kubectl apply -k ./kube/kustomization/

