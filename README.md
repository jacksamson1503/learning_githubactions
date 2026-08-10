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
