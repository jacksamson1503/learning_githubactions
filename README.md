# GitHub Actions - Learning Notes

## Table of Contents

### Day 1 - GitHub Actions Fundamentals

* What is GitHub Actions?
* Basic Workflow Structure
* Workflow Structure

  * name
  * on
  * jobs
* Workflow Triggers

  * workflow_dispatch
  * push
  * pull_request
  * schedule
* Multiple Triggers
* Filters

  * Branch Filter
  * Path Filter
  * Ignore Paths
* Runners
* Steps
* run
* uses
* run vs uses
* actions/checkout
* fetch-depth
* Conditions (if)
* Variables (`$GITHUB_EVENT_NAME`)

---

### Day 2 - Building Real CI Pipelines

* Environment Variables (env)
* Variable Scopes

  * Workflow Level
  * Job Level
  * Step Level
* Contexts

  * github
  * runner
  * env
  * secrets
  * vars
  * needs
* Secrets and Variables
* CI Pipeline
* Parallel Jobs
* Job Dependencies (needs)
* Conditional Execution (if)
* Status Functions

  * success()
  * failure()
  * always()
  * cancelled()
* Day 2 Workflow Flow
* Interview Questions

---

# Day 1 - GitHub Actions Fundamentals

# GitHub Actions - Learning Notes

## What is GitHub Actions?

GitHub Actions is a CI/CD and automation platform provided by GitHub. It allows you to automate tasks such as building, testing, and deploying applications whenever specific events occur in a repository.

---

# Basic Workflow Structure

```yaml
name: Hello World

on:
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Print Hello
        run: echo "Hello World"

      - name: Print Multiple Lines
        run: |
          echo "This is sample line 1"
          echo "This is sample line 2"
```

---

# Workflow Structure

Every GitHub Actions workflow contains the following sections:

```yaml
name:
on:
jobs:
```

### name

Defines the workflow name displayed in the GitHub Actions tab.

```yaml
name: Hello World
```

### on

Defines the event that triggers the workflow.

```yaml
on:
  workflow_dispatch:
```

### jobs

Contains one or more jobs that will be executed.

```yaml
jobs:
  build:
```

---

# Workflow Triggers

## workflow_dispatch

Manual trigger.

```yaml
on:
  workflow_dispatch:
```

The workflow starts when you click **Run Workflow** in the Actions tab.

---

## push

Automatic trigger when code is pushed.

```yaml
on:
  push:
```

Example:

```bash
git push origin main
```

---

## pull_request

Automatic trigger when a Pull Request is created, updated, or reopened.

```yaml
on:
  pull_request:
```

Used for code validation before merging.

---

## schedule

Runs workflows based on a cron schedule.

```yaml
on:
  schedule:
    - cron: '0 9 * * *'
```

Example:

* Daily reports
* Automated backups
* Security scans

---

## Multiple Triggers

A single workflow can listen to multiple events.

```yaml
on:
  push:
    branches: [main]

  pull_request:
    branches: [main]

  workflow_dispatch:
```

Workflow runs when any of these events occur.

---

# Filters

Filters control when workflows should run.

## Branch Filter

Run only for specific branches.

```yaml
on:
  push:
    branches:
      - main
```

---

## Path Filter

Run only when specific files or folders change.

```yaml
on:
  push:
    paths:
      - src/**
```

---

## Ignore Paths

Ignore specific files or folders.

```yaml
on:
  push:
    paths-ignore:
      - docs/**
```

---

# Runners

A runner is the machine that executes a job.

GitHub provides hosted runners:

* Ubuntu Linux
* Windows
* macOS

Example:

```yaml
runs-on: ubuntu-latest
```

```yaml
runs-on: windows-latest
```

```yaml
runs-on: macos-latest
```

---

# Steps

A job contains one or more steps.

```yaml
steps:
```

Example:

```yaml
steps:
  - name: Print Message
    run: echo "Hello"
```

---

# run

Used to execute shell commands on the runner.

```yaml
run: echo "Hello World"
```

Example:

```yaml
run: ls -la
```

---

# uses

Used to execute reusable GitHub Actions.

```yaml
uses: actions/checkout@v5
```

Example:

```yaml
uses: actions/setup-node@v4
```

---

# run vs uses

## run

Executes commands directly on the runner.

```yaml
run: echo "Hello World"
```

## uses

