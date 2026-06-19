# CI/CD - Continuous Integration, Continuous Delivery & Continuous Deployment

## What is CI/CD?

**CI/CD** = **Continuous Integration + Continuous Delivery/Deployment**

It is a software development practice that automates the process of building, testing, and deploying applications, enabling faster and more reliable software delivery.

---

## Continuous Integration (CI)

Continuous Integration is the practice of regularly merging code into a shared repository and automatically building and testing it.

**Goal:** Build and test code frequently to detect bugs early.

### Includes

- Code integration
- Automated testing
- Code validation checks

### Benefits

- Early bug detection
- Faster feedback
- Improved code quality
- Reduced integration issues

---

## Continuous Delivery (CD)

Continuous Delivery ensures that the application is always in a deployable state and can be released at any time with **manual approval**.

**Goal:** Keep the application ready for release.

### Characteristics

- Automated build and testing
- Automated packaging
- Manual approval before production deployment

---

## Continuous Deployment (CD)

Continuous Deployment is an extension of Continuous Delivery.

If all tests pass, code is automatically deployed to production **without manual intervention**.

**Goal:** Fully automated software delivery.

### Characteristics

- No manual approval required
- Faster release cycles
- Fully automated deployment pipeline

---

## Popular CI/CD Tools

- GitHub Actions
- GitLab CI/CD
- Argo CD
- Jenkins
- Harness
- CircleCI
- Tekton

### Industry Trend (2026)

- **GitHub Actions** → Dominant for SaaS companies and startups
- **GitLab CI** → Popular all-in-one DevOps platform
- **Argo CD** → Preferred for Kubernetes deployments
- **Jenkins** → Widely used in legacy enterprise environments

---

# CI/CD Quick Notes

## What is CI/CD?

CI/CD is an automation pipeline that streamlines:

- Building applications
- Testing code
- Security validation
- Deploying applications

---

## Continuous Integration (CI)

Frequent integration of code changes into a shared repository.

### Purpose

Validate code before delivery.

### Activities

- Code integration
- Automated testing
- Code validation

---

## Continuous Delivery / Deployment (CD)

Delivering or deploying an application to a target environment so that it becomes available to users.

---

# Overall CI/CD Flow

Applications typically pass through the following stages:

```text
Code
  ↓
Build
  ↓
Test
  ↓
Security Scan
  ↓
Deploy
```

---

# CI/CD Pipeline Stages

## 1. Unit Testing

Tests individual functions or code blocks.

### Purpose

Ensure small components work correctly.

### Examples

- Validate a Java method
- Test a Python function
- Verify API responses

---

## 2. Static Code Analysis

Analyzes code without executing it.

### Checks

- Syntax correctness
- Coding standards
- Formatting issues
- Unused variables
- Common code defects

---

## 3. Code Quality & Vulnerability Testing

Identifies:

- Security vulnerabilities
- Weak coding practices
- Dependency risks

### Purpose

Protect the application from potential exploits and security threats.

---

## 4. Automation / Functional / End-to-End Testing

Validates complete application workflows.

### Ensures

- Modules work together correctly
- Features don't break existing functionality
- Real-world user flows work as expected

---

## 5. Reports

Provides visibility into pipeline results.

### Examples

- Unit test results
- Code coverage
- Quality scores
- Security findings
- Failed checks

---

## 6. Deployment

Deploys the application to:

- Cloud platforms
- Virtual machines
- Kubernetes clusters
- Physical servers

### Result

The application becomes accessible to end users.

---

# 1-Minute Revision

### CI

Build + Test + Validate Code

### CD

Deploy Application

### CI/CD

Automated pipeline that improves:

- Speed
- Quality
- Security
- Reliability

---

# Jenkins

## Jenkins Architecture

Jenkins uses a **Master-Agent Architecture**.

### Master Responsibilities

- Job orchestration
- Scheduling
- Configuration management
- Pipeline coordination

### Agent Responsibilities

- Execute jobs
- Run builds
- Run tests
- Perform deployments

### Why Master-Agent Architecture?

- Scalability
- Distributed builds
- Better resource utilization
- Security through isolation
- Faster parallel execution

---

## Jenkins Defaults

### Default Port

```text
8080
```

### Default Home Directory

```text
/var/jenkins_home
```

This directory stores:

- Jenkins configuration
- Plugins
- Build history
- Credentials
- Job definitions

---

## Run Jenkins as a Docker Container

```bash
docker run -d \
-p 8081:8080 \
-p 5000:5000 \
-v jenkins_home:/var/jenkins_home \
jenkins/jenkins:lts
```

### Explanation

#### `-p 8081:8080`

Maps:

```text
Host Port 8081
        ↓
Container Port 8080
```

Access Jenkins from:

```text
http://localhost:8081
```

---

#### `-p 5000:5000`

Exposes the Jenkins agent communication port.

Used by:

- Jenkins workers
- Build agents

---

#### `-v jenkins_home:/var/jenkins_home`

Creates a Docker named volume:

```text
jenkins_home
```

and mounts it to:

```text
/var/jenkins_home
```

This preserves Jenkins data even if the container is deleted.

