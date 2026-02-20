# Lab 3 Submission - GitHub Actions Workflows

## Platform: GitHub Actions

## Task 1 — First GitHub Actions Workflow

### 1.1 Quickstart Implementation Observations

**Steps Followed:**
1. Created `.github/workflows` directory in the repository
2. Created `github-actions-demo.yml` workflow file
3. Defined basic workflow with `push` trigger
4. Added jobs with multiple steps for printing information
5. Committed and pushed changes to trigger the workflow

**Key Concepts Learned:**
- **Jobs**: Units of work that run on the same runner (my workflow had one job)
- **Steps**: Individual tasks within a job (multiple echo commands, checkout action)
- **Runners**: GitHub-hosted Linux server that executes the workflow
- **Triggers**: Events that start workflow execution (push in this case)
- **Actions**: Reusable units of code (like `actions/checkout@v5`)

### 1.2 Test Workflow Trigger

**Successful Run Link:**
[First Workflow Run - Push Trigger](https://github.com/group06854/DevOps-Intro/actions/runs/22184793461)

**What Caused the Run:**
The workflow was triggered automatically by a `git push` event to the `feature/lab3` branch. When I committed and pushed the workflow YAML file, GitHub detected the changes and initiated the workflow.

**Workflow Execution Analysis:**
1. **Trigger Phase**: Push event detected by GitHub
2. **Setup Phase**: GitHub provisioned a Linux runner
3. **Execution Phase**:
   - Step 1: Echoed trigger information (identified as push event)
   - Step 2: Displayed runner type (Linux server hosted by GitHub)
   - Step 3: Showed branch name and repository details
   - Step 4: `actions/checkout@v5` cloned the repository
   - Step 5: Confirmed repository cloning
   - Step 6: Listed repository contents
   - Step 7: Final status message
4. **Completion Phase**: Job marked as successful

## Task 2 — Manual Trigger + System Information

### 2.1-2.2 Manual Trigger Implementation

**Changes Made to Workflow File:**
Added `workflow_dispatch` trigger alongside the existing push trigger:

```yaml
name: GitHub Actions Demo
run-name: ${{ github.actor }} is testing out GitHub Actions 🚀
on: 
  workflow_dispatch:
```
### 2.3 System Information Collection

**Successful Manual Dispatch Run:**
[Second Workflow Run - Manual Trigger](https://github.com/group06854/DevOps-Intro/actions/runs/22221110750/job/64276486743)

**Gathered System Information:**\
--- RUNNER INFORMATION ---
Runner name: GitHub Actions 1000000267
Runner OS: Linux
Runner architecture: X64

--- OPERATING SYSTEM DETAILS ---
PRETTY_NAME="Ubuntu 24.04.3 LTS"
NAME="Ubuntu"
VERSION_ID="24.04"
VERSION="24.04.3 LTS (Noble Numbat)"
VERSION_CODENAME=noble
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=noble
LOGO=ubuntu-logo

--- HARDWARE SPECIFICATIONS ---
CPU Model: AMD EPYC 7763 64-Core Processor
CPU Cores: 4
CPU Architecture: x86_64
Total Memory: 15Gi
Available Memory: 14Gi
Disk Size: 145G
Disk Usage: 37%

### Comparison: Manual vs Automatic Workflow Triggers

| Aspect | Automatic (Push) | Manual (workflow_dispatch) |
|--------|------------------|----------------------------|
| **Trigger Method** | Code push to repository | Manual click in GitHub UI |
| **Use Case** | Continuous integration on code changes | On-demand testing, debugging |
| **Flexibility** | Fixed to specific branches | Can choose branch at runtime |
| **Parameters** | Limited to event data | Can accept input parameters |
| **When to Use** | Automated testing, deployment | Manual testing, scheduled runs |

## Summary

This lab demonstrated the fundamentals of GitHub Actions including:
- Basic workflow configuration with push triggers
- Manual workflow triggers via workflow_dispatch
- System information collection and analysis
- Understanding of runner environments

The comparison between automatic and manual triggers shows the flexibility of GitHub Actions for different CI/CD scenarios, while the system information reveals the robust, consistent environment provided by GitHub-hosted runners.