# Full-Stack Manual Deployment (Netflix Clone Architecture)

**Project Author:** Oluwasheyi Ojelade
**Status:** ✅ Completed / Documented
**Focus:** 3-Tier Architecture, MongoDB, Java/Maven Compilation, Node.js/React, and the "Why" behind Docker.

---

## Part 1: Q&A and Foundational Concepts

Before the lab, the instructor addressed crucial DevOps questions from previous classes:

- **Bash vs. YAML:** Bash is heavily tied to specific Linux distributions and requires different syntax for different environments. YAML is a universal, user-friendly data serialization language used globally across platforms (like Kubernetes and Ansible).
- **Hidden Git Files:** If you clone a repository and it looks empty, it is because the `.git` directory is a hidden system folder. You must use the `ls -la` command to view it.

---

## Part 2: The 3-Tier Application Architecture

To deploy an application successfully, you must understand its dependencies. The Netflix project is split into three stacks that must be deployed in a specific order:

1. **The Data Stack (MongoDB):** Has no dependencies. Must be deployed **first**.
2. **The Backend Stack (Java/Spring):** Depends on the Database. Must be deployed **second**.
3. **The Frontend Stack (React/Node.js):** Depends on the Backend. Must be deployed **last**.

### Testing Methodology

A DevOps engineer must test each layer independently before moving to the next:

- **Database:** Tested visually using **MongoDB Compass**.
- **Backend:** Tested using **Postman** or **Swagger UI** to verify CRUD (Create, Read, Update, Delete) API operations.
- **Frontend:** Tested via the **Web Browser** using the "Inspect" tool (Console and Network tabs).

---

## 📌 Part 3: Step 1 - Database Deployment (MongoDB)

**1. Installation & Security Configuration:**

- SSH into the Ubuntu database server and update the system (`sudo apt update -y`).
- Install the MongoDB Community Edition.
- By default, MongoDB only allows local connections (`bindIp: 127.0.0.1`). Using `sudo nano /etc/mongod.conf`, the instructor edited the network interfaces to `0.0.0.0` to allow the backend server to connect externally.
- *Troubleshooting Note:* The instructor accidentally uncommented a configuration line in the YAML file, which caused the MongoDB service to fail upon restart. Re-commenting the line fixed the issue, emphasizing the strict formatting rules of YAML.

**2. Data Seeding:**

- Connected to the server using the **MongoDB Compass** desktop application.
- Created a database named `movieDB`.
- Imported the raw `movies.json` mock data (containing movie banners, titles, and YouTube trailer links) directly into the collection.

> **[ 🖼️ INSERT SCREENSHOT: MongoDB Compass showing the connected `movieDB` with the populated JSON movie data. ]**
> 

---

## 📌 Part 4: Step 2 - Backend Deployment (Java)

**1. Environment Setup (The Compiler):**Unlike Python (which is interpreted line-by-line), Java is a compiled language. It must be packaged into a single artifact before it can run.

- Installed **JDK 17** (Java Development Kit) to provide the runtime environment.
- Installed **Maven** (`mvn`), the package manager required to read the `pom.xml` file and download Java dependencies.

**2. Configuring the Connection:**

- Cloned the Azure DevOps repository containing the backend code.
- Edited the environmental variables (`.env` / `application.properties`) to point to the MongoDB server's IP address.
- *Troubleshooting Note:* The connection string initially failed because it contained `+srv` (e.g., `mongodb+srv://`), which is meant for MongoDB Clusters. The instructor removed `+srv` since they were using a standalone server, which successfully resolved the crash.

**3. Building & Running the Artifact:**

- Ran `mvn clean` and `mvn package`. Maven downloaded all dependencies and packaged the application into a `.jar` (Java ARchive) file stored in the `target` folder.
- Started the server by running `java -jar target/movies-snapshot.jar`, spinning up the backend on **Port 8080**.

> **[ 🖼️ INSERT SCREENSHOT: The Linux terminal showing the successful Maven build and the Spring Boot logo indicating the backend is running on Port 8080. ]**
> 

---

## 📌 Part 5: Step 3 - Frontend Deployment (Node.js/React)

**1. Environment Setup:**

- SSH into the third Ubuntu server and cloned the frontend repository.
- Installed **Node.js (Version 20)** and **NPM** (Node Package Manager).
- Edited the frontend configuration file to include the IP address and port (8080) of the running backend server so the UI could fetch the movie data.

**2. Building & Running:**

- Ran `npm install`. This command reads the `package.json` file and downloads all necessary React dependencies.
- Ran `npm start` to launch the application on **Port 3000**.

---

## 📌 Part 6: The "Why" Behind Docker & CI/CD (Conclusion)

At the end of the lab, the instructor summarized the entire purpose of this grueling manual exercise:

- **The Problem:** It took over an hour to manually provision servers, install dependencies (JDK, Node.js), troubleshoot environment variables, and compile code across three different VMs. If traffic spikes, manually scaling this architecture would be a nightmare.
- **The Solution (Next Steps):** Instead of configuring individual servers, the industry uses **Docker** to package the application *with* its dependencies into a single, lightweight container.
- **The Future Stack:** In the next modules, the class will transition to using **GitHub Actions** (CI/CD) to automate this entire deployment process, and **Kubernetes** to orchestrate the containers instantly.

[Production Architecture & Deployment Portfolio](Production%20Architecture%20&%20Deployment%20Portfolio%20333d65318cf6802eb211d05502939803.md)

[Cloud SecDevOps - Introduction to Docker](Cloud%20SecDevOps%20-%20Introduction%20to%20Docker%20335d65318cf68068ade9dc08613d7631.md)