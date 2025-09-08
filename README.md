# 🚀 Kubernetes Monitoring Stack with Prometheus & Grafana

A **production-ready monitoring stack** built on **Kubernetes** using **Prometheus, Grafana, and Node Exporter**.
This setup also includes a **custom application** deployed from Docker Hub, so you can monitor real metrics from day one.

---

## ✨ Features

✅ Deploy Prometheus to scrape metrics
✅ Node Exporter for system-level metrics
✅ Grafana with beautiful dashboards
✅ Custom App deployed from Docker Hub
✅ Easy to extend for real-world projects

---

## 📂 Project Structure

```
manifests/
 ├── app-deployment.yaml           # Deploys the sample application (from Docker Hub)
 ├── grafana-deployment.yaml       # Deploys Grafana into Kubernetes
 ├── node-exporter-daemonset.yaml  # Deploys Node Exporter as a DaemonSet on all nodes
 ├── prometheus-configmap.yaml     # Prometheus configuration (scrape targets, jobs, etc.)
 ├── prometheus-deployment.yaml    # Deploys Prometheus into Kubernetes
```

---

## ⚡ Quick Start

### 1️⃣ Clone the repo

```bash
git clone https://github.com/<your-username>/k8s-prometheus-grafana.git
cd k8s-prometheus-grafana/manifests
```

### 2️⃣ Apply manifests

```bash
kubectl apply -f .
```

This will spin up Prometheus, Grafana, Node Exporter, and your app.

---

## 📊 Accessing the Prometheus Services

* **Prometheus** → [http://localhost:9090](http://localhost:9090)
1. Go to Status -> Target Health, to view the connected services Health Status.

---

## 🔗 Connecting Prometheus with Grafana

1. Open Grafana → **Configuration → Data Sources → Add data source**.
2. Select **Prometheus**.
3. Enter Prometheus URL:

   ```
   http://prometheus:9090
   ```
4. Save & Test → Should show **Data source is working**.

---

## 📈 Importing Dashboards

* Import **Prometheus 2.0 Overview (ID: 3662)** for Prometheus monitoring.
* Create a custom dashboard to monitor your **App metrics**.

---
--

## 🔍 Example PromQL Queries for Grafana

Here are some useful queries to get started 👇

### 🖥️ Node Metrics (via Node Exporter)

* **CPU Usage per Core**
```promql
rate(node_cpu_seconds_total{mode="user"}[5m])
```

* **Memory Usage**
```promql
node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes
```

* **Disk Usage %**
```promql
100 - (node_filesystem_free_bytes{fstype!="tmpfs"} * 100 / node_filesystem_size_bytes{fstype!="tmpfs"})
```

* **Network Traffic (per interface)**
```promql
rate(node_network_receive_bytes_total[5m])
```

### 📦 App Metrics (if app exposes /metrics)

* **Total HTTP Requests**
```promql
sum(rate(http_requests_total[5m]))
```

* **Request Latency**
```promql
histogram_quantile(0.95, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))
```

---

## 🐳 Application Deployment (Docker Hub)

Your app is deployed via `app-deployment.yaml` pulling directly from Docker Hub.
Update the image field if you want to replace with your own app:

```yaml
containers:
  - name: demo-app
    image: <your-dockerhub-username>/<your-app>:latest
    ports:
      - containerPort: 8080
```



📜 License
This project is open-source under the MIT License.