Executes a pre-built GitHub Action.

```yaml
uses: actions/checkout@v5
```

A step can contain either `run` or `uses`, but not both.

---

# actions/checkout

The runner starts as a fresh virtual machine.

Repository files are not available by default.

```yaml
uses: actions/checkout@v5
```

This action downloads the repository code to the runner.

Almost every CI/CD workflow begins with this step.

---

# fetch-depth

Controls how much Git history is downloaded during checkout.

## fetch-depth: 1

```yaml
with:
  fetch-depth: 1
```

Downloads only the latest commit.

---

## fetch-depth: 2

```yaml
with:
  fetch-depth: 2
```

Downloads the last two commits.

---

## fetch-depth: 0

```yaml
with:
  fetch-depth: 0
```

Downloads the complete Git history.

---

# Conditions

Use `if` to run steps only when a condition is true.

```yaml
if: github.event_name == 'push'
```

Example:

```yaml
- name: Deploy Application
  if: github.event_name == 'push'
  run: echo "Deploying application"
```

---

# Variables

GitHub provides built-in variables.

## GITHUB_EVENT_NAME

Displays the event that triggered the workflow.

```yaml
run: echo "$GITHUB_EVENT_NAME"
```

Examples:

```text
push
pull_request
workflow_dispatch
```

---

# Summary

Topics Learned:

* Workflow Structure
* Triggers

  * workflow_dispatch
  * push
  * pull_request
  * schedule
* Multiple Triggers
* Filters
* Runners
* Steps
* run
* uses
* actions/checkout
* fetch-depth
* Conditions (`if`)
* Variables (`$GITHUB_EVENT_NAME`)

These are the fundamental building blocks of GitHub Actions and form the foundation for creating CI/CD pipelines.


---

# Day 2 - Building Real CI Pipelines


---

## Environment Variables (env)

Environment variables are reusable values that can be used throughout a workflow.

Example:

```yaml
env:
  APP_NAME: Netflix
```

Environment variables help avoid repeating the same values multiple times in a workflow.

### Variable Scopes

GitHub Actions supports three levels of environment variables.

#### Workflow Level

Available to all jobs and steps.

```yaml
env:
  APP_NAME: Netflix
```

#### Job Level

Available only inside a specific job.

```yaml
jobs:
  build:
    env:
      APP_NAME: Amazon
```

#### Step Level

Available only inside a specific step.

```yaml
steps:
  - name: Test
    env:
      APP_NAME: Flipkart
```

### Scope Priority

```text
Step Level
   ↓
Job Level
   ↓
Workflow Level
```

The closest scope always overrides the outer scope.

---

## Contexts

Contexts are built-in objects that provide information about the workflow, repository, runner, and GitHub event.

Syntax:

```yaml
${{ context.property }}
```

### Common Contexts

#### github

Repository and workflow information.

```yaml
${{ github.actor }}
${{ github.repository }}
${{ github.ref }}
${{ github.sha }}
```

#### runner

Information about the runner.

```yaml
${{ runner.os }}
${{ runner.arch }}
```

#### env

Reads environment variables.

```yaml
${{ env.APP_NAME }}
```

#### secrets

Reads repository secrets.

```yaml
${{ secrets.API_KEY }}
```

#### vars

Reads repository variables.

```yaml
${{ vars.COMPANY_NAME }}
```

#### needs

Reads information from dependent jobs.

```yaml
${{ needs.build.result }}
```

### Debugging Contexts

```yaml
- env:
    GITHUB_CONTEXT: ${{ toJSON(github) }}
  run: echo "$GITHUB_CONTEXT"
```

Useful for viewing all available values inside a context.

---

## Secrets and Variables

Sensitive information should never be hardcoded inside workflow files.

Wrong:

```yaml
PASSWORD=12345
```

Correct:

Store credentials inside GitHub Secrets.

### Creating Secrets

```text
Repository
↓
Settings
↓
Secrets and Variables
↓
Actions
↓
New Repository Secret
```

Example:

```text
AWS_ACCESS_KEY
```

Usage:

```yaml
env:
  AWS_KEY: ${{ secrets.AWS_ACCESS_KEY }}
```

Secrets are automatically masked in workflow logs.

### Repository Variables

Variables are used for non-sensitive values.

Example:

```text
COMPANY_NAME=Netflix
```

Usage:

