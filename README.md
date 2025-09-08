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
git clone https://github.com/<your-username>/Kubernetes-Monitoring-Stack-with-Prometheus-Grafana.git
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
  1. Go to **Status → Target Health** to view the connected services' health status.

---

## 🔗 Connecting Prometheus with Grafana

1. Open Grafana [http://localhost:3000](http://localhost:3000) → **Configuration → Data Sources → Add data source**
2. Select **Prometheus**
3. Enter Prometheus URL:
   ```
   http://prometheus:9090
   ```
4. Click **Save & Test** → Should show **Data source is working**

---

## 🔎 How to See Node Exporter Data Yourself

If you want to check the raw metrics manually:

### Option 1: Port Forward (best for Docker Desktop)
```bash
kubectl port-forward svc/node-exporter 9100:9100
```
Now open → [http://localhost:9100/metrics](http://localhost:9100/metrics)
👉 You’ll see plain text metrics like `node_cpu_seconds_total`, `node_memory_MemAvailable_bytes`, etc.

---

### Option 2: Open Grafana

Grafana has prebuilt dashboards for Node Exporter:

1. On dashboard left menu → **Create → Import**
2. **Option 1: Dashboard ID** → enter `1860` (Node Exporter Full)
3. **Option 2: Upload JSON** → you can download JSON from [Grafana Dashboards](https://grafana.com/grafana/dashboards/1860)
4. Click **Load** → select your Prometheus data source → **Import**

---

## Explore Metrics

Your Grafana dashboard will show:

* CPU usage (`node_cpu_seconds_total`)
* Memory usage (`node_memory_*`)
* Disk usage (`node_filesystem_*`)
* Network traffic (`node_network_*`)

You can hover, zoom in/out, and set refresh intervals (e.g., 5s, 10s).

---

## Optional – Custom Panels

1. Click **+ → Dashboard → Add new panel**
2. Select **Prometheus** as data source
3. Enter a metric query, e.g.:

   ```promql
   node_cpu_seconds_total{mode="idle"}
   ```

4. Choose visualization type: graph, gauge, table, etc.
5. Click **Apply**

---

## 🔍 Example PromQL Queries for Grafana

Here are some useful queries to get started 👇

---

# 🔍 Prometheus Metrics in Your App

From your Flask app, you expose these Prometheus metrics:

* `http_requests_total{method, endpoint, http_status}` → counter
* `http_request_duration_seconds{endpoint}` → histogram
* `random_value` → gauge
* `users_signed_up_total` → counter
* `background_tasks_total` → counter

---

# 📊 Grafana Queries to Try

### 1️⃣ Requests per second (traffic load)
```promql
rate(http_requests_total[1m])
```
👉 Shows how many requests per second are being handled, grouped by method, endpoint, and status code.
*Useful for a traffic dashboard — simulate load when refreshing tabs.*

---

### 2️⃣ Requests split by endpoint
```promql
sum by (endpoint) (rate(http_requests_total[1m]))
```
👉 Compares which endpoints (`/`, `/signup`, `/healthz`) are hit most often.
*Useful to see popular features in the app.*

---

### 3️⃣ Latency distribution
```promql
histogram_quantile(0.95, sum(rate(http_request_duration_seconds_bucket[5m])) by (le, endpoint))
```
👉 Shows **95th percentile latency per endpoint**.
*Replace `0.95` with `0.5` for median, `0.99` for tail latency.*

---

### 4️⃣ User signups over time
```promql
increase(users_signed_up_total[5m])
```
👉 Number of new signups in the last 5 minutes.
*Useful to see effect of clicking "Get Started" button.*

---

### 5️⃣ Total signups
```promql
users_signed_up_total
```
👉 Shows the total counter value of signups so far.

---

### 6️⃣ Background tasks executed
```promql
rate(background_tasks_total[1m])
```
👉 Shows how often background jobs are firing (you have one every 5 seconds).

---

### 7️⃣ System random gauge (live changing value)
```promql
random_value
```
👉 Current gauge value (updates every 5 seconds).
*Great for live gauge panels in Grafana.*

---

# 🧪 How to Generate Data (Testing Tips)

### 1️⃣ Open app in multiple tabs
* Open `/` and `/dashboard` in **3–5 browser tabs**
* Each refresh generates `http_requests_total` metrics

---

### 2️⃣ Simulate signups
* Click **“Get Started”** button multiple times
* This increments `users_signed_up_total`
* Grafana graph of `increase(users_signed_up_total[5m])` will spike

---

### 3️⃣ Background job
* No action required — every 5 seconds, `background_tasks_total` and `random_value` update automatically

---

### 4️⃣ Simulate traffic load
```bash
while true; do curl -s http://<your-service-ip>:5000/ > /dev/null; done
```
👉 `rate(http_requests_total[1m])` will spike in Grafana

---

### 5️⃣ Auto refresh in Grafana

* In Grafana dashboard, set **Refresh Interval = 5s**
* Open multiple charts (requests/sec, latency, signups, gauge)
* Watch them update live as you interact with the app

---

## 🐳 Application Deployment (Docker Hub)

Your app is deployed via `app-deployment.yaml` pulling directly from Docker Hub.
Update the image field if you want to replace it with your own app:

```yaml
containers:
  - name: demo-app
    image: <your-dockerhub-username>/<your-app>:latest
    ports:
      - containerPort: 5000
```

---

📜 **License**
This project is open-source any one can use my project.



