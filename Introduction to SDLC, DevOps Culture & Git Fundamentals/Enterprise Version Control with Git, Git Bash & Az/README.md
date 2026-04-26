# Enterprise Version Control with Git, Git Bash & Azure DevOps

**Project Author:** Oluwasheyi Ojelade
**Status:** ✅ Completed / Documented
**Focus:** Local Git Workflows, Azure DevOps (Repos & Boards), Branching Strategies, and Secure Authentication (PATs).

---

## Part 1: The Core Git Workflow & Environment Setup

Before touching the cloud, a DevOps engineer must master the local working environment. Git operates in four distinct stages:

1. **Working Directory:** The local folder on your computer where you write code.
2. **Staging Area:** The preparation zone where files are added (`git add`) to be tracked.
3. **Local Repository:** The hidden `.git` folder on your machine where committed changes (`git commit`) are permanently logged.
4. **Remote Repository:** The cloud server (Azure DevOps, GitHub, GitLab) where the final code is pushed (`git push`) for team collaboration.

### 1.1 The VS Code & Git Bash Integration

- **VS Code (Visual Studio Code):** The industry-standard Integrated Development Environment (IDE) used by developers and DevOps engineers.
- **Integrated Terminal:** Instead of opening separate command prompts, VS Code allows you to run **Git Bash** directly inside the editor. This gives you a native Linux terminal feel on a Windows machine.

---

## Part 2: Introduction to Azure DevOps

While GitHub is the most popular public repository, **Azure DevOps** is an enterprise-grade suite favored by many large corporations. It contains everything needed for the Software Development Life Cycle (SDLC) in one place.

### The 5 Pillars of Azure DevOps:

1. **Azure Boards:** The Project Management tool (similar to Jira or Trello). It tracks **Backlogs** (the list of features to build), **Sprints** (2-week work cycles), and **Tasks**.
2. **Azure Repos:** The code repository (similar to GitHub).
3. **Azure Pipelines:** The CI/CD engine to automate building and deploying code.
4. **Azure Test Plans:** Used by QA engineers to test application functionality.
5. **Azure Artifacts:** Used to store and share packages (like npm or NuGet).

---

## Part 3: Practical Lab – Azure DevOps & Git Execution

**Objective:** Create an enterprise organization in Azure DevOps, set up multiple repositories (Frontend/Backend), clone them locally, apply a branching strategy, and securely push code.

### Step 1: Creating the Azure DevOps Infrastructure

1. Log into Azure DevOps and click **New Organization** (e.g., Virusinferno).
2. Create a **New Project** inside the organization (e.g., `KlipNaija App`). Keep it Private.
3. Navigate to **Repos** on the left menu.
4. Use the top dropdown to create two separate repositories: `KlipNaija-App_Backend` and `KlipNaija-App_Frontend`.

> **Azure DevOps dashboard showing the two newly created repositories.**
> 
> 
> ![image.png](images/Enterprise%20Version%20Control%20with%20Git,%20Git%20Bash%20&%20Az/image.png)
> 

### Step 2: Cloning & Authentication

1. Navigate to the `KlipNaija-App_Frontend` repo and click **Clone**. Copy the HTTPS URL.
2. Open your local terminal (Git Bash) and run:Bash
    
    `git clone [AZURE_REPO_URL]`
    
3. **Authentication:** Azure DevOps will pop up a Microsoft Entra ID login screen. Authenticate with your Azure credentials to authorize the clone.
4. Type `cd KlipNaija-App_Frontend` and then `code .` to open the folder directly in VS Code.

### Step 3: Local Git Configuration (The Security Warning)

When configuring Git, you must register your name and email so commits are tracked to you.

- **CRITICAL RULE:** Do **NOT** use the `-global` flag (e.g., `git config --global user.email`) if you use the same laptop to work for multiple clients. Doing so will attach the same email to every repository on your machine, potentially causing a breach of contract if Client A sees you committing code with Client B's email address.
- **The Fix:** Configure it strictly for the current local repository:Bash
    
    `git config user.name "Oluwasheyi Ojelade"
    git config user.email "sheymania@gmail.com"`
    

### Step 4: Branching Strategy & The `.gitignore` File

- **The Golden Rule:** **Never push code directly to the `main` branch.** The `main` branch represents the live production environment ("perfect code").
- Always create a "Feature Branch" or "Bugfix Branch" based on your assigned task.Bash
    
    `git checkout -b feature_button`
    
- Create a new file in VS Code (e.g., `file-01.txt`) and write some text. Ensure VS Code's **Auto-Save** feature is turned on to prevent tracking errors.
- **`.gitignore`:** If VS Code generates background settings files (like `.vscode/`), create a file named `.gitignore`, type `.vscode/` inside it, and save. Git will now completely ignore that folder, keeping your repository clean.

### Step 5: Staging, Committing, and Pushing

1. **Check Status:** `git status` (Your new file will show in red as Untracked).
2. **Stage the File:** `git add .` (Tracks all changes; file turns green).
    - *Note: If you stage a file by mistake, use `git restore --staged [filename]` to unstage it.*
3. **Commit the File:** Lock in the changes with a highly descriptive message.Bash
    
    `git commit -m "Added the new registration button to the backend"`
    
4. **Push to Azure DevOps:** Push your specific feature branch to the remote repository.Bash
    
    `git push -u origin feature_button`
    

> **VS Code terminal showing the successful 'git push' command, followed by a screenshot of the Azure DevOps UI showing the new code in the 'feature_button' branch**
> 
> 
> ![image.png](images/Enterprise%20Version%20Control%20with%20Git,%20Git%20Bash%20&%20Az/image%201.png)
> 

---

## Part 4: GitHub Equivalent & Personal Access Tokens (PAT)

While Azure uses Microsoft Entra ID for authentication, GitHub relies on **Personal Access Tokens (PAT)** for CLI authentication.

1. In GitHub, go to **Settings** -> **Developer Settings** -> **Personal Access Tokens (Classic)**.
2. Generate a new token with `repo` scopes. Set an expiration date for security.
3. When cloning a private GitHub repository via Git Bash, the system will prompt for a Username and Password.
    - **Username:** Your GitHub username.
    - **Password:** Paste the generated PAT (You cannot use your actual GitHub account password in the CLI).

---

## Part 5: Project & Code Documentation Differences

As a DevOps engineer, you must understand the difference between process documentation and code documentation:

- **Process Documentation (Notion):** What we are doing here. Full-blown architectural breakdowns, screenshots, and step-by-step guides used for portfolios or team handovers.
- **Code Documentation (`README.md`):** A markdown file located strictly inside the Git repository. It tells other developers exactly what the code does, how to install dependencies, and how to run the pipeline. A repository without a well-written `README.md` is considered incomplete.

[Enterprise Observability, Git Syncing & Pipeline Fundamentals (YAML)](Enterprise%20Version%20Control%20with%20Git,%20Git%20Bash%20&%20Az/Enterprise%20Observability,%20Git%20Syncing%20&%20Pipeline%20F%2032dd65318cf680f09ec0d5431f4bd2f6.md)