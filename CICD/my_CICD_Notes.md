CI - It's a practice of regularly merging code into a shared repository and automatically building and testing it. The goal is just build and test which helps in early bug detection.

Continuous Delivery (CD) - CD ensures that the application is always in the deployable state and can be released anytime with MANUAL APPROVAL. The goal is to keep the app ready to release.

Continuous Deployment ( CD) - Extension of Delivery - Continuous deployment automatically deploys code in the production WITHOUT manual intervention if all tests pass. Fully automated pipeline for faster delivery. 

GitHub Actions, GitLab CI/CD, Argo CD, Jenkins, Harness, CircleCI, Tekton are some popular CICD tools.

In 2026, the ecosystem is dominated by GitHub Actions for SaaS and startups, GitLab CI for all-in-one platforms, Argo CD for Kubernetes deployments, and Jenkins for legacy enterprises.

CI/CD – Quick Notes 

🔹 What is CI/CD?
CI/CD = Continuous Integration + Continuous Delivery/Deployment
It is a pipeline that automates the process of building, testing, and deploying applications

1. Continuous Integration (CI)

Process of integrating code changes frequently. Ensures code is validated before delivery

Includes:

Code integration
Automated testing
Code validation checks

2. Continuous Delivery/Deployment (CD)

Process of delivering/deploying the application to a platform/environment. Makes the application available to users/customers

Overall CI/CD Flow
Application goes through:

Build
Test
Security Scan
Deploy


CI/CD Pipeline Stages
1. Unit Testing

Test individual functions or code blocks
Ensures small components work correctly


2. Static Code Analysis

Checks code quality without executing code
 Validates:

Syntax correctness
Coding standards (indentation, formatting)
Unused variables
Basic code issues


3. Code Quality & Vulnerability Testing

Identifies:

Security vulnerabilities
Weak coding practices


Protects against potential hacker exploits


4. Automation / Functional / End-to-End Testing

Tests complete application flow
Ensures:

All modules work together
One feature doesn’t break another

5. Reports

Provides insights like:

Unit tests passed/failed
Code quality score
Security issues found


6. Deployment

Deploys application to:

Cloud / server / platform


Makes application accessible to end users

Key Summary (1-minute revision)

CI = Build + Test + Validate code
CD = Deploy application to users
CI/CD = Automation pipeline for faster and reliable delivery
Improves:

Speed 
Quality 
Security 
Reliability

Jenkins uses a master-agent architecture where the master handles orchestration, scheduling, and configuration, while agents execute the jobs. This design is needed for scalability, distributed builds across environments, better resource utilization, improved security via isolation, and faster parallel execution.

Jenkins runs by-default on TCP port 8080 and default home directory is /var/jenkins_home on the filesystem where jenkins store all its data.

### Run Jenkins as Docker container

docker run -d -p 8081:8080 -p 5000:5000 -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
-- Exposes port 8081 on host and forwards traffic to jenkins container port 8080
-- Exposes port 5000 for connecting to agent or worker nodes by the master
-- creates a docker named volume "jenkins_home" and maps to the container's jenkins home folder where all the jenkins configs are stored.
-- jenkins/jenkins:lts is the docker image name

Docker agents can be used as worker agents to run CI/CD pipelines in isolated environments by leveraging specific container images tailored to application requirements. If a prebuilt image does not meet the needs, a custom image can be created using a Dockerfile as part of the pipeline, ensuring consistency and flexibility in the build process.

## Types of Projects in Jenkins:

1) Freestyle - This is used for simple, single tasks like "Run Tests", "Build the code". A complete pipeline is created by combining multiple freestyle projects.

2) Pipleine - This is used for whole delivery cycle eg : Test/Build/Package/Deploy for a SINGLE branch. Pipeline can be coded in YAML.

3) Multibranch Pipeline - Like pipeline but for multiple branches.

### What is GitHub Actions?

GitHub ACtions is GitHub's built-in automation platform used to automate software development workflows. It can greet a new contributer in the GitHub Project OR take some action when an issue is created in the Project. It can also automate the CICD workflow such as Build applications
Run tests
Code validation
Package creation
Docker image creation
Deployments
Notifications
Release management

Unlike Jenkins, you don't need a separate CI/CD server because GitHub Actions is integrated directly into GitHub.
A workflow is a YAML file that defines automation stored in .github/workflows/. Job definitions are defined under workflow which executes steps.
A workflow starts on an event like "on: push" and executes one or more jobs.
A job is a collection of steps which is executed on a built-in or Hosted runners. Common jobs are build,Test,Security Scan and Deploy.
A job can run either sequentially or run in parallel. 
Steps are smallest unit inside a job. Each step exceutes shell command or Reusable Action.
Action is reusable atomation component.Instead of writing everything yourself , GitHub provides lot of ready-made actions:
"uses: actions/checkout@v3"
Ex ready-made actions are 
Checkout repository
Setup Java
Setup NodeJS
Docker Login
Deploy to AWS

### WHy GitHub ACtions?

- Integrated with GitHub - No seperate CI/CD server required. Its free for open-source and public projects. Use same tool instead of 3rd party     	integration
- Easy Setup - Workflow is defined as YAML code
- Large Marketplace - Thousands of reusable ready-masde actions
- Version Controlled - Automation stored alongside application code
- Event Driven - Anything in GitHub can trigger workflows.

Typical Workflow File Structure:

name: Java CI with Maven -----> Workflow Name

on:
  push:  -----> On Trigger or Event
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build: -----> Defines work to be exceuted

    runs-on: ubuntu-latest -------> OS of the runner, ex: windows-latest, macos-latest

    steps: ------> Tasks executed in order
    - uses: actions/checkout@v4--------> Use Existing/Built-in action
    - name: Set up JDK 17
      uses: actions/setup-java@v4
      with:
        java-version: '17'
        distribution: 'temurin'
        cache: maven
    - name: Build with Maven
      run: mvn -B package --file pom.xml --------> Execute Shell command
    - name: Build and Push the Docker Image
      uses: mr-smithers-excellent/docker-build-push@v7
      with:
        image: ranjan973/hello-world-app
        tags: latest
        registry: docker.io
        username: ${{ secrets.DOCKER_USERNAME }} ------> Secrets stored under Setting/Secret in GitHub
        password: ${{ secrets.DOCKER_PASSWORD }}
		
In Summary:

GitHub Event
      ↓
Workflow (YAML)
      ↓
Job
      ↓
Step
      ↓
Action / Command

Workflow Location:
.github/workflows/

Important Keywords:
name
on
jobs
runs-on
steps
uses
run

Execution:
GitHub Runner

Secrets:
${{ secrets.NAME }}

Common CI Pipeline:
Code → Build → Test → Docker Build → Push → Deploy
