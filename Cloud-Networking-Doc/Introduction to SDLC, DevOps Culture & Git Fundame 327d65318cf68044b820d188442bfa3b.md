# Introduction to SDLC, DevOps Culture & Git Fundamentals

**Project Author:** Oluwasheyi Ojelade
**Status:** ✅ Completed / Documented
**Focus:** Software Development Life Cycle (SDLC), DevOps Culture, Application Architecture (Frontend/Backend/DB), and Git Version Control.

---

## Part 1: The Software Development Life Cycle (SDLC)

Before jumping into DevOps automation, you must understand how software is built traditionally. The SDLC is a structured framework and set of guidelines that teams follow to build, test, and ship applications.

SDLC breaks down the complex process of software creation into manageable phases, ensuring collaboration across different departments.

### 1.1 The 6 Phases of SDLC & Key Roles

1. **Analysis & Planning:**
    - **Product Owner:** Conducts market research and validates the business idea.
    - **Project Manager / Scrum Master:** Manages tasks, creates sprints, and organizes the internal team.
    - **Business Analyst:** Interprets data to validate the product's viability.
    - **CTO (Chief Technology Officer):** Translates business requirements into technical possibilities.
2. **Design:**
    - **UI/UX Designer:** Designs the aesthetics, interface, and user journey (e.g., login screens, dashboards).
    - **System Architect:** Designs the high-level and low-level technical architecture (choosing the database, backend languages, and infrastructure layout).
3. **Development (Coding):**
    - **Backend Engineers:** Build the "foundation" and business logic (how the app behaves, processes payments, etc.).
    - **Frontend Engineers:** Build the client-facing interface to ensure users can access and interact with the application.
4. **Testing:**
    - **QA (Quality Assurance) Engineers:** Conduct functionality testing and User Acceptance Testing (UAT) to ensure the app works as intended (e.g., "Does the login button actually log me in?").
    - **DevSecOps / Security Testers:** Test for vulnerabilities and business logic flaws.
5. **Deployment:**
    - **DevOps / Operations Engineers:** Provision the staging/production environments and deploy the code so it goes "live" for actual users.
6. **Maintenance & Support:**
    - **Support Engineers:** Investigate user complaints and bugs in production.
    - **Data Administrators:** Monitor user interactions and system logs to gather feedback, which is then fed back into Phase 1 (Planning) for continuous improvement.

---

## Part 2: Introduction to DevOps

### 2.1 What is DevOps?

DevOps is **not** a specific tool; it is a **culture and a system** of practices designed to bridge the gap between Development teams (who write the code) and Operations teams (who deploy and manage the infrastructure).

Instead of developers writing code in isolation and manually handing it over to operations (which causes delays and friction), DevOps automates the entire SDLC pipeline.

### 2.2 The Benefits of DevOps

- **Fast Delivery (The Assembly Line Analogy):** In a traditional car factory, manual welders take years to build a few cars. By introducing robotic assembly lines (Automation), they can build thousands. DevOps acts as the "robotic assembly line" for software, removing manual human deployment errors.
- **Speed to Market (Go-To-Market):** In the modern "AI Race" (OpenAI vs. Anthropic vs. Google), the company that can deploy features the fastest captures the market. Machine Learning Operations (MLOps) and DevOps allow companies to ship code instantly.
- **Automated Rollbacks:** If a new feature contains a critical bug, DevOps automation allows the team to roll back to the previous stable version in split seconds without downtime.
- **Blue-Green Deployments:** Running two live environments simultaneously (one with the old code, one with the new code) and seamlessly cutting user traffic over to the new one only when it is fully stable.

---

## Part 3: The DevOps Toolchain

A DevOps engineer must be familiar with tools across the entire pipeline:

- **Version Control (VCS):** Git, GitHub, GitLab, Bitbucket.
- **CI/CD (Continuous Integration/Deployment):** Jenkins, GitLab CI, Azure DevOps, Bamboo.
- **Configuration Management:** Ansible, Chef, Puppet.
- **Containerization & Orchestration:** Docker (packaging apps into lightweight containers) and Kubernetes/OpenShift (managing multiple containers).
- **Infrastructure as Code (IaC):** Terraform, Pulumi.
- **Monitoring & Logging:** Datadog, Prometheus, Grafana, ELK Stack.
    - *Note on Agentic DevOps:* The integration of AI tools that can automatically read error logs, reason through them, and fix infrastructure issues at 3:00 AM without human intervention.
- **Security (DevSecOps):** SonarQube (SAST), Aqua Security, Snyk, HashiCorp Vault.

---

## Part 4: Application Architecture & The "Tiered" Stack

To deploy code, a DevOps engineer must understand the layers (Tiers) of the application they are hosting.

### 4.1 Tier 1: Frontend (Client-Side)

The graphical user interface (GUI) that users interact with.

- **Languages/Frameworks:** HTML5, CSS3, JavaScript, React, Angular, TypeScript, Tailwind CSS.

### 4.2 Tier 2: Backend (Business Logic)

The core logic that processes user requests, payments, and application rules.

- **Languages/Frameworks:** Java, Python, Golang (Go), C#, ASP.NET, Node.js.

### 4.3 Tier 3: Database (Data Layer)

- **Relational Databases (SQL):** Structured table data.
    - *Examples:* MySQL, PostgreSQL, Microsoft SQL.
    - *Browsers to access them:* MySQL Workbench, pgAdmin, DBeaver.
- **Non-Relational Databases (NoSQL):** Document-format data using JSON (JavaScript Object Notation).
    - *Examples:* MongoDB, Cassandra.
    - *Browsers to access them:* MongoDB Compass.

---

## Part 5: Introduction to Git & GitHub

When you have developers working globally across different time zones, you cannot physically ask them for their code via USB drives. You need a **Distributed Version Control System (DVCS)**.

### 5.1 Git vs. GitHub

- **Git:** The command-line *operation language* installed locally on your laptop. Created by Linus Torvalds (creator of Linux) to track code changes.
- **GitHub:** The remote, cloud-based SaaS platform (Repository) where the code is stored and collaborated on.

### 5.2 The 4 Stages of the Git Workflow

1. **Local Working Directory:** The folder on your computer where you are actively writing code or creating files. (Files here are *Untracked*).
2. **Staging Area:** The "hanging" or preparation phase. You've made changes and are preparing to lock them in. (Command: `git add .`)
3. **Local Repository:** You finalize the changes and log them permanently into your local system's history with a descriptive message. (Command: `git commit -m "Added the login button"`)
4. **Remote Repository:** You upload (Push) your committed code to the cloud (GitHub/GitLab) so the rest of your team and the DevOps engineers can access it. (Command: `git push`)

### 5.3 Essential Git Commands

- `git init`: Initializes a brand new, empty repository in your local folder (creates a hidden `.git` folder).
- `git clone [URL]`: Downloads an existing remote repository from GitHub to your local machine.
- `git config --global user.name "Oluwasheyi Ojelade"`: **CRITICAL STEP.** Configures your identity. If you do not do this, your company will not know you pushed the code, and you will not get credit (or paid) for your work.
- `git pull`: Downloads the latest updates from the remote repository to your local machine.
- `git log`: Shows the entire history of who made what changes and when.

> **[ 🖼️ INSERT SCREENSHOT: A screenshot of a GitHub repository commit history showing different developers and their commit messages ]**
> 

[Enterprise Version Control with Git, Git Bash & Azure DevOps](Enterprise%20Version%20Control%20with%20Git,%20Git%20Bash%20&%20Az%2032bd65318cf6801ea575e2ac581d7604.md)