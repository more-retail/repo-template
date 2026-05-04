# AWS Deployment Workflow

This repository contains a secure deployment workflow for managing AWS infrastructure across different environments using GitHub Actions and OpenID Connect (OIDC).

## 🚀 Workflow Overview

The deployment pipeline automates production deployments upon merging code, while development environments are deployed manually. The pipeline leverages GitHub Environments to introduce strict access controls and approvals.

### 🌐 Environments

1. **`dev` Environment:**
   - **Deployment Branch:** Any branch can be deployed.
   - **Trigger:** Manual (`workflow_dispatch`).
   - **Access:** Anyone with Write access to the repository can trigger a dev deployment.
   - **Approval Required:** None.
   - 
2. **`prod` Environment:**
   - **Deployment Branch:** Strictly limited to the `main` branch.
   - **Trigger:** Automatic (on merge/push to `main`) or Manual (`workflow_dispatch`).
   - **Access:** Anyone with Write access can trigger a manual deployment.
   - **Approval Required:** Yes. Job pauses and waits for designated reviewers to approve.

## 🛡️ Security & Access Policies

To maintain code quality and secure our deployment pipelines, our organization enforces the following global security rules across our repositories.

### 1. Branch Protection (`main` branch)
Direct pushes to the `main` branch are disabled organization-wide via GitHub Rulesets. All changes must go through the Pull Request (PR) review process. 

**Pull Request Requirements:**
* **Approvals:** At least **1 approved review** is required before merging.
* **Stale Approvals:** Pushing new commits to a PR will automatically dismiss previous approvals. You must request a re-review.
* **Conversations:** All comment threads must be marked as "Resolved" before the merge is allowed.

**Merge Restrictions:**
* While anyone with repository access can open a PR, the ability to actually click "Merge" on the `main` branch is restricted. 
* Only designated Organization Admins and specific GitHub Teams (defined in the Organization Ruleset Bypass List) have the authorization to merge code into `main`.



## 🛠️ Security Settings & Prerequisites

To ensure the security of the Production environment, the following configurations must be set within your GitHub Organization/Repository Settings:

### 1. Configure GitHub Environments
Environments add a layer of protection and require manual approval before allowing the workflow to contact AWS.

* Go to **Settings > Environments**.
* **Create `dev`**: Add a new environment named `dev`. No further restrictions are needed.
* **Create `prod`**: Add an environment named `prod`. 
   * **Required Reviewers:** Check this box and add the GitHub users/teams responsible for approving production deployments.
   * **Deployment Branches:** Select "Selected branches and tags", add a rule, and specify `main`.


## 🏃 How to Deploy

### Automatic Production Deployment
The `prod` environment is automatically deployed whenever a Pull Request is merged into the `main` branch. 

*(The workflow will pause. An authorized reviewer must click "Review Deployments" and approve it for the AWS deployment to proceed).*

### Manual Deployment
If you need to trigger a deployment manually:
1. Navigate to the **Actions** tab in this repository.
2. Select the **Deploy AWS Infrastructure** workflow on the left sidebar.
3. Click the **Run workflow** dropdown button.
4. **Select Branch:** 
   - For `dev`, select your feature branch.
   - For `prod`, you **must** select `main`.
5. **Select Environment:** Choose `dev` or `prod` from the dropdown.
6. Click **Run workflow**.

## 🔒 OIDC Authentication
This workflow uses OpenID Connect (OIDC) to authenticate with AWS. This eliminates the need to store long-lived AWS Access Keys in GitHub Secrets. The workflow assumes a specific AWS IAM Role (`{{ secrets.IAM_ROLE }}`) temporarily during the run.