Great! You can run **Apache Kafka** and **Kafka UI** on **Minikube** using **Helm** and the Bitnami charts for Kafka, along with `provectuslabs/kafka-ui` for the web interface.

---

## ✅ What You'll Get

* **Kafka** + **Zookeeper** via Helm
* **Kafka UI** (like Confluent Control Center, but open-source)

---

## 🔧 Step-by-Step Deployment

### 🔹 Step 1: Start Minikube (if not already running)

```bash
minikube start
```

### 🔹 Step 2: Create a Namespace

```bash
kubectl create namespace kafka
```

### 🔹 Step 3: Install Kafka with Helm (includes Zookeeper)

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

helm install kafka bitnami/kafka \
  --namespace kafka \
  --set replicaCount=1 \
  --set zookeeper.replicaCount=1 \
  --set auth.enabled=false \
  --set service.type=ClusterIP \
  --set listeners.client.protocol=PLAINTEXT \
  --set externalAccess.enabled=false \
  --set kafka.advertisedListeners=PLAINTEXT://kafka:9092
```

This disables SASL auth for simplicity. For production, **enable and configure auth**.

### 🔹 Step 4: Port-forward Kafka for external access (optional)

```bash
kubectl port-forward svc/kafka 9092 -n kafka
```

---

## 🧭 Deploy Kafka UI

Kafka UI by **Provectus** is a great open-source tool for monitoring and interacting with Kafka topics.

### 🔹 Step 5: Create a Kafka UI Deployment

Here’s a sample manifest for Kafka UI:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kafka-ui
  namespace: kafka
spec:
  replicas: 1
  selector:
    matchLabels:
      app: kafka-ui
  template:
    metadata:
      labels:
        app: kafka-ui
    spec:
      containers:
        - name: kafka-ui
          image: provectuslabs/kafka-ui:latest
          ports:
            - containerPort: 8080
          env:
            - name: KAFKA_CLUSTERS_0_NAME
              value: "local"
            - name: KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS
              value: "kafka:9092"
---
apiVersion: v1
kind: Service
metadata:
  name: kafka-ui
  namespace: kafka
spec:
  selector:
    app: kafka-ui
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
```

Save that as `kafka-ui.yaml` and run:

```bash
kubectl apply -f kafka-ui.yaml
```

### 🔹 Step 6: Port-forward Kafka UI

```bash
kubectl port-forward svc/kafka-ui 8081:8080 -n kafka
```

Now open [http://localhost:8081](http://localhost:8081) to view Kafka UI.

---

## ✅ Summary

| Component | Access                                         | Notes                            |
| --------- | ---------------------------------------------- | -------------------------------- |
| Keycloak  | [http://localhost:8080](http://localhost:8080) | Helm deploy, uses PostgreSQL     |
| Kafka     | port 9092                                      | Bitnami Helm chart               |
| Kafka UI  | [http://localhost:8081](http://localhost:8081) | Talks to `kafka:9092` internally |

---

Would you like:

* Persistent volumes for Kafka?
* TLS & Ingress setup?
* Helm-based Kafka UI instead of raw YAML?

Let me know and I can generate those too.
