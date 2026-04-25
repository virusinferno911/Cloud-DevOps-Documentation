# Cloud SecDevOps - Introduction to Docker

**Instructor:** Engineer Smart

**Topic:** Virtualization, Containerization Concepts, and Docker Practical Lab

**Module:** Cloud Security & DevOps

---

## 1️⃣ Opening Q&A and Troubleshooting

Before diving into the core Docker concepts, the instructor addressed two critical enterprise scenarios:

- **Local VMs vs. Cloud Hosting:** While local hypervisors (like Oracle VirtualBox) are acceptable for "closed-door" or local testing, cloud platforms (AWS, Azure, GCP) are strictly required for deploying applications publicly, integrating with CI/CD pipelines, and allowing external access.
- **MongoDB Security Reality:** Binding MongoDB to `0.0.0.0` (open to the internet) is purely for isolated testing. In a real enterprise environment, the database sits in a private subnet with no internet access. Connections are only allowed internally from the backend server via strict firewall rules or through secure VPNs for on-premise developers.

---

## 2️⃣ Introduction to Virtualization

To understand Docker, we first must understand traditional virtualization.

- **Physical vs. Virtual Layers:** Physical bare-metal machines act as the "underlay" layer, while virtual machines act as the "overlay" layer.
- **Hypervisors:** The software layer that makes virtualization possible by allocating hardware resources to virtual machines.
    - **Type 1 Hypervisor (Bare-Metal):** Installed directly on the hardware without a host operating system. This is what major cloud providers (AWS, Azure, GCP) use to partition their servers (e.g., VMware ESXi).
    - **Type 2 Hypervisor (Hosted):** Installed on top of an existing host operating system, functioning like a standard application (e.g., Oracle VirtualBox, VMware Workstation Pro).

> [ 📸 **INSERT IMAGE HERE:** *Screenshot or diagram showing the difference between Type 1 and Type 2 Hypervisors.* ]
> 

---

## 3️⃣ Containerization Concepts

Containerization solves the heavy resource overhead of traditional VMs and the classic developer excuse: *"It works on my machine."*

- **What is a Container?** It packages an application along with all its necessary configurations, binaries, and dependencies into an isolated, lightweight, and highly portable artifact. Developers on Windows, Mac, or Linux can run the exact same container without OS discrepancies.
- **The Reproduction Analogy:**
    - **Container Image (The Initiator):** The packaged, static artifact containing the code and dependencies.
    - **Container Engine (The Incubator):** The software (like Docker Engine, `containerd`, or `Podman`) that hosts and runs the container.
    - **Container Instance (The Offspring):** The actual, active running container created from the static image.

---

## 4️⃣ Docker Architecture & Repositories

- **The Docker Daemon (`dockerd`):** The background service engine that runs Docker. It manages two main partitions: one for storing downloaded images and one for executing active containers.
- **The Docker Client:** The CLI tool (Terminal) that engineers use to issue commands to the Docker Daemon.
- **Repositories (The Image Bank):** Where packaged images are stored and distributed.
    - **Docker Hub:** The public registry for official images (Ubuntu, WordPress, MySQL).
    - **AWS ECR (Elastic Container Registry):** A cloud-provider alternative offering both public and private enterprise registries.

> 🛡️ **DevSecOps Note:** Public images should never be deployed blindly in an enterprise. They must always be scanned by a DevSecOps engineer for vulnerabilities or malicious code prior to deployment.
> 

> [ 📸 **INSERT IMAGE HERE:** *Screenshot of the AWS ECR dashboard or Docker Hub interface shown by the instructor.* ]
> 

---

## 5️⃣ Practical Lab: Installing Docker on AWS Ubuntu

The instructor transitioned to a practical lab on an AWS EC2 Ubuntu instance.

### Installation Process

Following the official Docker documentation, the following scripts were used to add repository keys and install the engine:

Bash

`sudo apt update
# (Followed by adding official Docker GPG keys and repo)
sudo apt install docker-ce docker-ce-cli containerd.io`

### Fixing the "Permission Denied" Error

Attempting to run `docker ps` immediately after installation throws a permission error because the default `ubuntu` user lacks privileges.

Bash

`# Add the 'ubuntu' user to the 'docker' security group
sudo usermod -aG docker ubuntu

# Switch back into the user to apply group changes
su - ubuntu`

### Searching and Pulling Images

Bash

`# Search Docker Hub for official images
docker search ubuntu
docker search wordpress

# Download the images to the local Docker Daemon
docker pull ubuntu
docker pull wordpress`

> [ 📸 **INSERT IMAGE HERE:** *Screenshot of the terminal showing the successful `docker pull` output with the downloaded image layers.* ]
> 

---

## 6️⃣ Running Containers & Port Mapping (WordPress Demo)

To demonstrate application isolation and port mapping, the instructor deployed WordPress instances.

### Deploying the First Instance

Bash

`docker run -d -p 80:80 --name wordpress_container wordpress:latest`

- **`d`**: Runs the container in "detached" mode (in the background).
- **`p 80:80`**: Port mapping (`HostPort:ContainerPort`). It maps port 80 of the EC2 instance directly to port 80 inside the container.

### Deploying a Second Instance (Conflict-Free)

To prove the power of container isolation, a second WordPress site was launched on the *exact same server* by simply mapping it to a different host port.

Bash

`docker run -d -p 81:80 --name wordpress2 wordpress:latest`

Accessing `http://<EC2-IP>:80` loads the first site, and `http://<EC2-IP>:81` loads the completely separate second site.

> [ 📸 **INSERT IMAGE HERE:** *Screenshot of the browser showing the WordPress setup page accessed via the EC2 instance's IP address.* ]
> 

---

## 7️⃣ Closing Q&A and Next Steps

The session closed with a look ahead to creating custom `Dockerfiles` and utilizing GitHub Actions for CI/CD, alongside two final student questions:

- **The Importance of `.gitignore`:** Sensitive files, such as `.env` files holding database passwords, must *never* be pushed to GitHub. They must be explicitly listed in a `.gitignore` file. Instead, engineers provide a `README.md` or `.env.example` template so team members can safely recreate their local environment variables.
- **Server Ephemerality:** If the EC2 instance hosting Docker is deleted, all containers, images, and data on it are lost and must be rebuilt from scratch on a new server (highlighting the need for persistent storage volumes and CI/CD pipelines).

[Cloud-Native Infrastructure & CI/CD Pipeline Automation](Cloud-Native%20Infrastructure%20&%20CI%20CD%20Pipeline%20Autom%2033cd65318cf68056a83cddea3d4b104f.md)