```yaml
${{ vars.COMPANY_NAME }}
```

### Secrets vs Variables

| Feature        | Secrets | Variables |
| -------------- | ------- | --------- |
| Encrypted      | Yes     | No        |
| Hidden in Logs | Yes     | No        |
| Passwords      | Yes     | No        |
| URLs           | No      | Yes       |
| API Keys       | Yes     | No        |

---

## CI Pipeline

CI stands for Continuous Integration.

The purpose of CI is to automatically validate code whenever developers push changes.

### Typical CI Flow

```text
Developer Pushes Code
        ↓
Checkout Source Code
        ↓
Install Dependencies
        ↓
Lint Code
        ↓
Run Tests
        ↓
Pass or Fail
```

### Example Workflow

```yaml
name: Node CI

on:
  push:
    branches:
      - main

jobs:
  build-and-test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v5

      - uses: actions/setup-node@v4
        with:
          node-version: 20

      - run: npm install

      - run: npm run lint

      - run: npm test
```

---

## Parallel Jobs

By default, jobs without dependencies run simultaneously.

Example:

```yaml
jobs:
  build:
  test:
  security:
```

Execution:

```text
         Event
           │
 ┌─────────┼─────────┐
 │         │         │
Build     Test    Security
```

### Important Points

* Each job runs on a separate runner.
* Jobs do not share files.
* Jobs do not share installed software.
* Jobs do not share environment variables.

---

## Job Dependencies (needs)

The `needs` keyword creates dependencies between jobs.

Example:

```yaml
test:
  needs: build
```

Execution:

```text
Build
  ↓
Test
```

### Multiple Dependencies

```yaml
deploy:
  needs:
    - test
    - security
```

Execution:

```text
Build
 ↓
 ┌───────┬───────┐
 │       │       │
Test  Security
 │       │
 └───┬───┘
     ↓
 Deploy
```

### Key Points

* A job waits until all dependencies finish.
* Failed dependencies cause downstream jobs to be skipped.
* Circular dependencies are not allowed.

---

## Conditional Execution (if)

The `if` condition controls whether a job or step should run.

Example:

```yaml
if: github.ref == 'refs/heads/main'
```

Runs only on the main branch.

### Operators

```yaml
==
!=
&&
||
!
```

### Common Functions

```yaml
contains()
startsWith()
endsWith()
```

Example:

```yaml
if: github.event_name == 'push' && github.ref == 'refs/heads/main'
```

Meaning:

```text
Push Event
AND
Main Branch
```

Only then execute.

---

## Status Functions

Status functions allow workflows to react based on previous job results.

### success()

Runs when all previous jobs succeed.

```yaml
if: success()
```

### failure()

Runs when a previous job fails.

```yaml
if: failure()
```

Example:

```text
Build Failed
↓
Send Notification
```

### always()

Runs regardless of success or failure.

```yaml
if: always()
```

Example:

```text
Upload Logs
Cleanup Resources
Destroy Infrastructure
```

### cancelled()

Runs only when a workflow is cancelled.

```yaml
if: cancelled()
```

### Default Behavior

Every job and step automatically has:

```yaml
if: success()
```

This is why downstream jobs become skipped when dependencies fail.

---

## Day 2 Workflow Flow

```text
Push Code
    ↓
Trigger Workflow
    ↓
Read Variables (env)
    ↓
Read Contexts
    ↓
Read Secrets
    ↓
Checkout Code
    ↓
Install Dependencies
    ↓
Run Lint
    ↓
Run Tests
    ↓
Execute Parallel Jobs
    ↓
Apply Dependencies (needs)
    ↓
Evaluate Conditions (if)
    ↓
Check Status Functions
    ↓
Deploy Application
```

---



---

# GitHub Actions - Learning Summary (12-Aug-2026)

Today I covered and practiced the following advanced GitHub Actions concepts.

---

## 1. Job Outputs

### Purpose
Used to pass small values from one job to another job.

### Common Use Cases
- Version Number
- Docker Image Tag
- Build URL
- Timestamp
- Application Name

### Flow

```text
Job 1
  ↓
Create Output
  ↓
Job 2
  ↓
Use Output
```

### Key Points
- Small values only
- Uses `$GITHUB_OUTPUT`
- Step must have an `id`
- Job must define `outputs`
- Access using `needs.<job>.outputs.<value>`

---

