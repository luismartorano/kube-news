# Kube-News V1 - By Luis Martorano

kind create cluster --config cluster.yaml 

## KIND
```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
- role: worker
- role: worker
```
## Ingress NGINX
```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```
```sh
kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=90s
```
## LOAD BALANCER

Installing metallb using default manifests

```sh
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.12.1/manifests/namespace.yaml
```
```sh
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.12.1/manifests/metallb.yaml
```
```sh
kubectl get pods -n metallb-system --watch
```
```sh
docker network inspect -f '{{.IPAM.Config}}' kind
```
```sh
kubectl apply -f https://kind.sigs.k8s.io/examples/loadbalancer/metallb-configmap.yaml
```

-- VERIFICAÇÃO APÓS A CRIAÇÃO DE UM SVC--

```sh
LB_IP=$(kubectl get svc/nomedoserviço -o=jsonpath='{.status.loadBalancer.ingress[0].ip}')
```

#should output foo and bar on separate lines 
```sh
for _ in {1..10}; do
  curl ${LB_IP}:porta usada na imagem
done
```
