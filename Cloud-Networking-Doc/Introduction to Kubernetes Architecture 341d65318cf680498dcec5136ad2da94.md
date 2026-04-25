# Introduction to Kubernetes Architecture

> **Module:** Container Orchestration
**Topic:** Kubernetes (K8s) Theoretical Deep-Dive
**Tags:** K8s, Architecture, Control Plane, Worker Nodes, Scaling
> 

## 1. What is Kubernetes?

Kubernetes (K8s) is an open-source container orchestration platform originally developed by Google and currently managed by the Cloud Native Computing Foundation (CNCF).

- **Important Distinction:** It is *not* a container engine (like Docker). It is the "Choir Master" that commands, manages, and scales the container engines across multiple servers.
- **Purpose:** To prevent system crashes when applications experience massive traffic spikes (e.g., scaling from 10 users to 3 million users instantly).

## 2. Core Capabilities

- **Massive Scalability:** Distributes containers automatically across multiple underlying servers to handle heavy workloads.
- **Self-Healing (Auto-Recovery):** Constantly monitors the "heartbeat" of containers. If a container dies, K8s instantly spins up a replacement with zero user downtime.
- **Service Discovery & Load Balancing:** Pods (containers) frequently die and change IP addresses. Kubernetes uses load balancers to provide a single, unchanging access point for network traffic.
- **Rolling Updates & Rollbacks:** Deploys new versions of an application gradually by creating new pods and destroying old ones incrementally. If a new deployment fails, it automatically rolls back to the previous stable state.

## 3. Kubernetes Cluster Architecture

A cluster is split into two distinct environments: The **Control Plane** (The Brain) and the **Worker Nodes** (The Muscle).

### The Control Plane (Master Node)

The Control Plane makes global decisions about the cluster.

- **API Server:** The front door. All internal and external communications (e.g., from `kubectl`) must pass through here for authentication, authorization, and admission control.
- **ETCD (Cluster Store):** The highly available key-value database of the cluster. It stores every configuration and the "Desired State" of the entire system.
- **Scheduler:** Watches for newly created pods that have no assigned node, and selects a Worker Node for them to run on. Decisions are based on available resources, node health, and **Affinity/Anti-Affinity rules** (e.g., ensuring a high-resource pod only deploys to high-performance servers).
- **Controller Manager:** The internal enforcer. It runs controller processes that constantly compare the **Desired State** (defined in your YAML files) against the **Actual State** (what is physically running). If a pod dies, the Controller immediately instructs the cluster to spin up a new one to close the gap.
- **Cloud Controller Manager:** The external enforcer. It interacts directly with the underlying cloud provider (AWS, Azure, GCP) to provision external resources like Load Balancers or block storage, and ties them into the K8s cluster.

### The Worker Nodes

The physical or virtual machines where the containerized applications actually run.

- **Container Runtime:** The underlying software that runs the containers (e.g., Docker, containerd).
- **Kubelet:** The "umbilical cord" connecting the Worker Node to the Master Node's API Server. It ensures containers are running in a pod and constantly reports their health status back to the Control Plane.
- **Kube-Proxy:** The networking brain of the node. It maintains network rules, handling the complex translation of traffic by routing data from the physical server's network (Underlay Network) into the internal pod-to-pod network (Overlay Network) in milliseconds.

[Kubernetes Architecture & Deployment Models](Kubernetes%20Architecture%20&%20Deployment%20Models%20342d65318cf680a889c5f01d7c078c7a.md)