# Complete Kubernetes Guide - Commands and Concepts

A comprehensive guide to Kubernetes (K8s) container orchestration platform, covering core concepts, CLI commands, deployment strategies, and best practices for managing containerized applications at scale.

## Table of Contents
1. [What is Kubernetes?](#what-is-kubernetes)
2. [Core Kubernetes Concepts](#core-kubernetes-concepts)
3. [Kubernetes Architecture](#kubernetes-architecture)
4. [Installation and Setup](#installation-and-setup)
5. [Essential kubectl Commands](#essential-kubectl-commands)
6. [Working with YAML Manifests](#working-with-yaml-manifests)
7. [Networking and Services](#networking-and-services)
8. [Storage and Volumes](#storage-and-volumes)
9. [Configuration Management](#configuration-management)
10. [Scaling and Updates](#scaling-and-updates)
11. [Monitoring and Debugging](#monitoring-and-debugging)
12. [Advanced Topics](#advanced-topics)
13. [Best Practices](#best-practices)
14. [Common Workflows](#common-workflows)

---

## What is Kubernetes?

Kubernetes (K8s) is an **open-source container orchestration platform** that automates the deployment, scaling, and management of containerized applications across a cluster of machines.

### Why Kubernetes is Necessary

**The Problem:**
- Single containers can't handle increasing traffic (CPU/memory bottlenecks)
- Single point of failure - if one container fails, entire application goes down
- Manual scaling and management becomes complex
- No built-in load balancing or service discovery

**The Solution:**
- **Multiple replicas** of applications for high availability
- **Automatic scaling** based on demand
- **Self-healing** - replaces failed containers automatically
- **Load balancing** and service discovery
- **Rolling updates** without downtime

### How Kubernetes Works
- **Docker** provides individual containers that package your applications
- **Kubernetes** decides how, where, and when these containers run
- **Declarative approach** - you describe desired state using YAML files
- **Kubernetes maintains** that state automatically

---

## Core Kubernetes Concepts

### 1. Cluster
A **group of physical or virtual machines** working as a single system.

**Components:**
- **Control Plane**: Makes decisions, schedules pods, reconciles states, monitors health
- **Worker Nodes**: Machines where containers actually run

### 2. Pods
The **smallest deployable unit** in Kubernetes:
- Wraps one or more containers
- Each pod gets its own IP address
- Containers in a pod share storage and network
- Ephemeral - pods can be created and destroyed

### 3. ReplicaSet
Ensures a **specified number of identical pods** are always running:
- If a pod dies, automatically spins up a new one
- Maintains desired state
- Handles pod lifecycle management

### 4. Deployment
A **higher-level object** that manages ReplicaSets:
- Enables rolling updates
- Gradually replaces old pods with new ones
- Zero-downtime deployments
- Rollback capabilities

### 5. Service
A **stable network endpoint** that routes traffic to available pods:
- Permanent IP or DNS name
- Load balances requests across multiple replicas
- Abstracts away pod temporary nature
- Service discovery mechanism

### 6. ConfigMaps and Secrets
- **ConfigMaps**: Store non-sensitive configuration data (database URLs, settings)
- **Secrets**: Store sensitive data (passwords, API keys, certificates)
- Both are securely injected into pods

### 7. Ingress
Acts as a **smart router**:
- Exposes HTTP/HTTPS routes from outside cluster
- Routes to internal services
- SSL termination
- Load balancing

### 8. Volumes
Provide **persistent storage**:
- Data survives pod restarts
- Can be shared between pods
- Various storage backends supported

---

## Kubernetes Architecture

### Control Plane Components
```
┌─────────────────────────────────────────┐
│              Control Plane              │
├─────────────────────────────────────────┤
│  API Server  │  etcd  │  Scheduler     │
│  Controller Manager   │  Cloud Manager │
└─────────────────────────────────────────┘
```

### Worker Node Components
```
┌─────────────────────────────────────────┐
│              Worker Node                │
├─────────────────────────────────────────┤
│  Kubelet  │  Container Runtime  │ Proxy │
│           │     (Docker/containerd)      │
└─────────────────────────────────────────┘
```

---

## Installation and Setup

### Installing kubectl (Kubernetes CLI)

#### macOS (Apple Silicon)
```bash
# Download kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/arm64/kubectl"

# Make executable
chmod +x kubectl

# Move to PATH
sudo mv kubectl /usr/local/bin/

# Verify installation
kubectl version --client
```

#### macOS (Intel)
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

#### Linux
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

#### Windows
```bash
curl -LO "https://dl.k8s.io/release/v1.28.0/bin/windows/amd64/kubectl.exe"
```

### Installing Minikube (Local Development)

#### macOS
```bash
# Using Homebrew
brew install minikube

# Or direct download
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-arm64
sudo install minikube-darwin-arm64 /usr/local/bin/minikube
```

#### Linux
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

#### Verify Installation
```bash
minikube version
```

### Alternative Local Development Options
```bash
# Kind (Kubernetes in Docker)
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind

# K3s (Lightweight Kubernetes)
curl -sfL https://get.k3s.io | sh -
```

---

## Essential kubectl Commands

### Cluster Management
```bash
# Start local Minikube cluster
minikube start

# Start with specific resources
minikube start --memory=4096 --cpus=2

# Start with specific Kubernetes version
minikube start --kubernetes-version=v1.28.0

# Stop Minikube cluster
minikube stop

# Delete Minikube cluster
minikube delete

# Get cluster information
kubectl cluster-info
kubectl cluster-info dump

# Check cluster nodes
kubectl get nodes
kubectl get nodes -o wide

# Check cluster status
kubectl get componentstatuses
```

### Working with Resources

#### Viewing Resources
```bash
# List all pods
kubectl get pods
kubectl get po  # Short form

# List pods with more details
kubectl get pods -o wide

# List pods in all namespaces
kubectl get pods --all-namespaces
kubectl get pods -A

# Watch pods in real-time
kubectl get pods -w
kubectl get pods --watch

# List other resources
kubectl get services
kubectl get deployments
kubectl get replicasets
kubectl get configmaps
kubectl get secrets
kubectl get ingress
kubectl get pv,pvc  # Persistent volumes and claims
```

#### Describing Resources
```bash
# Get detailed information about a pod
kubectl describe pod <pod-name>

# Describe other resources
kubectl describe service <service-name>
kubectl describe deployment <deployment-name>
kubectl describe node <node-name>
```

#### Creating and Managing Resources
```bash
# Apply configuration from file
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

# Apply all files in directory
kubectl apply -f k8s/
kubectl apply -f . --recursive

# Create resource imperatively
kubectl create deployment nginx --image=nginx
kubectl create service clusterip my-service --tcp=80:80

# Delete resources
kubectl delete -f deployment.yaml
kubectl delete deployment <deployment-name>
kubectl delete pod <pod-name>

# Delete all resources in namespace
kubectl delete all --all
```

---

## Working with YAML Manifests

### Pod Manifest
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  labels:
    app: my-app
spec:
  containers:
  - name: my-container
    image: nginx:1.21
    ports:
    - containerPort: 80
    env:
    - name: ENV_VAR
      value: "production"
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

### Deployment Manifest
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
  labels:
    app: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: my-app:latest
        ports:
        - containerPort: 8080
        env:
        - name: DATABASE_URL
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: database-url
        - name: API_KEY
          valueFrom:
            secretKeyRef:
              name: app-secrets
              key: api-key
```

### Service Manifest
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
  type: ClusterIP  # ClusterIP, NodePort, LoadBalancer
```

### ConfigMap Manifest
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  database-url: "postgresql://db:5432/myapp"
  log-level: "info"
  config.properties: |
    property1=value1
    property2=value2
```

### Secret Manifest
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secrets
type: Opaque
data:
  api-key: YWJjZGVmZ2hpams=  # base64 encoded
  password: cGFzc3dvcmQ=     # base64 encoded
```

---

## Networking and Services

### Service Types
```bash
# ClusterIP (default) - Internal cluster access only
kubectl expose deployment my-app --port=80 --target-port=8080

# NodePort - Access via node IP:port
kubectl expose deployment my-app --type=NodePort --port=80

# LoadBalancer - External load balancer (cloud providers)
kubectl expose deployment my-app --type=LoadBalancer --port=80

# Access service in Minikube
minikube service my-app-service
minikube service my-app-service --url
```

### Ingress Configuration
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-app-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: my-app.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-app-service
            port:
              number: 80
```

### Network Policies
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
```

---

## Storage and Volumes

### Persistent Volume (PV)
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
  - ReadWriteOnce
  hostPath:
    path: /data/my-app
```

### Persistent Volume Claim (PVC)
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

### Using Volumes in Pods
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: nginx
    volumeMounts:
    - name: my-storage
      mountPath: /data
  volumes:
  - name: my-storage
    persistentVolumeClaim:
      claimName: my-pvc
```

### Storage Commands
```bash
# List persistent volumes
kubectl get pv

# List persistent volume claims
kubectl get pvc

# Describe storage
kubectl describe pv my-pv
kubectl describe pvc my-pvc
```

---

## Configuration Management

### ConfigMaps
```bash
# Create ConfigMap from literal values
kubectl create configmap app-config --from-literal=key1=value1 --from-literal=key2=value2

# Create ConfigMap from file
kubectl create configmap app-config --from-file=config.properties

# Create ConfigMap from directory
kubectl create configmap app-config --from-file=config/

# Get ConfigMap
kubectl get configmap
kubectl describe configmap app-config

# Edit ConfigMap
kubectl edit configmap app-config
```

### Secrets
```bash
# Create Secret from literal values
kubectl create secret generic app-secrets --from-literal=username=admin --from-literal=password=secret

# Create Secret from files
kubectl create secret generic app-secrets --from-file=username.txt --from-file=password.txt

# Create Docker registry secret
kubectl create secret docker-registry regcred \
  --docker-server=docker.io \
  --docker-username=myuser \
  --docker-password=mypass \
  --docker-email=myemail@example.com

# Get Secrets
kubectl get secrets
kubectl describe secret app-secrets

# Decode secret values
kubectl get secret app-secrets -o jsonpath='{.data.password}' | base64 --decode
```

---

## Scaling and Updates

### Manual Scaling
```bash
# Scale deployment
kubectl scale deployment my-app --replicas=5

# Scale ReplicaSet
kubectl scale rs my-replicaset --replicas=3

# Auto-scaling (Horizontal Pod Autoscaler)
kubectl autoscale deployment my-app --min=2 --max=10 --cpu-percent=80
```

### Rolling Updates
```bash
# Update deployment image
kubectl set image deployment/my-app container-name=new-image:tag

# Update with new image
kubectl set image deployment/my-app my-app=my-app:v2

# Check rollout status
kubectl rollout status deployment/my-app

# Check rollout history
kubectl rollout history deployment/my-app

# Rollback to previous version
kubectl rollout undo deployment/my-app

# Rollback to specific revision
kubectl rollout undo deployment/my-app --to-revision=2

# Pause rollout
kubectl rollout pause deployment/my-app

# Resume rollout
kubectl rollout resume deployment/my-app
```

### Horizontal Pod Autoscaler (HPA)
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

---

## Monitoring and Debugging

### Pod Logs and Debugging
```bash
# View pod logs
kubectl logs <pod-name>
kubectl logs <pod-name> -c <container-name>  # Multi-container pod

# Follow logs in real-time
kubectl logs -f <pod-name>

# Get logs from previous container instance
kubectl logs <pod-name> --previous

# Get logs from all containers in pod
kubectl logs <pod-name> --all-containers

# Execute commands in pod
kubectl exec -it <pod-name> -- /bin/bash
kubectl exec -it <pod-name> -c <container-name> -- /bin/sh

# Copy files to/from pod
kubectl cp <pod-name>:/path/to/file ./local-file
kubectl cp ./local-file <pod-name>:/path/to/file

# Port forwarding for debugging
kubectl port-forward <pod-name> 8080:80
kubectl port-forward service/<service-name> 8080:80
```

### Resource Usage and Events
```bash
# Check resource usage
kubectl top nodes
kubectl top pods
kubectl top pods --containers

# View cluster events
kubectl get events
kubectl get events --sort-by='.metadata.creationTimestamp'

# Check resource quotas
kubectl get resourcequota
kubectl describe resourcequota

# Check limits
kubectl get limitrange
kubectl describe limitrange
```

---

## Advanced Topics

### Namespaces
```bash
# Create namespace
kubectl create namespace production
kubectl create ns dev  # Short form

# List namespaces
kubectl get namespaces
kubectl get ns

# Set default namespace
kubectl config set-context --current --namespace=production

# Delete namespace
kubectl delete namespace dev

# Work with specific namespace
kubectl get pods -n production
kubectl apply -f deployment.yaml -n production
```

### Labels and Selectors
```bash
# Add labels to resources
kubectl label pod my-pod environment=production
kubectl label pod my-pod version=v1.0

# Remove labels
kubectl label pod my-pod environment-

# Select resources by labels
kubectl get pods -l environment=production
kubectl get pods -l 'environment in (production,staging)'
kubectl get pods -l environment=production,version=v1.0

# Show labels
kubectl get pods --show-labels
```

### Jobs and CronJobs
```yaml
# Job
apiVersion: batch/v1
kind: Job
metadata:
  name: my-job
spec:
  template:
    spec:
      containers:
      - name: my-job
        image: busybox
        command: ["echo", "Hello World"]
      restartPolicy: Never
  backoffLimit: 4

# CronJob
apiVersion: batch/v1
kind: CronJob
metadata:
  name: my-cronjob
spec:
  schedule: "0 2 * * *"  # Daily at 2 AM
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: my-cronjob
            image: busybox
            command: ["echo", "Scheduled task"]
          restartPolicy: Never
```

---

## Best Practices

### Resource Management
```yaml
# Always set resource requests and limits
resources:
  requests:
    memory: "64Mi"
    cpu: "250m"
  limits:
    memory: "128Mi"
    cpu: "500m"
```

### Health Checks
```yaml
# Liveness and readiness probes
livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 30
  periodSeconds: 10

readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 5
```

### Security Best Practices
```yaml
# Security context
securityContext:
  runAsNonRoot: true
  runAsUser: 1000
  allowPrivilegeEscalation: false
  readOnlyRootFilesystem: true
  capabilities:
    drop:
    - ALL
```

---

## Common Workflows

### Development Workflow
```bash
# 1. Prepare Docker image
docker build -t my-app:latest .
docker tag my-app:latest username/my-app:latest
docker push username/my-app:latest

# 2. Deploy to Kubernetes
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml

# 3. Check deployment
kubectl get pods -w
kubectl get services

# 4. Access application
minikube service my-app-service
```

### Automated Deployment Script
```bash
#!/bin/bash
# deploy.sh

set -e

IMAGE="username/my-app:latest"
NAME="my-app"

echo "Building Docker image..."
docker build -t $IMAGE .

echo "Pushing to Docker Hub..."
docker push $IMAGE

echo "Deploying to Kubernetes..."
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml

echo "Checking deployment status..."
kubectl get pods
kubectl get services

echo "Getting service URL..."
minikube service $NAME-service --url

echo "Deployment completed successfully!"
```

### Production Deployment Checklist
```bash
# 1. Resource limits set
kubectl describe pod <pod-name> | grep -A 5 "Limits"

# 2. Health checks configured
kubectl describe pod <pod-name> | grep -A 5 "Liveness"

# 3. Security context applied
kubectl describe pod <pod-name> | grep -A 10 "Security Context"

# 4. Monitoring and logging
kubectl logs <pod-name>
kubectl top pod <pod-name>

# 5. Backup and disaster recovery
kubectl get all -o yaml > backup.yaml
```

---

## Quick Reference

### Essential Commands
| Command | Description |
|---------|-------------|
| `kubectl get pods` | List all pods |
| `kubectl apply -f file.yaml` | Apply configuration |
| `kubectl describe pod <name>` | Get detailed pod info |
| `kubectl logs <pod-name>` | View pod logs |
| `kubectl exec -it <pod> -- bash` | Shell into pod |
| `kubectl scale deployment <name> --replicas=3` | Scale deployment |
| `kubectl delete pod <name>` | Delete pod |
| `kubectl get services` | List services |
| `minikube service <name>` | Access service in Minikube |
| `kubectl rollout restart deployment/<name>` | Restart deployment |

### Resource Short Names
| Resource | Short Name |
|----------|------------|
| pods | po |
| services | svc |
| deployments | deploy |
| replicasets | rs |
| namespaces | ns |
| configmaps | cm |
| persistentvolumes | pv |
| persistentvolumeclaims | pvc |

### Common Flags
| Flag | Description |
|------|-------------|
| `-n <namespace>` | Specify namespace |
| `-o wide` | Show more columns |
| `-o yaml` | Output in YAML format |
| `-w, --watch` | Watch for changes |
| `--dry-run=client` | Preview without applying |
| `-f <file>` | Specify file |

Kubernetes provides powerful orchestration capabilities for containerized applications, enabling scalable, resilient, and manageable deployments across distributed systems!