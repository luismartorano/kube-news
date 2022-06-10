helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm repo update

helm show values prometheus-community/prometheus

helm upgrade --install prometheus prometheus-community/prometheus --set alertmanager.enabled=false,server.persistentVolume.enabled=false,server.service.type=LoadBalancer,server.global.scrape_interval=10s,pushgateway.enabled=false

kubectl get svc --namespace default -w prometheus-server

export SERVICE_IP=$(kubectl get svc --namespace default prometheus-server -o jsonpath='{.status.loadBalancer.ingress[0].ip}')

echo http://$SERVICE_IP:80
