# CI/CD Automation: Terraform & GitHub Actions Integration

### Project Overview

Following the successful manual deployment of an AWS VPC using Terraform via the CLI, this phase of the project transitions the infrastructure deployment into a fully automated **Continuous Integration and Continuous Deployment (CI/CD)** pipeline.

By integrating **GitHub Actions**, the infrastructure is now provisioned automatically upon code commits, enforcing strict version control, code formatting standards, and secure remote state management without exposing sensitive credentials.

### Architecture & Tools

- **Version Control:** Git & GitHub
- **CI/CD Pipeline:** GitHub Actions (`ubuntu-latest` runners)
- **Infrastructure as Code:** Terraform
- **Cloud Environment:** AWS (VPC, IGW)
- **Remote State Storage:** AWS S3

---

## Phase 1: Remote State Management Configuration

In a CI/CD environment, GitHub Actions runners are ephemeral (temporary). If Terraform state is stored locally on the runner, it is destroyed when the job finishes, leading to infrastructure duplication and drift. To solve this, state management was migrated to a highly available remote backend.

**1. Provisioning the S3 Backend**
A dedicated S3 bucket was created in AWS to securely store the `terraform.tfstate` file, ensuring the pipeline always has a single source of truth for the current infrastructure state.

> *AWS S3 Console showing the successful creation of the `terraform-state-practice-2026` bucket used for remote state locking.*
> 
> 
> ![10.png](images/CI%20CD%20Automation%20Terraform%20&%20GitHub%20Actions%20Integr/10.png)
> 

**2. Updating the Provider Configuration**
The `0-provider.tf` file was updated to route state tracking to the new S3 bucket. Additionally, **hardcoded AWS credentials were completely removed** from the source code to adhere to strict security best practices.

> *VS Code showing the updated `0-provider.tf` file featuring the new S3 backend configuration block.*
> 
> 
> ![11.png](images/CI%20CD%20Automation%20Terraform%20&%20GitHub%20Actions%20Integr/11.png)
> 

---

## Phase 2: Version Control & The `.gitignore` Trap

The local workspace was initialized as a Git repository to link it to GitHub. During the initial push, the deployment hit a critical roadblock: Git attempted to upload the hidden `.terraform` directory, which contained a 685 MB AWS provider executable, exceeding GitHub's 100 MB file size limit.

**The Resolution:**
A `.gitignore` file was immediately implemented to block `.terraform/` and local state files. The Git cache was cleared (`git rm -r --cached .terraform`), and the commit was amended, successfully bypassing the size limitation while keeping the repository clean.

> *VS Code displaying the newly created `.gitignore` file and the terminal executing the cache-clearing and commit amendment commands.*
> 
> 
> ![11b.png](images/CI%20CD%20Automation%20Terraform%20&%20GitHub%20Actions%20Integr/11b.png)
> 

---

## Phase 3: Securing CI/CD Credentials

Because the hardcoded keys were removed from the Terraform configuration, the GitHub Actions runner needed a secure method to authenticate with AWS. The IAM access keys were injected directly into the GitHub repository's encrypted secrets environment.

> *GitHub Repository Settings demonstrating the injection of `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` into the Actions Secrets vault.*
> 
> 
> ![12.png](images/CI%20CD%20Automation%20Terraform%20&%20GitHub%20Actions%20Integr/12.png)
> 

---

## Phase 4: Constructing the Workflow Pipeline

A `.github/workflows/terraform.yml` file was created to define the exact CI/CD instructions. The pipeline was programmed to trigger on any push to the `main` branch.

**Pipeline Stages:**

1. Checkout repository code.
2. Authenticate to AWS using injected repository secrets.
3. Setup the Terraform CLI environment.
4. Execute `terraform init`, `terraform fmt -check`, `terraform plan`, and `terraform apply -auto-approve`.

> *The fully structured YAML workflow file configuring the GitHub Actions pipeline.*
> 
> 
> ![13.png](images/CI%20CD%20Automation%20Terraform%20&%20GitHub%20Actions%20Integr/13.png)
> 

---

## Phase 5: Pipeline Execution & Formatting Enforcement

Upon pushing the code, the pipeline triggered automatically. During an initial run, the pipeline intentionally failed at the `terraform fmt -check` stage. This functioned exactly as designed, acting as a strict quality gate preventing improperly formatted HCL code from reaching production.

After running `terraform fmt` locally and pushing the corrected code, the pipeline successfully executed all steps, provisioning the infrastructure automatically.

> *GitHub Actions dashboard confirming a fully successful "Terraform Execution" run, passing all quality checks and deployment stages.*
> 
> 
> ![14.png](images/CI%20CD%20Automation%20Terraform%20&%20GitHub%20Actions%20Integr/14.png)
> 

---

## Phase 6: Cloud Verification & Lifecycle Management

To verify the automated deployment, the AWS Management Console was audited. The pipeline successfully provisioned the target infrastructure without any manual intervention.

> *AWS VPC Dashboard verifying the active `DevOps-Practice-VPC` deployed by the GitHub Actions runner.*
> 
> 
> ![15.png](images/CI%20CD%20Automation%20Terraform%20&%20GitHub%20Actions%20Integr/15.png)
> 

### **Resource Teardown:**
To complete the lifecycle and prevent orphaned resources, the infrastructure was successfully destroyed. Because the state was housed remotely, the local CLI was initialized to read from S3, allowing a clean `terraform destroy` command to execute successfully.

> *VS Code terminal output verifying the successful teardown of the automated resources.*
> 
> 
> ![16.png](images/CI%20CD%20Automation%20Terraform%20&%20GitHub%20Actions%20Integr/16.png)
>