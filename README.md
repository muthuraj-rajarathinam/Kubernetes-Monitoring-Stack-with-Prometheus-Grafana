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

Perfect ✅ — let’s turn your **SaaS-style Flask app + Prometheus metrics** into something that looks great inside **Grafana dashboards**.
I’ll give you a **list of useful Grafana queries**, explain what each one does, and also tell you **how to test (refresh/open multiple tabs/etc.)** to generate real data.

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

## 1. **Requests per second (traffic load)**
```promql
rate(http_requests_total[1m])
```
👉 Shows how many requests per second are being handled, grouped by method, endpoint, and status code.
* This gives a **traffic dashboard** — useful for simulating load when you refresh tabs.

---

## 2. **Requests split by endpoint**
```promql
sum by (endpoint) (rate(http_requests_total[1m]))
```
👉 Compares which endpoints (`/`, `/signup`, `/healthz`) are hit most often.
* Useful to see **popular features** in the app.

---

## 3. **Latency distribution**
```promql
histogram_quantile(0.95, sum(rate(http_request_duration_seconds_bucket[5m])) by (le, endpoint))
```
👉 Shows **95th percentile latency per endpoint**.
* Replace `0.95` with `0.5` for median, `0.99` for tail latency.
* This is exactly what real SaaS companies monitor.

---

## 4. **User signups over time**
```promql
increase(users_signed_up_total[5m])
```
👉 Number of new signups in the last 5 minutes.
* Useful to see effect of clicking **"Get Started"** button.

---

## 5. **Total signups**
```promql
users_signed_up_total
```
👉 Just shows the total counter value of signups so far.

---

## 6. **Background tasks executed**
```promql
rate(background_tasks_total[1m])
```
👉 Shows how often background jobs are firing (you have one every 5 seconds).
* You’ll see a steady \~0.2 tasks/sec line.

---

## 7. **System random gauge (live changing value)**
```promql
random_value
```
👉 Current gauge value (updates every 5 seconds).
* Can be plotted as a **live gauge panel** in Grafana.

---

# 🧪 How to Generate Data (Testing Tips)

### 1. **Open app in multiple tabs**
* Open `/` and `/dashboard` in **3–5 browser tabs**.
* Each refresh generates **http\_requests\_total** metrics.

### 2. **Simulate signups**
* Click **“Get Started”** button multiple times.
* This increments `users_signed_up_total`.
* Grafana graph of `increase(users_signed_up_total[5m])` will spike.

### 3. **Background job**
* No need to do anything — every 5 seconds, `background_tasks_total` and `random_value` get updated.
* Great for showing **constant system activity** in Grafana.

### 4. **Simulate traffic load**
Run this in terminal to hit endpoints repeatedly:
```bash
while true; do curl -s http://<your-service-ip>:5000/ > /dev/null; done
```
👉 Now `rate(http_requests_total[1m])` will spike in Grafana.

### 5. **Auto refresh in Grafana**

* In Grafana dashboard, set **Refresh Interval = 5s**.
* Open multiple charts (requests/sec, latency, signups, gauge).
* Watch them update live as you interact with the app.

---


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








