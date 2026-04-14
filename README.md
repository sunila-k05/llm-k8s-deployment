#  Deploying an Open Source LLM on Kubernetes (Ollama + Mistral)

This project demonstrates how to deploy an open-source Large Language Model (LLM) inside a Kubernetes cluster, monitor it using Prometheus and Grafana, and interact with it via a simple chatbot UI.

---

##  Tech Stack

 Kubernetes (Minikube)
 Ollama (Mistral model)
 Prometheus + Grafana (Monitoring)
 HTML + JavaScript (Chatbot UI)

---

##  Architecture

User → Chat UI → Ollama API → Kubernetes Pod → LLM

---

##  Project Structure

```
llm-k8s-deployment/
│
├── k8s/
│   └── ollama.yaml        # Kubernetes deployment + service
│
├── ui/
│   └── index.html         # Simple chatbot UI
│
├── docs/                  # Screenshots (optional)
│
└── README.md
```

---

##  Setup Instructions

### 1. Start Kubernetes Cluster

```bash
minikube start --cpus 3 --memory 4096
```

---

### 2. Deploy Ollama in Kubernetes

```bash
kubectl create namespace ollama
kubectl apply -f k8s/ollama.yaml
```

Check pods:

```bash
kubectl get pods -n ollama
```

---

### 3. Load Model inside Pod

```bash
POD=$(kubectl get pods -n ollama -l app=ollama -o jsonpath='{.items[0].metadata.name}')

kubectl exec -it $POD -n ollama -- ollama pull mistral
```

---

### 4. Expose API

```bash
kubectl port-forward svc/ollama 11434:11434 -n ollama
```

Test API:

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "mistral",
  "prompt": "Which model are you?",
  "stream": false
}'
```

---

### 5. Run Chatbot UI

```bash
cd ui
python3 -m http.server 8080
```

Open in browser:

```
http://localhost:8080
```

---

##  Monitoring (Prometheus + Grafana)

Install monitoring stack:

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

helm install monitoring prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --create-namespace
```

Access Grafana:

```bash
kubectl port-forward svc/monitoring-grafana 3000:80 -n monitoring
```

Open:

```
http://localhost:3000
```

Default login:

* Username: admin
* Password: (retrieve via Kubernetes secret or reset)

---

## 📈 What is Monitored

* Node CPU usage
* Memory usage
* Pod health and status
![Grafana Dashboard](docs/grafana-dashboard.png) 

---

##  Challenges Faced

* Model not detected via API initially
* Kubernetes resource constraints (memory limits)
* Model persistence issues inside pods
* Debugging API endpoints (`/api/generate` vs `/api/chat`)

---

##  Demo Video

soon..

---

##  Blog

soon..

---

##  Key Learnings

 Deploying LLM workloads in Kubernetes
 Managing containerized AI models
 Monitoring infrastructure using Prometheus and Grafana
 Debugging real-world DevOps issues

---

##  Conclusion

This project shows how open source LLMs can be deployed and managed efficiently using Kubernetes, with observability and a simple UI for interaction.
