# DevOps Pipeline Demo

## Purpose
Demo project to showcase CI/CD with Docker, Kubernetes, and Terraform.

## Tech Stack
- Azure
- Docker
- Kubernetes
- Terraform
- Ansible
- Python Flask

---

## 📅 Day 1 – Flask App & Dockerization

### Progress
- ✅ Base Flask app created
- ✅ Dockerized locally

### Notes / Challenges
- Had to fix Docker permissions (`docker` group issue).
- Next step: Set up Kubernetes deployment (AKS) using Terraform.

---

## 📅 Day 2 – Deploying Flask App to Kubernetes with Minikube

### 1. Minikube Installation

#### Install `kubectl`
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
kubectl version --client
```

#### Install Minikube
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube version
```

#### Start Minikube
```bash
minikube start
kubectl get nodes
```

---

### 2. Build Docker Image in Minikube’s Environment
```bash
eval $(minikube docker-env)
docker build -t flask-devops-demo:latest .
docker images | grep flask-devops-demo
```

---

### 3. Deployment Manifest (`deployment.yaml`)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-demo
spec:
  replicas: 2
  selector:
    matchLabels:
      app: flask-demo
  template:
    metadata:
      labels:
        app: flask-demo
    spec:
      containers:
      - name: flask-demo
        image: flask-devops-demo:latest
        imagePullPolicy: Never
        ports:
        - containerPort: 5000
```

---

### 4. Service Manifest (`service.yaml`)
```yaml
apiVersion: v1
kind: Service
metadata:
  name: flask-demo-service
spec:
  type: NodePort
  selector:
    app: flask-demo
  ports:
  - port: 5000
    targetPort: 5000
    nodePort: 30001
```

---

### 5. Deploy & Access the Application
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

kubectl get pods -o wide
kubectl get svc

minikube service flask-demo-service
```


---

### 6. Scaling the Application (Bonus)
```bash
kubectl scale deployment flask-demo --replicas=5
kubectl get pods
```

---

## Summary
- **Deployment**: Manages pod replicas and ensures desired state.
- **Service**: Provides stable networking to pods, even if pod IPs change.
- **Minikube**: Runs Kubernetes locally for development and testing.
