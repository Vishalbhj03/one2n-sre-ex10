# Observability Stack – Exercise 10

This setup adds full observability (metrics + logs + uptime monitoring) to the student-api platform deployed on Kubernetes.  
All components are deployed using Helm and run inside the `observability` namespace.

## Components Deployed

- Prometheus – collects metrics
- Loki – stores logs
- Promtail – forwards logs (only student-api logs)
- Grafana – visualization for metrics and logs
- Blackbox Exporter – uptime and latency checks
- Postgres Exporter – database metrics
- Node Exporter – node-level metrics
- kube-state-metrics – Kubernetes object metrics

## What is Monitored

- student-api health, uptime, and latency
- Hashicorp Vault availability
- ArgoCD server availability
- Kubernetes node CPU, memory, disk
- Kubernetes workloads and states
- Postgres database metrics
- Application logs from student-api namespace only


## Prerequisites

- Kubernetes cluster running
- Helm installed
- student-api, Vault, and ArgoCD already deployed

## Installation Steps

Create namespace:

kubectl create namespace observability

Install Prometheus stack (includes node-exporter and kube-state-metrics):

helm install prometheus prometheus-community/kube-prometheus-stack \
-n observability \
-f prometheus/values.yaml

Install Loki:

helm install loki grafana/loki \
-n observability \
-f loki/values.yaml

Install Promtail (configured to send only student-api logs):

helm install promtail grafana/promtail \
-n observability \
-f promtail/values.yaml

Install Grafana:

helm install grafana grafana/grafana \
-n observability \
-f grafana/values.yaml

Install Blackbox Exporter (uptime and latency monitoring):

helm install blackbox prometheus-community/prometheus-blackbox-exporter \
-n observability \
-f blackbox-exporter/values.yaml

Install Postgres Exporter (database metrics):

helm install postgres-exporter prometheus-community/prometheus-postgres-exporter \
-n observability \
-f postgres-exporter/values.yaml

## Grafana Access

kubectl port-forward svc/grafana 3000:80 -n observability

Open in browser:

http://localhost:3000

Login:
- Username: admin
- Password: stored in Grafana Kubernetes secret

## Data Sources

Grafana is preconfigured with:
- Prometheus for metrics
- Loki for logs

## Notes

- Only student-api logs are sent to Loki
- No secrets or credentials are committed
- Database credentials are injected via Kubernetes secrets
- Configuration follows Helm and GitOps best practices