## 2. Matrix Strategy

### Purpose
Run the same job multiple times with different values.

### Example

```text
NodeJS 18
NodeJS 20
NodeJS 22
```

### Flow

```text
One Job
   ↓
Multiple Runs

Run 1 → Node 18
Run 2 → Node 20
Run 3 → Node 22
```

### Real Use Cases
- Multiple NodeJS versions
- Multiple Python versions
- Multiple Operating Systems
- Multiple Environments

---

## 3. Cache

### Purpose
Store dependencies and reuse them in future workflow runs.

### Flow

```text
Run 1
 ↓
Download Packages
 ↓
Save Cache

Run 2
 ↓
Use Cache
 ↓
Faster Execution
```

### Benefits
- Faster Builds
- Reduced Download Time
- Better Runner Efficiency

---

## 4. Artifacts

### Purpose
Transfer files between jobs.

### Common Use Cases
- Build Files
- ZIP Files
- Test Reports
- Logs
- Application Packages

### Flow

```text
Job 1
 ↓
Upload Artifact
 ↓
GitHub Storage
 ↓
Job 2
 ↓
Download Artifact
```

### Difference

```text
Outputs   → Small Values
Artifacts → Files
```

---

## 5. Reusable Workflows

### Purpose
Create a workflow once and reuse it across multiple repositories or workflows.

### Flow

```text
Main Workflow
      ↓
Reusable Workflow
```

### Benefits
- Less Duplicate Code
- Easy Maintenance
- Standardized Pipelines

---

## 6. Environments & Approvals

### Purpose
Protect deployments using manual approvals.

### Example

```text
Production Environment
       ↓
Approval Required
       ↓
Deploy
```

### Benefits
- Production Protection
- Approval Before Deployment
- Prevent Accidental Deployments

---

## 7. Concurrency

### Purpose
Cancel older workflow runs and keep only the latest workflow running.

### Flow

```text
Push 1 → Running

Push 2 → Cancel Push 1

Push 3 → Cancel Push 2

Push 3 Continues
```

### Benefits
- Avoid Duplicate Deployments
- Save Runner Time
- Deploy Latest Code Only

---

## 8. Self-Hosted Runner

### Purpose
Run workflows on company-owned servers instead of GitHub-hosted runners.

### Flow

```text
GitHub
   ↓
Company Server
   ↓
Run Workflow
```

### Benefits
- Access Internal Resources
- Private Network Connectivity
- Full Infrastructure Control

---

## 9. Token Permissions

### Purpose
Control what the workflow's `GITHUB_TOKEN` can access.

### Examples

```yaml
permissions:
  contents: read
```

```text
Read Repository Only
```

```yaml
permissions:
  contents: write
```

```text
Read + Modify Repository
```

### Benefits
- Improved Security
- Least Privilege Access

---

## 10. Timeout

### Purpose
Automatically stop long-running jobs.

### Example

```yaml
timeout-minutes: 10
```

### Flow

```text
Job Running
     ↓
10 Minutes Reached
     ↓
Job Stopped
```

### Benefits
- Prevents Stuck Jobs
- Saves Runner Resources

---

## 11. Continue-on-Error

### Purpose
Continue the workflow even if a step fails.

### Flow

```text
Step 1 Success
      ↓
Step 2 Failed
      ↓
Continue Workflow
      ↓
Step 3 Runs
```

### Benefits
- Ignore Non-Critical Failures
- Workflow Continues Execution

---

# Key Concepts Learned Today

| Topic | Purpose |
|---------|---------|
| Job Outputs | Pass small values between jobs |
| Matrix | Run same job multiple times |
| Cache | Speed up workflow execution |
| Artifacts | Transfer files between jobs |
| Reusable Workflows | Reuse workflow logic |
| Environments | Approval before deployment |
| Concurrency | Cancel old workflow runs |
| Self-Hosted Runners | Run on company servers |
| Permissions | Control token access |
| Timeout | Stop long-running jobs |
| Continue-on-Error | Continue after failure |

---

## Learning Status

✅ Job Outputs

✅ Matrix Strategy

✅ Cache

✅ Artifacts

✅ Reusable Workflows

✅ Environments & Approvals

✅ Concurrency

✅ Self-Hosted Runners

✅ Token Permissions

✅ Timeout

✅ Continue-on-Error

---

### GitHub Actions Progress