---

#### `jenkins/jenkins:lts`

Official Long-Term Support Jenkins Docker image.

---

## Jenkins Docker Agents

Docker containers can be used as Jenkins worker agents.

### Benefits

- Isolated environments
- Consistent builds
- Language-specific build environments
- Easy scalability

### Options

Use:

- Prebuilt images
- Custom Docker images created with a Dockerfile

---

# Types of Jenkins Projects

## 1. Freestyle Project

Used for simple individual tasks.

### Examples

- Run tests
- Compile code
- Execute scripts

Multiple freestyle projects can be chained together to form a delivery process.

---

## 2. Pipeline Project

Used for an entire software delivery workflow.

### Example

```text
Build
  ↓
Test
  ↓
Package
  ↓
Deploy
```

Typically used for a single branch.

Pipeline definitions are written as code.

---

## 3. Multibranch Pipeline

Similar to a Pipeline Project but automatically discovers and builds multiple branches.

### Benefits

- One pipeline per branch
- Automatic branch discovery
- Better Git integration

---

# What is a Jenkinsfile?

A **Jenkinsfile** is a text file that defines a Jenkins Pipeline using code.

It enables:

- Pipeline as Code
- Version control of CI/CD processes
- Repeatable and consistent builds
- Easier collaboration among team members

Instead of configuring jobs manually in the Jenkins UI, pipelines are stored in a repository alongside application source code. 【1-400332】

---

# Why Jenkinsfile?

## Traditional Approach
- Jenkins jobs configured manually through UI
- Difficult to track changes
- Hard to migrate between Jenkins instances
- No version history

## Jenkinsfile Approach
- Pipeline stored in Git
- Easy to review and track changes
- Infrastructure and CI/CD process managed as code
- Same pipeline can be reused across environments

---

# Jenkins Pipeline Structure

A Jenkins Pipeline is composed of:

```
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                // commands
            }
        }

        stage('Test') {
            steps {
                // commands
            }
        }

        stage('Deploy') {
            steps {
                // commands
            }
        }
    }
}
```

---

# Main Jenkinsfile Components

## 1. Pipeline

Top-level block.

```
pipeline {
}
```

This defines the entire CI/CD workflow.

---

## 2. Agent

Specifies where the pipeline runs.

```
agent any
```

Meaning:
- Jenkins can execute on any available agent/node.

Examples:

```
agent any
```

```
agent {
    label 'linux'
}
```

---

## 3. Stages

Stages represent major phases of the pipeline.

Examples:

- Build
- Test
- Package
- Deploy

```
stages {
}
```

---

## 4. Stage

Defines an individual phase.

```
stage('Build')
```

Example:

```
stage('Build') {
    steps {
        echo 'Building application'
    }
}
```

---

## 5. Steps

Actual commands executed inside a stage.

```
steps {
}
```

Examples:

```
echo 'Hello World'
```

```
sh 'mvn package'
```

```
sh 'npm install'
```

---

# Example Jenkinsfile

```
pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo 'Building application...'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
            }
        }
    }
}
```

Execution Flow:

```text
Build
  ↓
Test
  ↓
Deploy
```

---

# Declarative vs Scripted Pipeline

The video explains two Jenkinsfile styles. 【1-400332】

## Declarative Pipeline

Most commonly used.

Example:

```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Build'
            }
        }
    }
}
```

### Advantages
- Easier to read
- Structured syntax
- Better for beginners
- Less error-prone

---

## Scripted Pipeline

Uses Groovy syntax and programming constructs.

Example:

```groovy
node {
    stage('Build') {
        echo 'Build'
    }
}
```

### Advantages
- More flexibility
- Supports advanced logic
- Useful for complex workflows

### Disadvantages
- Harder to read
- Steeper learning curve

---

# Useful Jenkinsfile Commands

## Echo

Print message in Jenkins console.

```groovy
echo "Application Build Started"
```

---

## Shell Command

Execute Linux command.

```groovy
sh "ls -la"
```

```groovy
sh "mvn package"
```

---

# Replay Feature

Jenkins provides a **Replay** feature. 【1-400332】

Purpose:

- Modify Jenkinsfile temporarily
- Test changes quickly
- No need to commit to Git for every small test

Benefits:

- Faster experimentation
- Easier troubleshooting
- Useful during pipeline development

Note:
- Changes made through Replay are not automatically saved to Git.

---

# Restart from Stage

Jenkins allows restarting the pipeline from a specific stage. 【1-400332】

Example:

Pipeline stages:

```text
Build
Test
Deploy
```

If deployment fails:

- No need to rerun Build and Test
- Restart directly from Deploy stage

Benefits:

- Saves time
- Reduces resource consumption
- Faster troubleshooting

---

# Best Practices

## Keep Jenkinsfile in Git

Store alongside application code.

```text
project/
 ├─ src/
 ├─ pom.xml
 └─ Jenkinsfile
```

---

## Use Meaningful Stage Names

Good:

```groovy
stage('Build')
stage('Unit Tests')
stage('Deploy to Dev')
```

Avoid:

```groovy
stage('Step1')
stage('Step2')
```

---

