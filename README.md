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

1. Open Grafana [http://localhost:9090](http://localhost:3000) → **Configuration → Data Sources → Add data source**.
2. Select **Prometheus**.
3. Enter Prometheus URL:

   ```
   http://prometheus:9090
   ```
4. Save & Test → Should show **Data source is working**.

---

## 🔎 How to See Node Exporter Data Yourself

If you want to check the raw metrics manually :

### Option 1 : Port Forward (best for Docker Desktop)

```bash
kubectl port-forward svc/node-exporter 9100:9100
```

Now open → [http://localhost:9100/metrics](http://localhost:9100/metrics)
👉 You’ll see plain text metrics like `node_cpu_seconds_total`, `node_memory_MemAvailable_bytes`, etc.

---


## Option 2 : Open Grafana

Grafana has prebuilt dashboards for Node Exporter:

1. On Dashboard left menu → **Create → Import**
2. **Option 1: Dashboard ID** → enter `1860` (Node Exporter Full)
3. **Option 2: Upload JSON** → you can download JSON from [Grafana Dashboards](https://grafana.com/grafana/dashboards/1860)
4. Click **Load** → select your Prometheus data source → **Import**

---

## Explore Metrics

Now your Grafana dashboard will show:

* CPU usage (`node_cpu_seconds_total`)
* Memory usage (`node_memory_*`)
* Disk usage (`node_filesystem_*`)
* Network traffic (`node_network_*`)

You can hover, zoom in/out, and set refresh intervals (e.g., 5s, 10s).

---

## Optional – Custom Panels

You can also create your own panels in Grafana:

1. Click **+ → Dashboard → Add new panel**
2. Select **Prometheus** as data source
3. Enter a metric query, e.g.:

   ```promql
   node_cpu_seconds_total{mode="idle"}
   ```
4. Choose visualization type: graph, gauge, table, etc.
5. Click **Apply**

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








