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


