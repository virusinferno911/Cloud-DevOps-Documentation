# Cloud-Native Infrastructure & CI/CD Pipeline Automation

**Date:** 06/04/2026
**Author:** Oluwasheyi Ojelade

### Overview: The Shift to Automated Infrastructure

This class focused on bridging the gap between local development and enterprise-grade production environments. The core theme was moving away from manual server administration (SSH, manual builds, exposed ports) toward secure, containerized, and fully automated deployment workflows.

### Core Concepts Covered

**1. Database Security & DBaaS (Database-as-a-Service)**

- **The Problem:** Self-hosting databases on a VPS with open ports (like MongoDB on 27017) exposes the server to automated ransomware bots.
- **The Solution:** Migrating data layers to managed cloud providers like MongoDB Atlas. This shifts the burden of replication, backups, and firewall whitelisting (IP access management) to the cloud provider.

**2. Multi-Stage Docker Containerization**

- Best practices for containerizing compiled languages (like Java/Spring Boot).
- Using a "builder" image (containing Maven and the JDK) to compile the `.jar` file, and a minimal "runtime" image to execute it.
- This ensures no source code or build tools are exposed in the final production container, reducing the image size and security footprint.

**3. CI/CD Pipeline Architecture (GitHub Actions to AWS)**

- **Continuous Integration:** Using GitHub Actions to automatically run runners on every push to the `main` branch.
- **Secrets Management:** Utilizing encrypted GitHub Secrets to inject production variables (like AWS credentials and database URIs) so they never exist in the codebase.
- **Continuous Delivery:** Automating the push to a container registry (AWS ECR) and using automated SSH commands with `ed25519` deploy keys to trigger server-side updates.

**4. Reverse Proxying with Web Servers**

- Using tools like Caddy to securely route HTTPS traffic, automatically provision SSL certificates, and manage the boundary between the frontend static assets and backend API containers.

---

### Practical Application: My Implementation (Chill Clips)

Following the class, I completely overhauled my streaming platform, **Chill Clips**, to implement these enterprise standards.

- **The Catalyst:** After experiencing the exact vulnerability discussed in class (ransomware bots wiping my self-hosted database), I migrated the entire application data layer to MongoDB Atlas and permanently blocked port 27017 on my firewall.
- **The Backend:** I wrote a multi-stage Dockerfile for my Spring Boot API, ensuring a lean, secure final image.
- **The Pipeline:** I successfully built the CI/CD pipeline using GitHub Actions. Now, when I push code, the runner builds the Docker image, pushes it to AWS ECR, logs into my Contabo VPS, and restarts the backend container in under 90 seconds.
- **The Frontend:** I routed traffic through a Dockerized Caddy reverse proxy, serving the React frontend (with its new real-time search functionality and custom UI) alongside the isolated backend.

[Automated Deployment with Docker Compose](Cloud-Native%20Infrastructure%20&%20CI%20CD%20Pipeline%20Autom/Automated%20Deployment%20with%20Docker%20Compose%20341d65318cf680388d97fb409366368a.md)