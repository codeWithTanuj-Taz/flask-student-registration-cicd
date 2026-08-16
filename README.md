# Flask Student Registration System – End-to-End CI/CD Pipeline on AWS

![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-blue)
![Docker](https://img.shields.io/badge/Container-Docker-blue)
![AWS](https://img.shields.io/badge/Cloud-AWS-orange)
![Python](https://img.shields.io/badge/Python-3.14-blue)
![Flask](https://img.shields.io/badge/Framework-Flask-black)
![MongoDB](https://img.shields.io/badge/Database-MongoDB%20Atlas-green)



# 1. Project Overview

This project implements a complete CI/CD pipeline for a Python Flask Student Registration System.

The application allows users to:

- View registered students
- Add students
- Update students
- Delete students
- Verify application and MongoDB connectivity

Student information is stored in MongoDB Atlas.

The application is packaged as a Docker image, pushed to Amazon Elastic Container Registry (Amazon ECR), and deployed to an Ubuntu-based Amazon EC2 instance.

GitHub Actions automates the CI/CD process from source-code push through deployment and health verification.

## Overall Flow

```text
Developer
    |
    | git push
    v
GitHub Repository
    |
    v
GitHub Actions
    |
    +---- Test
    |
    +---- Quality Check
    |
    +---- Docker Build
    |
    +---- Push Docker Image to ECR
    |
    +---- Deploy to EC2
    |
    +---- Health Check
    |
    v
Flask Application
    |
    v
MongoDB Atlas
```

---

# 2. Project Objectives

The project was designed to demonstrate the following:

1. Flask application development
2. MongoDB Atlas integration
3. Automated pytest testing
4. Code-quality validation using Black
5. Docker containerization
6. Amazon ECR image management
7. Amazon EC2 deployment
8. GitHub Actions CI/CD
9. GitHub OIDC authentication with AWS
10. IAM-based access control
11. Git SHA-based Docker image tagging
12. Immutable ECR image tags
13. Automated container replacement
14. Deployment health verification
15. Secure secret management
16. Success/failure pipeline notifications
17. End-to-end cloud deployment

---

# 3. Architecture

```text
                         +----------------------+
                         |      Developer       |
                         +----------+-----------+
                                    |
                                    | git push
                                    v
                         +----------------------+
                         |       GitHub         |
                         |      main branch     |
                         +----------+-----------+
                                    |
                                    v
                         +----------------------+
                         |   GitHub Actions     |
                         +----------+-----------+
                                    |
                    +---------------+---------------+
                    |               |               |
                    v               v               v
                 Pytest           Black        Docker Build
                    |               |               |
                    +---------------+---------------+
                                    |
                                    v
                         +----------------------+
                         |      Amazon ECR      |
                         | flask-student-app    |
                         +----------+-----------+
                                    |
                                    | docker pull
                                    v
                         +----------------------+
                         |      Amazon EC2      |
                         |       Ubuntu         |
                         |       Docker         |
                         +----------+-----------+
                                    |
                                    | :5000
                                    v
                         +----------------------+
                         | Flask Student App    |
                         +----------+-----------+
                                    |
                                    | MONGO_URI
                                    v
                         +----------------------+
                         |    MongoDB Atlas     |
                         +----------------------+
```

---

# 5. Application Features

## Application Routes

| Route | Method | Purpose |
|---|---|---|
| `/` | GET | Display all students |
| `/add` | GET/POST | Add a student |
| `/update/<student_id>` | GET/POST | Update a student |
| `/delete/<student_id>` | GET | Delete a student |
| `/health` | GET | Check Flask and MongoDB connectivity |

## Health Endpoint

The `/health` endpoint performs a MongoDB ping.

Successful response:

```json
{
  "status": "healthy",
  "mongodb": "connected"
}
```

Unsuccessful response:

```json
{
  "status": "unhealthy",
  "mongodb": "disconnected"
}
```

The health endpoint is used as the final deployment verification gate.

---

# 6. Project Structure

```text
flask-student-app/
│
├── app.py
├── Dockerfile
├── requirements.txt
├── test_app.py
├── .dockerignore
├── .gitignore
├── README.md
│
├── templates/
│   ├── index.html
│   ├── add_student.html
│   └── update_student.html
│
└── .github/
    └── workflows/
        └── ci-cd.yml
```

---

# 8. Clone the Repository to VS code(local)

```bash
git clone https://github.com/codeWithTanuj-Taz/flask_Practice
cd flask-student-app
```
### Screenshot
<img width="1888" height="939" alt="Screenshot 2026-08-16 015549" src="https://github.com/user-attachments/assets/9c05e8ac-69ba-4b2f-a896-3d616fa8d7c0" />

git status
<img width="1008" height="882" alt="Screenshot 2026-08-16 015822" src="https://github.com/user-attachments/assets/40249045-4d2e-465e-a746-c1c091417d9d" />

The same GitHub repository can be updated later. 

---

# 9. Run the Flask Application Locally

## 9.1 Create Virtual Environment

Windows PowerShell:

```powershell
python -m venv venv
```

Activate:

```powershell
.\venv\Scripts\Activate.ps1
```

Linux:

```bash
python3 -m venv venv
source venv/bin/activate
```

## 9.2 Install Dependencies

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

## 9.3 Configure Environment Variables

Create a local `.env` file for development:

```text
MONGO_URI=<YOUR_MONGODB_URI>
SECRET_KEY=<YOUR_SECRET_KEY>
```

Do not commit `.env`.

## 9.4 Start Flask

```bash
python app.py
```

Application:

```text
http://localhost:5000
```

## 9.5 Test Health

```bash
curl http://localhost:5000/health
```

---

# 10. MongoDB Atlas Setup

MongoDB Atlas provides the cloud database.

## Step 1 – Create Cluster

1. Log in to MongoDB Atlas.
2. Create a cluster.
3. Wait until the cluster is available.

> During development, the cluster was found to be paused. The cluster was resumed before deployment testing.

## Step 2 – Create Database User

Create a MongoDB database user.

Store the username and password securely.

## Step 3 – Configure Network Access

Allow the EC2 application to connect to MongoDB Atlas.

For production, use the most restrictive network configuration possible.

## Step 4 – Generate Connection String

General format:

```text
mongodb+srv://<username>:<password>@<cluster>.mongodb.net/<database>?appName=<application-name>
```

Example structure:

```text
mongodb+srv://USERNAME:PASSWORD@travelmemory01.rgptdx3.mongodb.net/student_db?appName=Travelmemory01
```

The password must be URL-safe/URL-encoded if it contains characters requiring encoding.

## Step 5 – Store URI

Store the complete URI as:

```text
MONGO_URI
```

in GitHub Secrets.

## MongoDB Verification

The deployed container was tested directly with PyMongo:

```bash
sudo docker exec flask-student-app python -c \
"import os; from pymongo import MongoClient; MongoClient(os.environ['MONGO_URI'], serverSelectionTimeoutMS=10000).admin.command('ping'); print('MongoDB authentication successful')"
```

Expected:

```text
MongoDB authentication successful
```

---

# 11. Docker Configuration

The Flask application is containerized using Docker.

The application listens on port:

```text
5000
```

## Build Image

```bash
docker build -t flask-student-app .
```

## Run Container

```bash
docker run -d \
  --name flask-student-app \
  -p 5000:5000 \
  -e MONGO_URI="<MONGO_URI>" \
  -e SECRET_KEY="<SECRET_KEY>" \
  flask-student-app
```

## Check Container

```bash
docker ps
```

## Test Application

```bash
curl -i http://localhost:5000/health
```

---

# 12. AWS ECR Setup

## ECR Repository

Repository:

```text
flask-student-app
```

Region:

```text
us-east-1
```

Registry:

```text
789193817635.dkr.ecr.us-east-1.amazonaws.com
```

## ECR Image Name

```text
789193817635.dkr.ecr.us-east-1.amazonaws.com/flask-student-app:<GIT_SHA>
```

## ECR Login

```bash
aws ecr get-login-password \
  --region us-east-1 | \
docker login \
  --username AWS \
  --password-stdin \
  789193817635.dkr.ecr.us-east-1.amazonaws.com
```

---

# 13. EC2 Setup

An Ubuntu EC2 instance is used as the application host.

## Required Software

- Docker
- AWS CLI
- SSH
- IAM instance role

## Start Docker

```bash
sudo systemctl start docker
```

## Enable Docker

```bash
sudo systemctl enable docker
```

## Verify Docker

```bash
docker --version
```

## Verify AWS Identity

```bash
aws sts get-caller-identity
```

---

# 14. EC2 IAM Role

The EC2 instance uses:

```text
EC2ECRPullRole
```

The role provides ECR read/pull permissions.

This allows EC2 to authenticate with ECR without storing AWS access keys on the server.

## Verify Role

```bash
aws sts get-caller-identity
```

The output should show the assumed EC2 role.

---

# 15. EC2 Security Group

Required inbound rules:

| Protocol | Port | Purpose |
|---|---:|---|
| TCP | 22 | SSH |
| TCP | 5000 | Flask application |

The application was finally accessed using:

```text
http://<EC2-PUBLIC-IP>:5000
```

For production, do not expose port `5000` broadly. Prefer HTTPS through a reverse proxy or load balancer.

---

# 16. GitHub OIDC and IAM Role

GitHub Actions uses AWS OIDC instead of long-lived AWS access keys.

The workflow contains:

```yaml
permissions:
  id-token: write
  contents: read
```

The GitHub Actions IAM role is:

```text
GitHubActionsECRPush
```

Role ARN:

```text
arn:aws:iam::789193817635:role/GitHubActionsECRPush
```

## Authentication Flow

```text
GitHub Actions
      |
      | OIDC Token
      v
AWS IAM
      |
      | AssumeRole
      v
GitHubActionsECRPush
      |
      v
Amazon ECR
```

---

# 17. GitHub Secrets

Configure these repository secrets:

```text
EC2_HOST
EC2_USER
EC2_SSH_KEY
MONGO_URI
SECRET_KEY
```

## EC2_HOST

EC2 public IP or DNS name.

## EC2_USER

For Ubuntu:

```text
ubuntu
```

## EC2_SSH_KEY

The private SSH key associated with the EC2 key pair.

## MONGO_URI

Complete MongoDB Atlas connection string.

## SECRET_KEY

Flask secret key.

### Security Rule

Never commit the actual values into the repository.

Do not include:

```text
MongoDB passwords
SSH private keys
AWS access keys
AWS secret keys
GitHub tokens
SECRET_KEY
SMTP passwords
```

---

# 18. GitHub Actions CI/CD Workflow

Workflow file:

```text
.github/workflows/ci-cd.yml
```

The workflow runs when code is pushed to `main`.

```yaml
on:
  push:
    branches:
      - main
```

## Workflow Dependency

```text
test
  |
  v
quality
  |
  v
build
  |
  v
deploy
```

A failure in an earlier stage prevents dependent stages from proceeding.

---

# 19. Complete CI/CD YAML

The following is the CI/CD workflow used for the project.

> **Important:** The repository should contain the exact version currently committed in `.github/workflows/ci-cd.yml`. If the workflow is later changed for email notifications or immutable-tag handling, update this README code block at the same time.

```yaml
name: Flask CI/CD Pipeline

on:
  push:
    branches:
      - main

permissions:
  id-token: write
  contents: read

jobs:
  test:
    runs-on: ubuntu-latest

    env:
      MONGO_URI: mongodb://localhost:27017/test_student_db
      SECRET_KEY: ci-test-secret

    services:
      mongodb:
        image: mongo:latest
        ports:
          - 27017:27017

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.14"

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Run tests
        run: pytest

  quality:
    needs: test
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.14"

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Check Black formatting
        run: black --check .

  build:
    needs: quality
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v5
        with:
          role-to-assume: arn:aws:iam::789193817635:role/GitHubActionsECRPush
          aws-region: us-east-1

      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2

      - name: Build Docker image
        run: |
          docker build -t flask-student-app:${{ github.sha }} .

      - name: Tag Docker image
        run: |
          docker tag flask-student-app:${{ github.sha }} \
            789193817635.dkr.ecr.us-east-1.amazonaws.com/flask-student-app:${{ github.sha }}

      - name: Push Docker image to ECR
        run: |
          docker push 789193817635.dkr.ecr.us-east-1.amazonaws.com/flask-student-app:${{ github.sha }}

  deploy:
    needs: build
    runs-on: ubuntu-latest

    steps:
      - name: Deploy to EC2
        uses: appleboy/ssh-action@v1.2.2
        env:
          MONGO_URI: ${{ secrets.MONGO_URI }}
          SECRET_KEY: ${{ secrets.SECRET_KEY }}
          IMAGE_TAG: ${{ github.sha }}
        with:
          host: ${{ secrets.EC2_HOST }}
          username: ${{ secrets.EC2_USER }}
          key: ${{ secrets.EC2_SSH_KEY }}
          envs: MONGO_URI,SECRET_KEY,IMAGE_TAG
          script: |
            set -e

            echo "Logging in to Amazon ECR..."

            aws ecr get-login-password --region us-east-1 | \
              sudo docker login \
              --username AWS \
              --password-stdin 789193817635.dkr.ecr.us-east-1.amazonaws.com

            echo "Pulling image..."

            sudo docker pull \
              789193817635.dkr.ecr.us-east-1.amazonaws.com/flask-student-app:${IMAGE_TAG}

            echo "Stopping existing container..."

            sudo docker rm -f flask-student-app || true

            echo "Starting new container..."

            sudo docker run -d \
              --name flask-student-app \
              --restart unless-stopped \
              -p 5000:5000 \
              -e MONGO_URI="$MONGO_URI" \
              -e SECRET_KEY="$SECRET_KEY" \
              789193817635.dkr.ecr.us-east-1.amazonaws.com/flask-student-app:${IMAGE_TAG}

            echo "Waiting for application..."

            for i in {1..30}; do
              if curl -fsS http://localhost:5000/health; then
                echo "Health check passed!"
                exit 0
              fi

              echo "Application not ready yet. Waiting..."
              sleep 2
            done

            echo "Health check failed!"

            sudo docker logs flask-student-app

            exit 1
```

---

# 20. Pipeline Stages Explained

## Stage 1 – Test

The test job:

1. Starts an Ubuntu runner.
2. Starts MongoDB as a service container.
3. Installs Python 3.14.
4. Installs dependencies.
5. Runs pytest.

Command:

```bash
pytest
```

If tests fail, the pipeline stops.

---

## Stage 2 – Quality

The quality job runs after successful tests.

```yaml
needs: test
```

Black checks formatting:

```bash
black --check .
```

If formatting fails, build and deployment do not proceed.

---

## Stage 3 – Build

The build job:

1. Configures AWS credentials using OIDC.
2. Logs into ECR.
3. Builds the Docker image.
4. Tags it using the Git commit SHA.
5. Pushes it to ECR.

---

## Stage 4 – Deploy

The deployment job:

1. Connects to EC2 through SSH.
2. Logs into ECR.
3. Pulls the image.
4. Removes the existing container.
5. Starts the new container.
6. Passes MongoDB and Flask secrets as environment variables.
7. Waits for `/health`.
8. Fails if health verification does not succeed.

---

# 21. Docker Image Tagging

The image is tagged with:

```text
${{ github.sha }}
```

Example:

```text
51877bc6f680da8074e4c6deb7457d715016f237
```

Full image:

```text
789193817635.dkr.ecr.us-east-1.amazonaws.com/flask-student-app:51877bc6f680da8074e4c6deb7457d715016f237
```

This allows every deployment to be mapped to a specific Git commit.

---

# 22. ECR Immutable Tags

During implementation, a pipeline rerun produced:

```text
tag invalid: The image tag '51877bc6f680da8074e4c6deb7457d715016f237'
already exists in the 'flask-student-app' repository and cannot be overwritten
because the tag is immutable.
```

The existing image was confirmed using:

```bash
aws ecr describe-images \
  --repository-name flask-student-app \
  --region us-east-1 \
  --image-ids imageTag=51877bc6f680da8074e4c6deb7457d715016f237
```

The image was already present in ECR.

The pipeline was subsequently updated to handle existing immutable SHA tags safely.

---

# 23. Deployment to EC2

The deployment uses SSH through GitHub Actions.

## Login to ECR

```bash
aws ecr get-login-password --region us-east-1 | \
sudo docker login \
--username AWS \
--password-stdin \
789193817635.dkr.ecr.us-east-1.amazonaws.com
```

## Pull Image

```bash
sudo docker pull \
789193817635.dkr.ecr.us-east-1.amazonaws.com/flask-student-app:${IMAGE_TAG}
```

## Remove Existing Container

```bash
sudo docker rm -f flask-student-app || true
```

## Start New Container

```bash
sudo docker run -d \
  --name flask-student-app \
  --restart unless-stopped \
  -p 5000:5000 \
  -e MONGO_URI="$MONGO_URI" \
  -e SECRET_KEY="$SECRET_KEY" \
  789193817635.dkr.ecr.us-east-1.amazonaws.com/flask-student-app:${IMAGE_TAG}
```

---

# 24. Deployment Health Check

The pipeline checks:

```bash
curl -fsS http://localhost:5000/health
```

The pipeline waits up to 30 attempts with a two-second interval.

Successful response:

```text
Health check passed!
```

Failure:

```text
Health check failed!
```

The health check verifies both:

```text
Flask Application
        +
MongoDB Connectivity
```

Final verified response:

```text
HTTP/1.1 200 OK

{"status": "healthy", "mongodb": "connected"}
```

---

# 25. Manual Deployment

Manual deployment can be performed directly from EC2.

## Step 1 – ECR Login

```bash
aws ecr get-login-password \
--region us-east-1 | \
sudo docker login \
--username AWS \
--password-stdin \
789193817635.dkr.ecr.us-east-1.amazonaws.com
```

## Step 2 – Pull Image

```bash
sudo docker pull \
789193817635.dkr.ecr.us-east-1.amazonaws.com/flask-student-app:<GIT_SHA>
```

## Step 3 – Remove Existing Container

```bash
sudo docker rm -f flask-student-app || true
```

## Step 4 – Start Container

```bash
sudo docker run -d \
  --name flask-student-app \
  --restart unless-stopped \
  -p 5000:5000 \
  -e MONGO_URI="<MONGO_URI>" \
  -e SECRET_KEY="<SECRET_KEY>" \
  789193817635.dkr.ecr.us-east-1.amazonaws.com/flask-student-app:<GIT_SHA>
```

## Step 5 – Verify

```bash
sudo docker ps
```

## Step 6 – Health Check

```bash
curl -i http://localhost:5000/health
```

---

# 26. End-to-End Verification

## Docker Verification

Command:

```bash
sudo docker ps
```

Final deployment showed:

```text
CONTAINER ID   IMAGE
a8a62a78f05a   789193817635.dkr.ecr.us-east-1.amazonaws.com/flask-student-app:fbe1641fbc4973cfade86072d0bf02efce3ef9a8
```

Container:

```text
flask-student-app
```

Port mapping:

```text
0.0.0.0:5000->5000/tcp
```

## Health Verification

Command:

```bash
curl -i http://localhost:5000/health
```

Final result:

```text
HTTP/1.1 200 OK
Content-Type: application/json

{"status": "healthy", "mongodb": "connected"}
```

## Public Verification

The application was successfully accessed through:

```text
http://<EC2-PUBLIC-IP>:5000
```

The Student Registration System loaded in the browser.

## Functional Verification

A student was successfully created through the deployed application.

This verified:

```text
Browser
   |
   v
EC2
   |
   v
Docker
   |
   v
Flask
   |
   v
MongoDB Atlas
```

---

# 27. Troubleshooting

## 27.1 SSH Permission Denied

Error:

```text
Permission denied (publickey)
```

### Resolution

Verify:

- EC2 key pair
- `EC2_SSH_KEY` GitHub Secret
- `EC2_USER`
- EC2 public IP/DNS
- Security Group port 22

---

## 27.2 MongoDB Cluster Paused

Symptom:

```text
MongoDB connection failure
```

### Resolution

Resume the MongoDB Atlas cluster and retry the deployment.

---

## 27.3 MongoDB Authentication Failed

Error:

```text
pymongo.errors.OperationFailure:
bad auth : authentication failed
```

### Resolution

1. Change the MongoDB database-user password.
2. Update the complete `MONGO_URI`.
3. Update the GitHub `MONGO_URI` Secret.
4. Trigger a new deployment.
5. Verify the environment variable inside the new container.
6. Test MongoDB with PyMongo.

---

## 27.4 Mongo Database Was None

Diagnostic output:

```text
Database: None
```

### Cause

The MongoDB URI did not specify a database.

### Resolution

Use a URI that includes a database name:

```text
mongodb+srv://<username>:<password>@<cluster>.mongodb.net/student_db?appName=<application-name>
```

---

## 27.5 ECR Immutable Tag Error

Error:

```text
tag invalid: The image tag already exists
and cannot be overwritten because the tag is immutable
```

### Resolution

Check the image:

```bash
aws ecr describe-images \
--repository-name flask-student-app \
--region us-east-1 \
--image-ids imageTag=<GIT_SHA>
```

If the image already exists, reuse it instead of trying to overwrite the immutable tag.

---

## 27.6 Health Endpoint Returns HTTP 500

Check container logs:

```bash
sudo docker logs flask-student-app
```

Then test MongoDB directly:

```bash
sudo docker exec flask-student-app python -c \
"import os; from pymongo import MongoClient; MongoClient(os.environ['MONGO_URI'], serverSelectionTimeoutMS=10000).admin.command('ping'); print('MongoDB authentication successful')"
```

Then test:

```bash
curl -i http://localhost:5000/health
```

---

## 27.7 Application Works on Localhost but Not Public IP

Test locally on EC2:

```bash
curl -i http://localhost:5000/
```

Then verify:

```text
Security Group → TCP 5000
```

From Windows PowerShell:

```powershell
Test-NetConnection <EC2-PUBLIC-IP> -Port 5000
```

Expected:

```text
TcpTestSucceeded : True
```

Then open:

```text
http://<EC2-PUBLIC-IP>:5000
```

---

# 28. Useful EC2 Commands

## Docker Version

```bash
docker --version
```

## Docker Status

```bash
sudo systemctl status docker
```

## Running Containers

```bash
sudo docker ps
```

## All Containers

```bash
sudo docker ps -a
```

## Application Logs

```bash
sudo docker logs flask-student-app
```

## Follow Logs

```bash
sudo docker logs -f flask-student-app
```

## Container Environment Check

Do not print secret values:

```bash
sudo docker exec flask-student-app python -c \
"import os; print('MONGO_URI present:', bool(os.getenv('MONGO_URI')))"
```

## Health Check

```bash
curl -i http://localhost:5000/health
```

## Home Page

```bash
curl -i http://localhost:5000/
```

## AWS Identity

```bash
aws sts get-caller-identity
```

---

# 29. Security Considerations

The project uses the following security practices:

- GitHub Actions authenticates to AWS using OIDC.
- EC2 authenticates to ECR using an IAM role.
- MongoDB credentials are stored in GitHub Secrets.
- EC2 SSH private key is stored in GitHub Secrets.
- Flask secret key is stored in GitHub Secrets.
- Docker images use Git SHA tags.
- ECR tags are immutable.
- Secrets are passed to the application as environment variables.
- Credentials are not committed to Git.

## Never Commit

```text
.env
MongoDB passwords
MongoDB URI containing credentials
AWS access keys
AWS secret keys
SSH private keys
GitHub tokens
SECRET_KEY
SMTP passwords
```

---

# 30. Screenshots and Evidence

This section is reserved for screenshots required to demonstrate the implementation.

## Recommended Repository Structure

```text
flask-student-app/
│
├── README.md
│
└── screenshots/
    ├── 01-github-repository.png
    ├── 02-project-files.png
    ├── 03-mongodb-atlas.png
    ├── 04-ecr-repository.png
    ├── 05-ecr-image.png
    ├── 06-ec2-instance.png
    ├── 07-ec2-iam-role.png
    ├── 08-security-group.png
    ├── 09-github-oidc-role.png
    ├── 10-github-secrets.png
    ├── 11-ci-cd-workflow.png
    ├── 12-successful-pipeline.png
    ├── 13-ecr-image-sha.png
    ├── 14-ec2-docker-ps.png
    ├── 15-health-check.png
    ├── 16-public-application.png
    ├── 17-add-student.png
    ├── 18-student-record.png
    ├── 19-success-email.png
    ├── 20-intentional-failure.png
    └── 21-failure-email.png
```

## Screenshot 1 – GitHub Repository

**File:**

```text
screenshots/01-github-repository.png
```

Show:

- Repository name
- Repository files
- GitHub URL

---

## Screenshot 2 – Project Files

**File:**

```text
screenshots/02-project-files.png
```

Show:

```text
app.py
Dockerfile
requirements.txt
test_app.py
.github/workflows/ci-cd.yml
README.md
```

---

## Screenshot 3 – MongoDB Atlas

**File:**

```text
screenshots/03-mongodb-atlas.png
```

Show:

- Cluster
- Cluster status
- Database deployment

Do not expose credentials.

---

## Screenshot 4 – ECR Repository

**File:**

```text
screenshots/04-ecr-repository.png
```

Show:

```text
flask-student-app
```

and:

```text
us-east-1
```

---

## Screenshot 5 – ECR Image

**File:**

```text
screenshots/05-ecr-image.png
```

Show the image tagged using the Git SHA.

Example:

```text
fbe1641bc4973cfade86072d0bf02efce3ef9a8
```

---

## Screenshot 6 – EC2 Instance

**File:**

```text
screenshots/06-ec2-instance.png
```

Show:

- Running instance
- Ubuntu
- Instance status
- Public IPv4 address

---

## Screenshot 7 – EC2 IAM Role

**File:**

```text
screenshots/07-ec2-iam-role.png
```

Show the EC2 role attached to the instance.

Example:

```text
EC2ECRPullRole
```

---

## Screenshot 8 – Security Group

**File:**

```text
screenshots/08-security-group.png
```

Show inbound rules for:

```text
22
5000
```

Do not expose unnecessary sensitive infrastructure details.

---

## Screenshot 9 – GitHub OIDC IAM Role

**File:**

```text
screenshots/09-github-oidc-role.png
```

Show:

```text
GitHubActionsECRPush
```

and the trust relationship configured for GitHub Actions OIDC.

---

## Screenshot 10 – GitHub Secrets

**File:**

```text
screenshots/10-github-secrets.png
```

Only show the secret **names**.

Example:

```text
EC2_HOST
EC2_USER
EC2_SSH_KEY
MONGO_URI
SECRET_KEY
```

Never show secret values.

---

## Screenshot 11 – CI/CD Workflow

**File:**

```text
screenshots/11-ci-cd-workflow.png
```

Show the workflow stages:

```text
test
quality
build
deploy
```

---

## Screenshot 12 – Successful Pipeline

**File:**

```text
screenshots/12-successful-pipeline.png
```

Show:

```text
test       ✓
quality    ✓
build      ✓
deploy     ✓
```

---

## Screenshot 13 – ECR Image with SHA

**File:**

```text
screenshots/13-ecr-image-sha.png
```

Show the successfully pushed image and Git SHA tag.

---

## Screenshot 14 – Running Docker Container

**File:**

```text
screenshots/14-ec2-docker-ps.png
```

Run:

```bash
sudo docker ps
```

The screenshot should show:

```text
flask-student-app
```

and:

```text
0.0.0.0:5000->5000/tcp
```

---

## Screenshot 15 – Health Check

**File:**

```text
screenshots/15-health-check.png
```

Run:

```bash
curl -i http://localhost:5000/health
```

Expected:

```text
HTTP/1.1 200 OK
```

and:

```json
{"status": "healthy", "mongodb": "connected"}
```

---

## Screenshot 16 – Public Application

**File:**

```text
screenshots/16-public-application.png
```

Show the Student Registration System running in a browser.

URL format:

```text
http://<EC2-PUBLIC-IP>:5000
```

---

## Screenshot 17 – Add Student

**File:**

```text
screenshots/17-add-student.png
```

Show the Add Student form with a test record.

---

## Screenshot 18 – Student Record

**File:**

```text
screenshots/18-student-record.png
```

Show the created student appearing in the application.

---

## Screenshot 19 – Success Email

**File:**

```text
screenshots/19-success-email.png
```

This screenshot will be added after email notification implementation.

The email should contain:

- Success indicator
- Commit SHA
- Branch
- Docker image tag
- EC2 target
- GitHub Actions run link

---

## Screenshot 20 – Intentional Failure

**File:**

```text
screenshots/20-intentional-failure.png
```

This screenshot will show an intentionally broken pipeline.

Expected flow:

```text
test       ✗
quality    skipped
build      skipped
deploy     skipped
```

The failure should be created deliberately for testing and then reverted.

---

## Screenshot 21 – Failure Email

**File:**

```text
screenshots/21-failure-email.png
```

This screenshot will be added after failure notification implementation.

The email should contain:

- Failure indicator
- Failed stage
- Commit SHA
- Branch
- GitHub Actions run/log link

---

# 31. Email Notifications

> **Status: Pending implementation**

The assignment requires customized email notifications for successful and failed pipeline executions.

## Success Notification

The success email should include:

```text
SUCCESS

Repository:
Branch:
Commit SHA:
Docker Image:
EC2 Target:
Application:
Pipeline Run:
```

## Failure Notification

The failure email should include:

```text
FAILURE

Failed Stage:
Repository:
Branch:
Commit SHA:
Pipeline Run:
Logs:
```

## Intentionally Failed Pipeline

A temporary failing test will be introduced to verify that:

```text
test fails
    |
    +--> quality skipped
    |
    +--> build skipped
    |
    +--> deploy skipped
    |
    +--> failure email
```

After evidence is captured, the failing test will be removed and the successful pipeline will be run again.

SMTP credentials must be stored in GitHub Secrets.

---

# 32. Assignment Checklist

| Requirement | Status |
|---|---|
| Flask application | Completed |
| CRUD functionality | Completed |
| `/health` endpoint | Completed |
| pytest | Completed |
| Black quality check | Completed |
| Dockerfile | Completed |
| Docker build | Completed |
| GitHub Actions | Completed |
| Push trigger on main | Completed |
| Test stage | Completed |
| Quality stage | Completed |
| Build stage | Completed |
| ECR repository | Completed |
| Git SHA image tagging | Completed |
| ECR immutable tags | Completed |
| EC2 instance | Completed |
| EC2 IAM role | Completed |
| GitHub OIDC | Completed |
| GitHub Secrets | Completed |
| EC2 deployment | Completed |
| Container replacement | Completed |
| Restart policy | Completed |
| Health check gate | Completed |
| MongoDB Atlas | Completed |
| Public application | Completed |
| Success email | Pending |
| Failure email | Pending |
| Intentional failure test | Pending |
| Final screenshot evidence | In progress |

---

# 33. Final Architecture

```text
                              DEVELOPER
                                  |
                                  | git push
                                  v
                         +----------------+
                         |     GitHub     |
                         +-------+--------+
                                 |
                                 v
                     +------------------------+
                     |    GitHub Actions      |
                     +-----------+------------+
                                 |
                    +------------+------------+
                    |            |            |
                    v            v            v
                 PYTEST        BLACK       DOCKER BUILD
                    |            |            |
                    +------------+------------+
                                 |
                                 v
                     +------------------------+
                     |      Amazon ECR        |
                     |   flask-student-app    |
                     +-----------+------------+
                                 |
                                 | docker pull
                                 v
                     +------------------------+
                     |      Amazon EC2        |
                     |        Ubuntu          |
                     |        Docker          |
                     +-----------+------------+
                                 |
                                 | :5000
                                 v
                     +------------------------+
                     | Flask Student System   |
                     +-----------+------------+
                                 |
                                 | MONGO_URI
                                 v
                     +------------------------+
                     |     MongoDB Atlas      |
                     +------------------------+
```

---

# 34. Final Result

The application was successfully deployed to EC2.

## Docker

The running container:

```text
flask-student-app
```

was mapped as:

```text
0.0.0.0:5000->5000/tcp
```

## Health

The final health check returned:

```text
HTTP/1.1 200 OK
```

with:

```json
{
  "status": "healthy",
  "mongodb": "connected"
}
```

## Public Application

The Student Registration System was successfully opened through:

```text
http://<EC2-PUBLIC-IP>:5000
```

## Functional Test

A student was successfully created through the deployed application.

This confirmed:

```text
Browser
   |
   v
EC2 Public IP
   |
   v
Docker
   |
   v
Flask
   |
   v
MongoDB Atlas
```

---

# 35. Future Improvements

Potential production improvements:

- HTTPS
- Domain name
- Nginx reverse proxy
- Application Load Balancer
- Auto Scaling
- CloudWatch monitoring
- Centralized logging
- AWS Systems Manager instead of SSH
- More restrictive network access
- Automated rollback
- Container vulnerability scanning
- Deployment approvals
- Email notifications
- Secrets Manager / Parameter Store
- Blue/green deployment

---

# 36. Repository

GitHub Repository:

```text
<YOUR-GITHUB-REPOSITORY-LINK>
```

Replace the placeholder with the existing repository URL.

---

# 37. Conclusion

This project demonstrates an end-to-end CI/CD implementation for a containerized Flask application.

The completed workflow connects:

```text
GitHub
   |
GitHub Actions
   |
Pytest
   |
Black
   |
Docker
   |
Amazon ECR
   |
Amazon EC2
   |
Flask
   |
MongoDB Atlas
```

The pipeline automatically tests the application, performs a quality check, builds and versions the Docker image, stores the image in ECR, deploys the image to EC2, replaces the previous container, and verifies the deployment through the `/health` endpoint.

The final deployment was successfully tested with the public Flask application and MongoDB-backed student data.

The remaining assignment work is the success/failure email notification implementation, intentional failure test, and corresponding evidence screenshots.