## Keep Stages Small

Prefer:

```text
Build
Test
Package
Deploy
```

Instead of one huge stage containing everything.

---

## Version Control Everything

Track:
- Source code
- Build process
- Deployment process
- Pipeline changes

---

# Interview Questions

### What is a Jenkinsfile?
A text file that defines a Jenkins Pipeline using code and can be stored in version control.

### What is Pipeline as Code?
Managing CI/CD workflows through code instead of manual UI configuration.

### What are the main sections of a Jenkinsfile?
- pipeline
- agent
- stages
- stage
- steps

### Difference between Declarative and Scripted Pipeline?

| Declarative | Scripted |
|------------|------------|
| Structured syntax | Groovy-based |
| Easier to learn | More flexible |
| Recommended for most cases | Suitable for advanced use cases |

### What is Replay in Jenkins?
A feature to test Jenkinsfile modifications without changing Git repository files.

### What is Restart from Stage?
Ability to rerun a failed pipeline starting from a specific stage instead of running the entire pipeline again.

---

# Quick Revision Notes

✅ Jenkinsfile = Pipeline as Code

✅ Stored in Git repository

✅ Main blocks:

```text
pipeline
 ├── agent
 └── stages
      ├── stage
      │     └── steps
```

✅ Declarative Pipeline → easier, recommended

✅ Scripted Pipeline → flexible, advanced

✅ Replay → test Jenkinsfile changes quickly

✅ Restart from Stage → rerun only failed stages

✅ Jenkinsfile should live with application code in the same repository


# GitHub Actions

## What is GitHub Actions?

GitHub Actions is GitHub's built-in automation platform.

It can automate:

- CI/CD pipelines
- Issue management
- Pull request workflows
- Notifications
- Release management

### Examples

- Welcome new contributors
- Run tests
- Build applications
- Validate code
- Create Docker images
- Deploy applications

---

## Why GitHub Actions?

### Integrated with GitHub

No separate CI/CD server required.

---

### Easy Setup

Workflows are defined using YAML.

---

### Large Marketplace

Thousands of reusable actions are available.

---

### Version Controlled

Workflow code is stored alongside application code.

---

### Event Driven

GitHub events automatically trigger workflows.

Examples:

- Push
- Pull Request
- Tag Creation
- Issue Creation
- Release Creation

---

# Core Components of GitHub Actions

## Workflow

A workflow is a YAML file stored under:

```text
.github/workflows/
```

It defines the automation process.

---

## Event

An activity that triggers the workflow.

Examples:

```yaml
on:
  push:
  pull_request:
```

---

## Job

A group of steps executed on a runner.

Common jobs:

- Build
- Test
- Security Scan
- Deploy

Jobs can run:

- Sequentially
- In Parallel

---

## Step

The smallest execution unit in a job.

A step can:

- Run shell commands
- Execute reusable actions

---

## Action

Reusable automation components.

Instead of writing everything manually, you can use prebuilt actions.

Example:

```yaml
uses: actions/checkout@v4
```

Popular Actions:

- Checkout repository
- Setup Java
- Setup NodeJS
- Docker Login
- AWS Deployment

---

# GitHub Actions Workflow Structure

```yaml
name: Java CI with Maven

on:
  push:
    branches: ["main"]

  pull_request:
    branches: ["main"]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: "17"
          distribution: "temurin"
          cache: maven

      - name: Build with Maven
        run: mvn -B package --file pom.xml

      - name: Build and Push the Docker Image
        uses: mr-smithers-excellent/docker-build-push@v7
        with:
          image: ranjan973/hello-world-app
          tags: latest
          registry: docker.io
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}
```

---

# Workflow Breakdown

## Workflow Name

```yaml
name: Java CI with Maven
```

Defines the workflow name displayed in GitHub Actions.

---

## Trigger

```yaml
on:
  push:
    branches: ["main"]
```

Runs when code is pushed to the main branch.

---

## Job Definition

```yaml
jobs:
  build:
```

Defines a job named `build`.

---

## Runner

```yaml
runs-on: ubuntu-latest
```

Specifies the operating system.

Examples:

- ubuntu-latest
- windows-latest
- macos-latest

---

## Steps

```yaml
steps:
```

Tasks executed in sequence.

---

## Reusable Action

```yaml
uses: actions/checkout@v4
```

Uses a built-in GitHub Action.

---

## Execute Command

```yaml
run: mvn -B package --file pom.xml
```

Executes a shell command.

---

## Secrets

```yaml
${{ secrets.DOCKER_USERNAME }}
```

Sensitive values stored securely in GitHub.

Location:

```text
Settings → Secrets and Variables → Actions
```

---

# Execution Flow

```text
GitHub Event
      ↓
Workflow (YAML)
      ↓
Job
      ↓
Step
      ↓
Action / Command
```

---

# Important GitHub Actions Keywords

```text
name
on
jobs
runs-on
steps
uses
run
```

---

# Workflow Location

```text
.github/workflows/
```

---

# Common CI Pipeline

```text
Code
  ↓
Build
  ↓
Test
  ↓
Docker Build
  ↓
Push
  ↓
Deploy
```
