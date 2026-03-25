# Installation-Tools
Installing Prometheus + Grafana using Helm in a Kubernetes cluster is one of the most common DevOps setups (especially in EKS/AKS/GKE).

I’ll give you a production-style step-by-step guide 👇

Step 1: Prerequisites
Make sure you have:
Kubernetes cluster (EKS / AKS / GKE / Minikube)
kubectl configured
helm installed
kubectl get nodes
helm version

Step 2: Add Helm Repository
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

Step 3: Create Namespace
kubectl create namespace monitoring

Step 4: Install Prometheus + Grafana (kube-prometheus-stack)
This chart installs:
Prometheus
Grafana
Alertmanager
Node Exporter
kube-state-metrics

helm install monitoring prometheus-community/kube-prometheus-stack \
  --namespace monitoring

kubectl get pods -n monitoring

kubectl port-forward svc/monitoring-grafana 3000:80 -n monitoring

kubectl port-forward svc/monitoring-kube-prometheus-prometheus 9090:9090 -n monitoring

To check the secret like password of grafana:

kubectl get secret monitoring-grafana -n monitoring -o jsonpath="{.data.admin-password}" | base64 --decode


kubectl patch svc monitoring-grafana -n monitoring \
  -p '{"spec": {"type": "NodePort"}}'


kubectl patch svc monitoring-grafana -n monitoring \
  -p '{"spec": {"type": "LoadBalancer"}}'

  kubectl get svc -n monitoring

if want to add loadbalancer add this into values.yaml and upgrade the helm chart

  grafana:
  service:
    type: LoadBalancer

prometheus:
  prometheusSpec:
    serviceMonitorSelectorNilUsesHelmValues: false

helm install monitoring prometheus-community/kube-prometheus-stack \
  -n monitoring -f values.yaml

  helm upgrade monitoring prometheus-community/kube-prometheus-stack -n monitoring



  error: need to add to values.yaml
  prometheus-node-exporter:
  hostRootFsMount:
    enabled: false

helm upgrade monitoring prometheus-community/kube-prometheus-stack \
  -n monitoring -f values.yaml

kubectl port-forward svc/monitoring-kube-prometheus-prometheus 9090:9090 -n monitoring



ports : http://<NodeIP>:32542
ports: http://<NodeIP>:32000       # optional (must be 30000–32767)