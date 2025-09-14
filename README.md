# Monitoring Multi-Tier Java Web Application on Kubernetes with Prometheus & Grafana (Helm)

This project demonstrates how to deploy and monitor a multi-tier Java web application on a Kubernetes (K8s) cluster.  
It extends the base application deployment by installing **Prometheus** and **Grafana** (via Helm charts) to collect metrics and visualize application and cluster performance.

## Architecture Overview

### Application Components  

| Component      | Type (K8s Objects)            | Service Type  |
| -------------- | ----------------------------- | ------------- |
| Database       | Deployment + ReplicaSet       | ClusterIP     |
| MemCache       | Deployment + ReplicaSet       | ClusterIP     |
| RabbitMQ       | Deployment + ReplicaSet       | ClusterIP     |
| Application (Tomcat) | Deployment + ReplicaSet | LoadBalancer  |

**Summary:**
- 4 Deployments  
- 3 ClusterIP Services  
- 1 LoadBalancer Service  
- 1 Secret  

### Monitoring Components  

| Component  | Source | Installation Method | Notes |
| ---------- | ------ | ------------------- | ----- |
| Prometheus | Official Helm chart | `helm install prometheus` | Scrapes app & K8s metrics |
| Grafana    | Official Helm chart | `helm install grafana`    | Dashboards + visualization |

## Prerequisites  

- A running Kubernetes cluster with `kubectl` and `helm` configured  
- DockerHub access to pull application images  
- All microservice images are available on [Docker Hub Repository](#) (replace with actual link)  

## Deployment Steps  

### 1. Clone the repository  
```bash
git clone https://github.com/your-username/monitoring-multi-tier-java-app-k8s-prometheus-grafana-helm.git
cd monitoring-multi-tier-java-app-k8s-prometheus-grafana-helm
```

### 2. Deploy the Java Web Application  
Apply all manifests for the application:
```bash
kubectl -n myapp apply -f manifests/
```

Scale manually if needed:
```bash
kubectl scale deployment app-deployment --replicas=5 -n myapp
```

Or configure HPA/VPA for automatic scaling.

### 3. Install Prometheus via Helm  
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/prometheus --namespace monitoring --create-namespace
```

### 4. Install Grafana via Helm  
```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install grafana grafana/grafana --namespace monitoring
```

### 5. Access Grafana UI  
Forward the Grafana service port or expose it as a LoadBalancer:  
```bash
kubectl port-forward svc/grafana 3000:80 -n monitoring
```
Then open `http://localhost:3000` (default credentials: `admin` / `prom-operator` unless overridden).  

## Deployment Status  
- Verify application pods:
  ```bash
  kubectl get pods -n myapp
  ```
- Verify Prometheus & Grafana:
  ```bash
  kubectl get pods -n monitoring
  ```

## Dashboards & Alerts  
- Import or build dashboards in Grafana to visualize your application metrics (Tomcat, RabbitMQ, JVM, etc.)  
- Configure Prometheus alert rules as needed (High CPU, pod restarts, memory usage, etc.)  


## Repository Contents  
- `manifests/` – K8s YAML files for the multi-tier Java web application  
- `helm/` (optional) – Custom values or overrides for Prometheus/Grafana  
- `README.md` – This documentation  
