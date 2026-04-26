# AWS EKS Kubernetes Cluster Provisioning & Scaling

**Objective:** Provision a production-ready, cloud-managed Kubernetes cluster using Infrastructure as Code (IaC) and demonstrate container orchestration through dynamic scaling.

**Tech Stack:** AWS EKS, EC2, `eksctl`, `kubectl`, OpenLens GUI, Nginx.

### 1. Infrastructure as Code (IaC) Configuration

Instead of manually clicking through the AWS console, the cluster was provisioned using `eksctl` with a declarative YAML configuration file. This ensured repeatable, version-controlled infrastructure.

YAML

`apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: sharppay-k8s-cluster
  region: us-east-1

managedNodeGroups:
  - name: standard-workers
    instanceType: t2.medium
    minSize: 2
    maxSize: 3`

### 2. Cluster Provisioning & Verification

The control plane and worker nodes were successfully created in AWS. The connection was verified locally via `kubectl`.

> **Terminal showing `kubectl get nodes` in Ready state**
> 
> 
> ![image.png](AWS%20EKS%20Kubernetes%20Cluster%20Provisioning%20&%20Scaling/image.png)
> 

> **AWS Console showing the EKS Cluster as Active**
> 
> 
> ![image.png](AWS%20EKS%20Kubernetes%20Cluster%20Provisioning%20&%20Scaling/image%201.png)
> 

### 3. Visual Cluster Management (OpenLens)

The `kubeconfig` was securely exported and integrated into OpenLens to provide a visual dashboard of the live AWS infrastructure, bypassing the need to manage everything strictly via CLI.

> **OpenLens Nodes screen showing the EC2 instances**
> 
> 
> ![image.png](AWS%20EKS%20Kubernetes%20Cluster%20Provisioning%20&%20Scaling/image%202.png)
> 

### 4. Workload Deployment & Auto-Scaling

Deployed a stateless Nginx web server to the cluster. To simulate high traffic, the deployment was instantly scaled to 5 replicas. Kubernetes automatically distributed the pods across the available worker nodes using AWS's built-in VPC CNI for networking.

> **OpenLens Pods screen showing all 5 Nginx pods running perfectly**
> 
> 
> ![image.png](AWS%20EKS%20Kubernetes%20Cluster%20Provisioning%20&%20Scaling/image%203.png)
> 

### 5. Tear Down

To maintain cost efficiency, the entire infrastructure was destroyed post-validation using:
`eksctl delete cluster --name sharppay-k8s-cluster --region us-east-1`

# 1. Connected terminal to AWS Account

aws configure

# 2. Created the Cluster using the eks-cluster.yaml blueprint

eksctl create cluster -f eks-cluster.yaml

# 3. Verified the servers were running

kubectl get nodes

cat ~/.kube/config

# 4. Deployed our first application (Nginx)

kubectl create deployment my-first-app --image=nginx

# 5. Scaled the application to 5 containers

kubectl scale deployment my-first-app --replicas=5

# 6. Verified the 5 containers were running

kubectl get pods

# 7. Destroyed the cluster to save money

eksctl delete cluster --name sharppay-k8s-cluster --region us-east-1