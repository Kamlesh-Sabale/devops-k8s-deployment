# Monitoring Setup

## Prometheus & Grafana Stack

This project includes monitoring using the kube-prometheus-stack.

### Components
- **Prometheus**: Metrics collection and storage
- **Grafana**: Visualization and dashboards
- **Alertmanager**: Alert management
- **Node Exporter**: Node-level metrics
- **Kube State Metrics**: Kubernetes object metrics

### Access

**Grafana:**
- URL: Check the LoadBalancer service in `monitoring` namespace
- Username: `admin`
- Password: `admin123`

**Prometheus:**
- Available as ClusterIP service
- Access via port-forward: `kubectl port-forward -n monitoring svc/prometheus-kube-prometheus-prometheus 9090:9090`

### Pre-configured Dashboards
- Kubernetes Cluster Overview
- Node Metrics
- Pod Metrics
- Namespace Resources
- Network Metrics

### Installation
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

helm install prometheus prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --create-namespace \
  --set grafana.adminPassword=admin123 \
  --set grafana.service.type=LoadBalancer
```
