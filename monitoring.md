To install monitoring for your Kubernetes cluster, the most popular and production-ready stack is **Prometheus + Grafana**, typically deployed using the **Kube-Prometheus Stack** from the [Prometheus Community Helm chart](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack).

---

## ✅ Step-by-Step: Install Monitoring with Prometheus + Grafana via Helm

### 🧩 Prerequisites

* Helm installed (`helm version`)
* `kubectl` configured to point to your cluster
* Optionally: create a namespace like `monitoring`

```bash
kubectl create namespace monitoring
```

---

### 🚀 Step 1: Add Prometheus Community Helm Repo

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

---

### 🚀 Step 2: Install the Kube Prometheus Stack

```bash
helm install kube-prometheus-stack prometheus-community/kube-prometheus-stack \
  --namespace monitoring
```

This will install:

* Prometheus
* Grafana
* Node Exporter
* Alertmanager
* kube-state-metrics

---

### 🔍 Step 3: Access Grafana

You can port forward to access Grafana locally:

```bash
kubectl port-forward svc/kube-prometheus-stack-grafana -n monitoring 3000:80
```

Then go to [http://localhost:3000](http://localhost:3000)

Default credentials:

* Username: `admin`
* Password: (run below to get it)

```bash
kubectl get secret --namespace monitoring kube-prometheus-stack-grafana -o jsonpath="{.data.admin-password}" | base64 -d
```

---

### 📈 Step 4: Add Dashboards (Optional)

Grafana comes preconfigured with some dashboards. You can import more dashboards using their IDs from [https://grafana.com/grafana/dashboards](https://grafana.com/grafana/dashboards).

---

### 🛡️ Bonus: Expose Grafana or Prometheus with an Ingress (optional)

If you want to access these services via domain:

Create an Ingress route and TLS cert, or expose the service with a `LoadBalancer`:

```yaml
kubectl patch svc kube-prometheus-stack-grafana -n monitoring \
  -p '{"spec": {"type": "LoadBalancer"}}'
```

---


