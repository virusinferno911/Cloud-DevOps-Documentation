# Kubernetes Architecture & Deployment Models

> **Module:** Container Orchestration (Class 25)
**Topic:** K8s Architecture, Deployment Models, and OpenLens Management
**Tags:** Kubernetes, K3s, EKS, Kubeadm, DevOps
> 

## 1. Kubernetes Cluster States & Architectures

A Kubernetes cluster can be deployed in three primary configurations depending on the workload requirements:

- **Single-Node Cluster:** An "all-in-one" setup where the master and worker node functionalities reside on a single virtual machine. It is highly cost-effective and strictly used for development and local testing.
- **Multi-Node Cluster:** Consists of one Master node and multiple Worker nodes. Used for staging and pre-production. It is **not** used for production because the single master node represents a Single Point of Failure (SPOF).
- **Multi-Master Node Cluster:** The production-grade architecture. It utilizes multiple master nodes behind a load balancer. If one master fails, a standby master instantly takes over, ensuring complete fault tolerance for enterprise applications.

## 2. On-Premises vs. Cloud-Managed (EKS)

- **On-Premises (Manual):** The DevOps engineer is fully responsible for configuring and upgrading the Control Plane (Master Node). Maintaining consistency across component versions (API Server, Controller, Scheduler) requires significant effort and deep troubleshooting skills.
- **Cloud-Managed (e.g., AWS EKS):** The cloud provider manages the Master Node, ensuring it never goes down. Engineers only manage the Worker Nodes via an **Autoscaler**, which automatically spins up or tears down worker instances based on CPU/Memory thresholds and defined minimum/maximum rules.

## 3. Core Technologies & Concepts

- **K3s:** A lightweight, highly efficient Kubernetes distribution developed by SUSE. It is ideal for IoT, edge computing, and air-gapped environments (where no inbound/outbound internet traffic is allowed for security reasons).
- **Swap Memory:** Kubernetes strictly requires swap memory to be disabled (`swapoff -a`) to ensure the `kubelet` can accurately allocate hardware resources without system interference.
- **Networking (Underlay vs. Overlay):**
    - *Underlay:* The host virtual machine network (e.g., the AWS VPC and physical private IPs).
    - *Overlay:* The internal Pod-to-Pod network. This requires a Container Network Interface (CNI).
- **Cilium:** An open-source CNI used to manage the overlay network, providing high-performance load balancing and strict network security policies.
- **OpenLens:** A graphical user interface (GUI) installed locally to visually manage multiple K8s clusters, view resource utilization, and deploy applications without relying strictly on the terminal.

[AWS EKS Kubernetes Cluster Provisioning & Scaling](Kubernetes%20Architecture%20&%20Deployment%20Models/AWS%20EKS%20Kubernetes%20Cluster%20Provisioning%20&%20Scaling%20343d65318cf680cab1ffcbf644f781b5.md)

[Manual On-Premises Kubernetes Cluster (Kubeadm)](Kubernetes%20Architecture%20&%20Deployment%20Models/Manual%20On-Premises%20Kubernetes%20Cluster%20(Kubeadm)%20343d65318cf680c8a7dbd7664e73d917.md)

[☸️ Kubernetes Operations: Pods, Scaling, and Exposing Services via AWS ELB](Kubernetes%20Architecture%20&%20Deployment%20Models/%E2%98%B8%EF%B8%8F%20Kubernetes%20Operations%20Pods,%20Scaling,%20and%20Exposi%20347d65318cf680f1b9d9e244deba1ac9.md)