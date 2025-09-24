# Deploying Prometheus and Grafana on Kubernetes Using Helm
This project demonstrates how to deploy Prometheus and Grafana on a Kubernetes (K8s) cluster using Helm. It uses the official kube-prometheus-stack Helm chart from the Prometheus Community repository to set up a full monitoring stack.

<img width="549" height="382" alt="493312281-5c22d83d-eef9-4df5-a0e0-045e3ccee3cb" src="https://github.com/user-attachments/assets/b0746c85-abb7-4239-84e4-9502b6ede952" />


## kube-prometheus-stack Architecture
This kube-prometheus-stack is a one-shot deployment of everything you need to monitor a K8s cluster. It consists of:

- 1. Prometheus Operator
  - Creates the Prometheus instance.
  - Deploys Alertmanager for alerts configuration.

- 2. Exporters
  - Node Exporter: collects node metrics.
  - Kube State Metrics: collects Kubernetes object metrics (pods, deployments, services, etc.).

- 3. Grafana
  - Provides dashboards and visualization.

## Prerequisites
-   A running Kubernetes cluster.
-   Helm installed on your machine. Follow the official [Helm installation guide](https://helm.sh/docs/intro/install/).

## Deployment Steps
### Step 1: Install Helm
- For Debian-based systems
  ``` bash
  sudo apt-get install curl gpg apt-transport-https --yes
  curl -fsSL https://packages.buildkite.com/helm-linux/helm-debian/gpgkey | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
  echo "deb [signed-by=/usr/share/keyrings/helm.gpg] https://packages.buildkite.com/helm-linux/helm-debian/any/ any main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
  sudo apt-get update
  sudo apt-get install helm
  helm version
  ```

### Step 2: Add Prometheus Community Helm Repository
  ``` bash
  helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
  helm repo update
  helm repo list
  ```

### Step 3: Create "values.yaml" file
- For instance, customize service types and Grafana credentials.
  ``` yaml
  grafana:
    service:
      type: NodePort
      nodePort: 30094
    adminUser: admin
    adminPassword: admin
  
  prometheus-node-exporter:
    service:
      type: NodePort
      nodePort: 30095
  
  kube-state-metrics:
    service:
      type: NodePort
      nodePort: 30096

### Step 4: Install the Stack
- Create a namespace (e.g., `monitoring`) and install the chart passing `values.yaml` file.
  ``` bash
  helm install prometheus prometheus-community/kube-prometheus-stack   --version 77.6.0   --namespace monitoring   --create-namespace   -f values.yaml
  ```
  
- List installed Helm charts:
  
  ``` bash
  helm list -A
  ```

### Step 5: Upgrade Values (if needed)
- If `values.yaml` file is modified, upgrade the release:
  ``` bash
  helm upgrade prometheus prometheus-community/kube-prometheus-stack   --namespace monitoring   -f values.yaml
  ```

### Step 6: Check cluster status
- Check the `monitoring` namespace for running components.
  <img width="2167" height="1212" alt="Screenshot 2025-09-15 162316" src="https://github.com/user-attachments/assets/6c342e05-5d66-4c8c-80a4-274726859183" />

### Step 7: Access Dashboards
- Access Grafana dashboard.
  <img width="3599" height="1831" alt="Screenshot 2025-09-15 195511" src="https://github.com/user-attachments/assets/a7df7ba3-a146-47e6-b554-a5c79a9db9eb" />

## References
- [kube-prometheus-stack on Artifact Hub](https://artifacthub.io/packages/helm/prometheus-community/kube-prometheus-stack)
- [Official Helm Documentation](https://helm.sh/docs/)
