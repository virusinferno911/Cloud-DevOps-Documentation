# Automated Deployment with Docker Compose

> **Module:** Containerization & CI/CD
**Topic:** Migrating from Manual Deployments to Automated Docker Compose Workflows
**Tags:** Docker, AWS ECR, EC2, IAM, Networking
> 

## 1. Real-World Engineering Context: Troubleshooting

When manual deployments fail (e.g., NPM build failures, incorrect JDK versions, or malformed API route configurations), professional environments handle this via **Code Reviews**. DevOps engineers do not fix application code blindly; they escalate the error logs to the Backend or Frontend development teams and collaboratively debug the connection strings or dependencies on a call.

## 2. CI/CD Branching Strategy

Before touching the server, a strict Git branching strategy is utilized to align with professional deployment pipelines:

- **`dev` (Development):** Runs on a "Green" pipeline, deploying to a safe testing environment.
- **`preprod` / `staging`:** A dedicated pipeline for staging features before a live release.
- **`main` (Production):** The final pipeline deploying directly to the live environment.

**Pipeline Flow:**

1. **Job 1 (Build & Push):** GitHub Actions uses Dockerfiles to build the container images and push them securely to AWS ECR (Elastic Container Registry).
2. **Job 2 (Deploy):** Pulls the images from ECR and deploys them to the server.

## 3. Server Provisioning & AWS Authentication

To set up the development environment:

- **Infrastructure:** An AWS EC2 instance (`t2.medium`, 4GB RAM) was provisioned.
- **Security Groups:** Port 22 (SSH), Port 8080 (Backend), and Port 3000 (Frontend) were opened initially for testing.
- **Server Tooling:** Installed Docker (Community Edition), Docker Compose Plugin, and AWS CLI.

**Authenticating EC2 with AWS ECR:**
Because Docker images are stored privately in ECR, the server needs read permissions.

1. Create an AWS IAM Role with the `AmazonEC2ContainerRegistryPullOnly` policy.
2. Attach this IAM Role to the EC2 instance.
3. Execute the AWS CLI ECR login command on the server to generate a temporary authentication token (stored in `~/.docker/config.json`).

## 4. Docker Compose & Secure Networking

The `docker-compose.yml` file standardizes the deployment of multiple containers (frontend and backend).

**Key Configuration Blocks:**

- **Constants:** `version`, `services`, `networks`, `volumes`.
- **Variables:** `image`, `ports`, resource limits (`cpus`/`memory`).

**Networking & Security Fixes:**
To ensure containers can communicate securely:

1. **Docker Bridge Network:** A custom network (e.g., `dev-net`) was defined so the frontend and backend containers could talk to each other internally.
2. **Port Mapping (Frontend):** Used `ports: "3000:3000"` to map the internal container port to the external EC2 host, exposing it to the internet.
3. **Internal Routing (Backend):** Used `expose: "8080"` instead of `ports`. This is a crucial security best practice. It prevents external internet traffic from reaching the backend directly, ensuring the API is strictly private and only accessible internally by the frontend via the Docker network.

**Core Commands:**

- `docker compose up -d`: Run containers detached in the background.
- `docker logs -f [container_name]`: Monitor live traffic and errors.
- `docker compose down`: Destroy the environment safely.