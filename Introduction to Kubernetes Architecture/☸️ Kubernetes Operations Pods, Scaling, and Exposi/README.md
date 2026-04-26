# ☸️ Kubernetes Operations: Pods, Scaling, and Exposing Services via AWS ELB

**Objective:** Understand and execute the core operational lifecycle of applications within an AWS EKS Kubernetes cluster. This lab covers environment isolation, desired state management, zero-downtime rolling updates, and exposing applications to the internet via an Elastic Load Balancer.

**Prerequisites:**

- An active EKS Cluster (provisioned via `eksctl`).
- `kubectl` configured and connected to the cluster.
- OpenLens installed for visual cluster management.

---

### Phase 1: Environment Isolation (Namespaces)

Namespaces are used to logically isolate cluster resources, ensuring multi-tenant security and preventing naming collisions.

**Command:**

Bash

`kubectl create namespace movieapp-namespace`

**Verification:**

Bash

`kubectl get namespace`

---

### Phase 2: Deploying Standalone Workloads (Pods)

The Pod is the smallest deployable unit in Kubernetes, acting as an envelope for containerized applications.

**1. Create `pod.yaml`:**

YAML

`apiVersion: v1
kind: Pod
metadata:
  name: movieapp-pod
  namespace: movieapp-namespace
  labels:
    app: movieapp
spec:
  containers:
  - name: movieapp-container
    image: nginx:latest
    ports:
    - containerPort: 80`

**2. Deploy and Test:**

Bash

`kubectl apply -f pod.yaml
kubectl get pods -n movieapp-namespace
# Tunnel into the cluster to test locally
kubectl port-forward pod/movieapp-pod 8080:80 -n movieapp-namespace`

---

### Phase 3: High Availability & Auto-Healing (ReplicaSets)

To ensure high availability, a ReplicaSet is used to maintain a strict "desired state" of running pods. If a pod crashes, the ReplicaSet instantly creates a replacement.

**1. Create `replicaset.yaml`:**

YAML

`apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: movieapp-rs
  namespace: movieapp-namespace
spec:
  replicas: 3
  selector:
    matchLabels:
      app: movieapp-web
  template:
    metadata:
      labels:
        app: movieapp-web
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
        ports:
        - containerPort: 80`

**2. Deploy, Scale, and Test Auto-Healing:**

Bash

`kubectl apply -f replicaset.yaml
# Dynamically scale the infrastructure up to 6 pods
kubectl scale rs movieapp-rs --replicas=6 -n movieapp-namespace
# Chaos Test: Delete a pod to watch it instantly resurrect
kubectl delete pod <target-pod-name> -n movieapp-namespace`

**OpenLens showing the running Pods**

![10-k8s.png](%E2%98%B8%EF%B8%8F%20Kubernetes%20Operations%20Pods,%20Scaling,%20and%20Exposi/10-k8s.png)

---

### Phase 4: Zero-Downtime Rolling Updates (Deployments)

Deployments act as the overarching controllers for ReplicaSets. They enable seamless image upgrades without application downtime.

**1. Create `deploy.yaml`:**

YAML

`apiVersion: apps/v1
kind: Deployment
metadata:
  name: movieapp-deployment
  namespace: movieapp-namespace
spec:
  replicas: 3
  selector:
    matchLabels:
      app: movieapp-frontend
  template:
    metadata:
      labels:
        app: movieapp-frontend
    spec:
      containers:
      - name: frontend-container
        image: nginx:1.25  # Starting version
        ports:
        - containerPort: 80`

**2. Execute a Rolling Update and Rollback:**

Bash

`kubectl apply -f deploy.yaml
# Upgrade the live application to version 1.26
kubectl set image deployment/movieapp-deployment frontend-container=nginx:1.26 -n movieapp-namespace
# Watch the update rollout live
kubectl rollout status deployment/movieapp-deployment -n movieapp-namespace
# Revert immediately to the previous version in case of failure
kubectl rollout undo deployment/movieapp-deployment -n movieapp-namespace`

---

### Phase 5: External Networking (LoadBalancer Service)

Pods are ephemeral and their internal IP addresses constantly change. A Service provides a permanent entry point. Using the `LoadBalancer` type triggers AWS to provision a physical Elastic Load Balancer (ELB) to route public internet traffic into the cluster.

**1. Create `service.yaml`:**

YAML

`apiVersion: v1
kind: Service
metadata:
  name: movieapp-service
  namespace: movieapp-namespace
spec:
  type: LoadBalancer
  selector:
    app: movieapp-frontend # Must perfectly match the Deployment pod labels
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80`

**2. Deploy and Expose:**

Bash

`kubectl apply -f service.yaml
# Retrieve the AWS ELB Public DNS
kubectl get svc -n movieapp-namespace`

**Terminal output showing the EXTERNAL-IP:**

![09-k8s.png](%E2%98%B8%EF%B8%8F%20Kubernetes%20Operations%20Pods,%20Scaling,%20and%20Exposi/09-k8s.png)

 **OpenLens Network tab showing the movieapp-service LoadBalancer:**

![12-k8s.png](%E2%98%B8%EF%B8%8F%20Kubernetes%20Operations%20Pods,%20Scaling,%20and%20Exposi/12-k8s.png)

---

### Web browser displaying the live application via the AWS URL

![08-k8s.png](%E2%98%B8%EF%B8%8F%20Kubernetes%20Operations%20Pods,%20Scaling,%20and%20Exposi/08-k8s.png)

### Phase 6: Infrastructure Teardown

To prevent ongoing cloud computing costs, the entire EKS cluster and its associated resources (EC2, VPC, ELB) must be destroyed when operations are complete.

**Command:**

Bash

`eksctl delete cluster --name movie-cluster --region us-east-1`