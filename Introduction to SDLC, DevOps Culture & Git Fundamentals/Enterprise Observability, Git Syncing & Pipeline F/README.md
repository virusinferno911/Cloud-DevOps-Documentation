# Enterprise Observability, Git Syncing & Pipeline Fundamentals (YAML)

**Project Author:** Oluwasheyi Ojelade
**Status:** ✅ Completed / Documented
**Focus:** Slack Integration (Service Hooks), Git Conflict Resolution, YAML Data Structures, and Azure Pipelines/GitHub Actions.

---

## Part 1: Enterprise Observability (Azure DevOps to Slack)

In an enterprise environment, developers and DevOps engineers cannot manually message each other every time code is pushed or a pipeline is triggered. We must build an automated notification system.

### 1.1 Internal Notifications vs. Service Hooks

- **Internal Notifications:** Used when you want to send alerts *within* the Azure DevOps environment (e.g., emailing a specific backend team member about a pull request).
- **Service Hooks:** Used when you need to send notifications *outside* of Azure DevOps to external consumers like Slack, Trello, or Datadog.

### 1.2 Step-by-Step: Slack Integration via Webhooks

To get Azure DevOps to post messages directly into a Slack channel whenever a developer pushes code, we configure an Incoming Webhook.

1. **Slack App Creation:** Go to the Slack API platform and create a new App (e.g., `Azure DevOps Service Hook`) from scratch, assigning it to your target workspace.
2. **Activate Webhooks:** Navigate to "Incoming Webhooks" in the Slack App settings and toggle it **On**.
3. **Generate URL:** Add a new Webhook to workspace, select the specific channel (e.g., `Netflix_backend_notify`), and authorize it. Copy the generated Webhook URL.
4. **Azure DevOps Configuration:** * In Azure DevOps, go to **Project Settings** -> **Service Hooks** -> **Create Subscription**.
    - Select **Slack** as the service.
    - Set the trigger to **Code pushed** and filter it to the specific repository and team.
    - Paste the Slack Webhook URL and click **Test**.
5. **Validation:** A successful test will instantly push a notification message into your Slack channel, proving the API connection is active.

> **A split screen showing Azure DevOps Service Hook configuration on one side, and the automated Slack notification arriving in channel on the other.**
> 
> 
> ![image.png](images/Enterprise%20Observability,%20Git%20Syncing%20&%20Pipeline%20F/image.png)
> 

---

![image.png](images/Enterprise%20Observability,%20Git%20Syncing%20&%20Pipeline%20F/image%201.png)

## Part 2: Advanced Git Operations (Resolving Out-of-Sync Repositories)

As a DevOps engineer, you will often encounter errors when trying to push code if your local machine is out of sync with the cloud repository.

### 2.1 The Sync Error Scenario

- **The Problem:** You write new code locally (e.g., adding `file_03.txt`) and try to push using `git push`. The terminal throws a "Failed to push" error.
- **The Cause:** Another developer (or you, from another computer) pushed changes directly to the remote repository (e.g., changing a file from 6 lines of code to 10 lines). Your local repository does not have these updates, so Git blocks the push to prevent overwriting.

### 2.2 The Resolution Workflow

To fix this, you must merge the remote changes into your local environment before pushing:

1. **Pull the Updates:** Run `git pull`. This downloads the latest updates from the remote repository to your local machine.
2. **Merge and Commit:** Git will automatically attempt to merge the files. You must stage and commit this merge action by running `git add .` followed by `git commit -m "Merged remote updates with local repo"`.
3. **Push to Cloud:** Now that your local repository contains both the new remote changes and your local additions, run `git push origin [branch_name]` to successfully upload the code.

---

## Part 3: Pipeline Scripting Fundamentals (YAML)

To automate CI/CD pipelines in Azure DevOps, GitHub Actions, or Kubernetes, you must write configuration files using YAML.

### 3.1 What is YAML?

YAML stands for "Yet Another Markup Language" (or "YAML Ain't Markup Language"). It is a human-readable data serialization format that is vastly easier to read and write than JSON.

### 3.2 YAML Syntax & Indentation

- **Indentation is Critical:** Like Python, YAML uses strict indentation (spaces, never the Tab key) to denote structure. Nested elements must be indented uniformly (usually by 2 spaces).
- **Case Sensitivity:** `Oluwasheyi` and `oluwasheyi` are treated as entirely different entities.
- **Comments:** Any line starting with a `#` is ignored by the system, allowing you to leave notes for other engineers.

### 3.3 Core Data Types & Structures

- **Data Types:** YAML supports Strings (text), Integers (numbers), Booleans (True/False or 1/0), Null (empty), and Dates.
- **Lists (Arrays):** Used to group common items.
- **Dictionaries (Maps):** Key-Value pairs used to define attributes (e.g., `name: Oluwasheyi`, `location: Nigeria`).
- **Nested Structures:** Placing dictionaries inside of other dictionaries to map out complex resources (like defining the CPU and Memory attributes nested under a specific Server).

---

## Part 4: Introduction to CI/CD Pipelines

### 4.1 Pipeline Architecture

A CI/CD pipeline script generally consists of two main components:

- **The Trigger:** The event that tells the pipeline to start. (e.g., "Run this pipeline whenever code is pushed to the `main` branch") .
- **The Pool (Runner/Agent):** The actual server (Virtual Machine) that acts as the "worker" to execute your pipeline code.

### 4.2 Public vs. Private Runners

- **Public/Hosted Runners:** Free servers provided by Microsoft/GitHub. They are easy to use but are shared resources, making them less secure for highly sensitive enterprise data.
- **Private/Self-Hosted Runners:** A dedicated virtual machine configured specifically to run your company's pipelines. You generate a script in Azure DevOps (or GitHub), log into your private Linux/Windows server, run the script, and register it as a secure, private worker agent.

### 4.3 GitHub Actions & The Marketplace

While Azure uses Azure Pipelines, GitHub uses **GitHub Actions**.

- Instead of building complex pipelines from scratch, DevOps engineers leverage the **GitHub Marketplace**.
- The Marketplace contains thousands of pre-written YAML scripts (e.g., "Docker Build & Push" or "Deploy to AWS EC2") that you can copy, paste, and modify to rapidly automate your workflow.

> **[ 🖼️ INSERT SCREENSHOT: A split image showing an Azure DevOps pipeline template library next to the GitHub Actions Marketplace. ]**
> 

[Full-Stack Manual Deployment (Netflix Clone Architecture)](Enterprise%20Observability,%20Git%20Syncing%20&%20Pipeline%20F/Full-Stack%20Manual%20Deployment%20(Netflix%20Clone%20Archit%20332d65318cf680a38826f5abf52798f0.md)