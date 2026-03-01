# CLO835 – Assignment 2  
## Kubernetes Deployment on AWS EC2 using kind

---

## 📌 Overview

This project demonstrates the deployment of a containerized Flask web application and MySQL database on a Kubernetes cluster created using **kind** (Kubernetes-in-Docker) on an Amazon Linux EC2 instance.

The project fulfills the following objectives:

- Deploy Amazon Linux EC2
- Install Docker, kubectl, and kind
- Create Kubernetes cluster using kind
- Deploy Pod, ReplicaSet, Deployment, and Service manifests
- Expose web application using NodePort
- Expose MySQL using ClusterIP
- Demonstrate scaling
- Perform rolling update of application

---

# 🖥 Step 1 – EC2 Setup

An Amazon Linux EC2 instance was provisioned.

### Install Docker

```bash
sudo yum update -y
sudo yum install docker -y
sudo systemctl start docker
sudo usermod -aG docker ec2-user
```

### Install kubectl

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

### Install kind

```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/latest/kind-linux-amd64
chmod +x kind
sudo mv kind /usr/local/bin/
```

---

# ☸️ Step 2 – Create Kubernetes Cluster

```bash
kind create cluster --name clo835
```

Verify cluster:

```bash
kubectl get nodes
kubectl get pods -A
```

---

# 📁 Step 3 – Create Namespaces

```bash
kubectl create namespace mysql
kubectl create namespace employees
```

---

# 🐬 Step 4 – Deploy MySQL (ClusterIP Service)

Apply MySQL Deployment:

```bash
kubectl apply -f mysql/mysql-deployment.yaml
```

Apply MySQL Service:

```bash
kubectl apply -f mysql/mysql-service.yaml
```

Verify:

```bash
kubectl get pods -n mysql
kubectl get svc -n mysql
```

MySQL is exposed internally using:

- **Service Type:** ClusterIP  
- **Port:** 3306  

This allows only internal cluster communication.

---

# 🌐 Step 5 – Deploy Employees Web Application

## Build Docker Image

```bash
docker build -t employees:v1 .
kind load docker-image employees:v1 --name clo835
```

## Apply Deployment

```bash
kubectl apply -f employees/employees-deployment.yaml
```

## Apply NodePort Service

```bash
kubectl apply -f employees/employees-service.yaml
```

Verify:

```bash
kubectl get pods -n employees
kubectl get svc -n employees
```

Employees Service is exposed using:

- **Service Type:** NodePort  
- **NodePort:** 30000  
- **Target Port:** 8080  

---

# 🔌 Step 6 – Access the Application

Since kind runs inside Docker, access is demonstrated using port-forward:

```bash
kubectl port-forward -n employees svc/employees-service 8080:8080 --address 0.0.0.0
```

Open in browser:

```
http://<EC2_PUBLIC_IP>:8080
```

The application connects internally to MySQL via:

```
mysql-service.mysql.svc.cluster.local
```

---

# 📈 Step 7 – Scaling Demonstration

Scale up:

```bash
kubectl scale deployment employees-deployment --replicas=5 -n employees
kubectl get pods -n employees
```

Scale down:

```bash
kubectl scale deployment employees-deployment --replicas=3 -n employees
kubectl get pods -n employees
```

This demonstrates horizontal scaling capability.

---

# 🔄 Step 8 – Rolling Update

Build new image version:

```bash
docker build -t employees:v2 .
kind load docker-image employees:v2 --name clo835
```

Trigger rolling update:

```bash
kubectl set image deployment/employees-deployment employees=employees:v2 -n employees
kubectl rollout status deployment/employees-deployment -n employees
kubectl rollout history deployment/employees-deployment -n employees
```

Verify updated pods:

```bash
kubectl get pods -n employees
```

This demonstrates zero-downtime updates.

---

# 📦 Kubernetes Objects Used

| Object | Purpose |
|---------|----------|
| Pod | Basic deployable unit |
| ReplicaSet | Maintains specified number of pod replicas |
| Deployment | Manages ReplicaSets and rolling updates |
| Service (NodePort) | Exposes web application externally |
| Service (ClusterIP) | Enables internal MySQL communication |

---

# 📂 Repository Structure

```
clo835-assignment2/
│
├── employees/
│   ├── employees-pod.yaml
│   ├── employees-rs.yaml
│   ├── employees-deployment.yaml
│   ├── employees-service.yaml
│
├── mysql/
│   ├── mysql-pod.yaml
│   ├── mysql-deployment.yaml
│   ├── mysql-service.yaml
│
└── README.md
```

---

# 🧹 Cleanup Commands (Optional)

```bash
kubectl delete ns employees mysql
kind delete cluster --name clo835
```

---

# 🛠 Technologies Used

- AWS EC2 (Amazon Linux)
- Docker
- Kubernetes
- kind
- kubectl
- Flask
- MySQL

---

# ✅ Assignment Requirements Covered

✔ EC2 Deployment  
✔ Kubernetes cluster using kind  
✔ Pod, ReplicaSet, Deployment manifests  
✔ NodePort service for web application  
✔ ClusterIP service for MySQL  
✔ Scaling demonstration  
✔ Rolling update implementation  

---

## 👤 Author

Maharshi Hitesh Mehta  
Student ID: 101563187  
Course: CLO835  

