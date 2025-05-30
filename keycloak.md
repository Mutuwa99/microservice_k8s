
---

### ✅ Prerequisites

1. **Minikube** installed and running:

   ```bash
   minikube start
   ```

2. **kubectl** configured to use your Minikube cluster.

3. Optional but useful: **Helm** installed.

---

## Option 1: Using Helm (Easiest)

We'll use the **Bitnami Helm charts**.

### Step 1: Add Bitnami Repo

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

### Step 2: Create a namespace

```bash
kubectl create namespace keycloak
```

### Step 3: Install Keycloak with PostgreSQL

```bash
helm install keycloak bitnami/keycloak \
  --namespace keycloak \
  --set auth.adminUser=admin \
  --set auth.adminPassword=adminpassword \
  --set postgresql.enabled=true \
  --set postgresql.auth.postgresPassword=postgrespassword \
  --set service.type=LoadBalancer
```

* You can override versions and more configs if needed.

### Step 4: Port-forward to access the Keycloak UI

```bash
kubectl port-forward svc/keycloak 8080:80 -n keycloak
```

Now access **[http://localhost:8080](http://localhost:8080)** and log in with `admin / adminpassword`.

---

## Option 2: Manual Deployment (More Control)

You’ll need:

* A `PostgreSQL` Deployment + Service
* A `Keycloak` Deployment + Service that connects to the database

Would you like a YAML manifest for this manual setup?

---

Let me know if you want:

* Ingress config
* TLS
* External DB (vs internal PostgreSQL)
* Persistent volumes

Or if you'd prefer to use `docker-compose` first for testing.
