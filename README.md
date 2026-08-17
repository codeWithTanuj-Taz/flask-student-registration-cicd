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

```
### Screenshot
<img width="1888" height="939" alt="Screenshot 2026-08-16 015549" src="https://github.com/user-attachments/assets/9c05e8ac-69ba-4b2f-a896-3d616fa8d7c0" />

```bash
git status
dir
```

You should see files/folders from the original Flask project.

<img width="1008" height="882" alt="Screenshot 2026-08-16 015822" src="https://github.com/user-attachments/assets/40249045-4d2e-465e-a746-c1c091417d9d" />

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

### ScreenShots 

<img width="2166" height="726" alt="Screenshot 2026-07-20 123057" src="https://github.com/user-attachments/assets/a34f8153-e962-48e7-aad6-fd85178a4d7a" />

<img width="1683" height="42" alt="Screenshot 2026-08-17 035017" src="https://github.com/user-attachments/assets/9f72a373-d4c4-44d1-ae96-128f233fa3d9" />


## 9.2 Install Dependencies

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```
###Screenshot
<img width="1762" height="892" alt="Screenshot 2026-07-20 123069" src="https://github.com/user-attachments/assets/a9d06a86-42e1-4b5c-bbc7-815a8bc6e09b" />
<img width="1754" height="897" alt="Screenshot 2026-07-20 765834" src="https://github.com/user-attachments/assets/bc63defd-8643-4145-b1f4-61d94d1d9dc7" />



## 9.3 Check pytest

```bash
pytest
```
<img width="1367" height="297" alt="Screenshot 2026-08-16 083747" src="https://github.com/user-attachments/assets/ff716326-cb8c-4b97-9e77-dd7a6a10a046" />


## 9.4 Start Flask

```bash
python app.py
```

Application:

```text
http://localhost:5000
```

<img width="1893" height="653" alt="image" src="https://github.com/user-attachments/assets/0389135a-b3f8-4ed3-aea3-6e3aaca53783" />


## 9.5 Test Health

```bash
curl http://localhost:5000/health
```

<img width="1384" height="337" alt="image" src="https://github.com/user-attachments/assets/c1baf01d-b986-4be7-9d1a-8f053afb71fe" />


---


# 10. Docker Configuration

The Flask application is containerized using Docker.

The application listens on port:

```text
5000
```

## Create folder Dockerfile

<img width="1888" height="659" alt="image" src="https://github.com/user-attachments/assets/583801ee-5302-4472-9e17-a941ddd94dbd" />



## Build Image

```bash
docker build -t flask-student-app .
```
<img width="1885" height="465" alt="image" src="https://github.com/user-attachments/assets/7a2e705e-958f-4ed2-8291-9e7ff4aa2c97" />
<img width="1344" height="182" alt="Screenshot 2026-08-16 143238" src="https://github.com/user-attachments/assets/7fab9824-1cfc-4dca-8d05-3509b6a50ad6" />



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
<img width="1895" height="624" alt="image" src="https://github.com/user-attachments/assets/20820b20-08d7-4e94-80d7-aadb15427a13" />

## Create MongoDB flask network

<img width="1303" height="99" alt="Screenshot 2026-08-16 151814" src="https://github.com/user-attachments/assets/985f358d-1654-4a8e-b4d8-9c230065a2e5" />


## Test Application

```bash
curl -i http://localhost:5000/health
```

<img width="1388" height="315" alt="image" src="https://github.com/user-attachments/assets/e41d3d0e-18ce-4087-a885-40a825b29bf7" />

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

### Screenshots
<img width="1893" height="1041" alt="Screenshot 2026-08-16 181914" src="https://github.com/user-attachments/assets/165f01d0-89a0-42b7-889d-4ecef8173599" />
<img width="1905" height="896" alt="Screenshot 2026-08-16 181944" src="https://github.com/user-attachments/assets/aca3f4fe-bf61-4027-a437-46c2063b1078" />
<img width="1896" height="1224" alt="Screenshot 2026-08-16 182252" src="https://github.com/user-attachments/assets/48fa8d55-b3ec-42a4-82c1-593e0bb54fa1" />
<img width="1682" height="303" alt="Screenshot 2026-08-16 182426" src="https://github.com/user-attachments/assets/69fe4e0f-4180-46ce-af18-04168b9d370a" />
<img width="1892" height="536" alt="Screenshot 2026-08-16 182702" src="https://github.com/user-attachments/assets/9db1ad27-747e-49a0-82ef-52027b4b75cc" />
<img width="1908" height="933" alt="Screenshot 2026-08-16 182735" src="https://github.com/user-attachments/assets/0b3c8ba4-143f-4ef8-ac77-0ca9c5da01e8" />
<img width="1265" height="391" alt="Screenshot 2026-08-16 182920" src="https://github.com/user-attachments/assets/2f3f8be3-faca-447f-8cc1-d62d7eab4e9f" />
<img width="1919" height="1041" alt="Screenshot 2026-08-16 183125" src="https://github.com/user-attachments/assets/43b80f0d-433a-4c3f-8b17-acafd4f06a98" />
<img width="1882" height="861" alt="Screenshot 2026-08-16 183158" src="https://github.com/user-attachments/assets/7f1dd923-0f1f-473c-a399-bd21d9cab718" />
<img width="1919" height="1180" alt="Screenshot 2026-08-16 183826" src="https://github.com/user-attachments/assets/5b015fab-ef4d-4199-9781-2471170c4371" />
<img width="1883" height="994" alt="Screenshot 2026-08-16 184122" src="https://github.com/user-attachments/assets/4042234e-8ad1-411d-beca-cdb3353fc501" />
<img width="1873" height="910" alt="Screenshot 2026-08-16 184601" src="https://github.com/user-attachments/assets/c009192d-ea90-4faa-9c59-4abcd299fb0b" />
<img width="1887" height="913" alt="Screenshot 2026-08-16 190306" src="https://github.com/user-attachments/assets/5b41e6ad-9669-4d16-9275-13c4a34b6bab" />
<img width="1913" height="343" alt="image" src="https://github.com/user-attachments/assets/44d1f7a2-a466-4c91-b894-0a4dfb6e3fb8" />




---

# 13. EC2 Setup

An Ubuntu EC2 instance is used as the application host.

launch an EC2 Instance
<img width="1869" height="921" alt="Screenshot 2026-08-16 193751" src="https://github.com/user-attachments/assets/29b6a879-c24b-4d89-8b9e-9b94d2870ddf" />


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

### Screen shots 

Connect the Ec2 through SSH
<img width="1612" height="338" alt="image" src="https://github.com/user-attachments/assets/89563f2c-81fe-472d-b011-c754943928a5" />
<img width="1631" height="98" alt="image" src="https://github.com/user-attachments/assets/2eb476a4-81db-4aff-8d4d-a171449832c5" />


---

# 14. EC2 IAM Role

The EC2 instance uses:

```text
EC2ECRPullRole
```

The role provides ECR read/pull permissions.

This allows EC2 to authenticate with ECR without storing AWS access keys on the server.

<img width="1605" height="694" alt="image" src="https://github.com/user-attachments/assets/9dd6c59d-9811-4c5b-9f4b-ec278c48a178" />


## Verify Role

```bash
aws sts get-caller-identity
```
The output should show the assumed EC2 role.

<img width="1647" height="138" alt="image" src="https://github.com/user-attachments/assets/52604671-357c-4650-bd96-3a62e3f01063" />


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

<img width="1890" height="839" alt="image" src="https://github.com/user-attachments/assets/9cbfe3cc-df8c-4ff2-a974-7227b483a443" />


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

<img width="1873" height="910" alt="Screenshot 2026-08-16 184601" src="https://github.com/user-attachments/assets/0eabbe27-463b-4215-8754-886f761c8084" />


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

# 17. GitHub Secrets including email configuration 

Configure these repository secrets:

```text
EC2_HOST        EC2 public IP or DNS name.
EC2_USER        ubuntu
EC2_SSH_KEY     The private SSH key associated with the EC2 key pair.
MONGO_URI       Complete MongoDB Atlas connection string.
SECRET_KEY      Flask secret key.
SMTP_HOST       smtp.gmail.com
SMTP_PORT       587
SMTP_USERNAME   yourgmail@gmail.com
SMTP_PASSWORD   16-character-app-password
EMAIL_TO        yourgmail@gmail.com
```

<img width="1364" height="912" alt="image" src="https://github.com/user-attachments/assets/5df8668b-0015-4047-b9ff-1b4b4b4a2bcb" />


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

###Screen shots
<img width="1179" height="1052" alt="Screenshot 2026-08-17 071218" src="https://github.com/user-attachments/assets/03ef83d0-da26-402a-9260-e6a484f3a105" />


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
     notification:
    needs:
      - test
      - quality
      - build
      - deploy

    if: ${{ always() }}

    runs-on: ubuntu-latest

    steps:
      - name: Send success email
        if: |
          needs.test.result == 'success' &&
          needs.quality.result == 'success' &&
          needs.build.result == 'success' &&
          needs.deploy.result == 'success'
        uses: dawidd6/action-send-mail@v6
        with:
          server_address: ${{ secrets.SMTP_HOST }}
          server_port: ${{ secrets.SMTP_PORT }}
          username: ${{ secrets.SMTP_USERNAME }}
          password: ${{ secrets.SMTP_PASSWORD }}
          subject: "SUCCESS - Flask CI/CD Pipeline"
          to: ${{ secrets.EMAIL_TO }}
          from: ${{ secrets.SMTP_USERNAME }}
          body: |
            Flask Student Registration CI/CD Pipeline

            STATUS: SUCCESS

            Repository:
            ${{ github.repository }}

            Branch:
            ${{ github.ref_name }}

            Commit:
            ${{ github.sha }}

            Docker Image:
            789193817635.dkr.ecr.us-east-1.amazonaws.com/flask-student-app:${{ github.sha }}

            Deployment:
            Amazon EC2

            Pipeline Run:
            ${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}

            Test: SUCCESS
            Quality: SUCCESS
            Build: SUCCESS
            Deploy: SUCCESS

            The application was successfully deployed
            and passed the health check.


      - name: Send failure email
        if: |
          needs.test.result != 'success' ||
          needs.quality.result != 'success' ||
          needs.build.result != 'success' ||
          needs.deploy.result != 'success'
        uses: dawidd6/action-send-mail@v6
        with:
          server_address: ${{ secrets.SMTP_HOST }}
          server_port: ${{ secrets.SMTP_PORT }}
          username: ${{ secrets.SMTP_USERNAME }}
          password: ${{ secrets.SMTP_PASSWORD }}
          subject: "FAILURE - Flask CI/CD Pipeline"
          to: ${{ secrets.EMAIL_TO }}
          from: ${{ secrets.SMTP_USERNAME }}
          body: |
            Flask Student Registration CI/CD Pipeline

            STATUS: FAILURE

            Repository:
            ${{ github.repository }}

            Branch:
            ${{ github.ref_name }}

            Commit:
            ${{ github.sha }}

            Pipeline Run:
            ${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}

            Job Results:

            Test:   ${{ needs.test.result }}
            Quality: ${{ needs.quality.result }}
            Build:  ${{ needs.build.result }}
            Deploy: ${{ needs.deploy.result }}

            Please open the GitHub Actions run above
            to investigate the failed stage.  
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

<img width="1656" height="734" alt="image" src="https://github.com/user-attachments/assets/f915f43a-e385-4a4b-982a-5268b163a4b8" />


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
<img width="1638" height="185" alt="image" src="https://github.com/user-attachments/assets/13cb6cce-6ba9-4be4-8188-98d445332774" />



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
<img width="1092" height="68" alt="image" src="https://github.com/user-attachments/assets/dff3fb70-49f8-4c00-85a7-6f7f75d34e1e" />


The pipeline waits up to 30 attempts with a two-second interval.


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
<img width="1179" height="1052" alt="Screenshot 2026-08-17 071218" src="https://github.com/user-attachments/assets/dfe8249d-651f-43a5-95f4-17421a33a173" />
<img width="1850" height="999" alt="Screenshot 2026-08-17 071615" src="https://github.com/user-attachments/assets/f37b754f-39ee-444b-931b-e4e430581452" />
<img width="1860" height="989" alt="Screenshot 2026-08-16 194530" src="https://github.com/user-attachments/assets/e9f69503-318f-46c6-9559-81c5a1b16ca2" />
<img width="1679" height="762" alt="Screenshot 2026-08-16 194440" src="https://github.com/user-attachments/assets/3f13d7a1-5124-4a9b-9327-edfe03592d2a" />
<img width="6" height="6" alt="Screenshot 2026-08-16 194427" src="https://github.com/user-attachments/assets/78b3be51-5e6c-4e5f-86bf-753fbf01e6fa" />
<img width="1901" height="1189" alt="Screenshot 2026-08-16 194107" src="https://github.com/user-attachments/assets/9528b4aa-2a5f-4ed3-bcf0-735ddaa559f5" />
<img width="1905" height="1237" alt="Screenshot 2026-08-16 194057" src="https://github.com/user-attachments/assets/d47e3bcb-e01c-411f-be16-38acb3f864c2" />
<img width="1917" height="704" alt="Screenshot 2026-08-16 231158" src="https://github.com/user-attachments/assets/c8133208-adab-4eff-9701-b5f1d9e09439" />
<img width="1907" height="657" alt="Screenshot 2026-08-16 231037" src="https://github.com/user-attachments/assets/9ccee203-b031-437f-9bfb-f71df3a96778" />
<img width="1919" height="617" alt="Screenshot 2026-08-16 225825" src="https://github.com/user-attachments/assets/64443ced-edc8-497b-8b58-8927a9498392" />
<img width="1317" height="390" alt="Screenshot 2026-08-16 224816" src="https://github.com/user-attachments/assets/7a90b135-d453-4a5f-8a85-a409cf9feab3" />
<img width="1887" height="880" alt="Screenshot 2026-08-16 215231" src="https://github.com/user-attachments/assets/5ae0b940-5265-4226-ac6c-dcc66416c143" />
<img width="1328" height="804" alt="Screenshot 2026-08-16 211055" src="https://github.com/user-attachments/assets/9819c27d-331e-4745-a658-5fe826ef7dfc" />
<img width="1293" height="979" alt="Screenshot 2026-08-16 210931" src="https://github.com/user-attachments/assets/a4ef3262-304c-4618-8992-13cd45756550" />
<img width="1351" height="382" alt="Screenshot 2026-08-16 210828" src="https://github.com/user-attachments/assets/fd5f8f76-88d6-4f18-868d-ea2bb31124d3" />
<img width="1290" height="912" alt="Screenshot 2026-08-16 204820" src="https://github.com/user-attachments/assets/fdfffa87-5577-4ff2-a8e2-5f5656c501b8" />
<img width="1274" height="733" alt="Screenshot 2026-08-16 203043" src="https://github.com/user-attachments/assets/d0306990-b3ad-469b-8a7b-c6a67c4ed5e1" />
<img width="1451" height="692" alt="Screenshot 2026-08-16 202237" src="https://github.com/user-attachments/assets/a1904c0f-6d87-4f01-916d-a6bc967059d0" />
<img width="1660" height="253" alt="Screenshot 2026-08-16 195922" src="https://github.com/user-attachments/assets/49c1a719-810c-4dc9-ab07-0d1e21c8fb86" />

<img width="1850" height="999" alt="Screenshot 2026-08-17 071615" src="https://github.com/user-attachments/assets/fac509a8-22be-467c-b71e-256857d93634" />
<img width="1179" height="1052" alt="Screenshot 2026-08-17 071218" src="https://github.com/user-attachments/assets/b12637d0-c5d1-4d0a-8948-07f32ac06e2f" />
<img width="1317" height="382" alt="Screenshot 2026-08-17 081205" src="https://github.com/user-attachments/assets/bf361bd6-a0e1-40d8-b84b-15f9ec536f61" />
<img width="1092" height="68" alt="Screenshot 2026-08-17 080428" src="https://github.com/user-attachments/assets/f3400cd0-d0e6-4415-bf99-a5c766e9cbda" />
<img width="1638" height="185" alt="Screenshot 2026-08-17 080157" src="https://github.com/user-attachments/assets/dabed6f7-3cf7-4e46-b1ba-add9d47ce5d7" />
<img width="1818" height="918" alt="Screenshot 2026-08-17 080040" src="https://github.com/user-attachments/assets/006ce4ad-fdb0-422d-9abe-de17433205cd" />
<img width="1656" height="734" alt="Screenshot 2026-08-17 075728" src="https://github.com/user-attachments/assets/7040e6c0-1c7d-401e-ae3a-f75425610b0b" />
<img width="1364" height="912" alt="Screenshot 2026-08-17 074415" src="https://github.com/user-attachments/assets/3d48493b-459c-4bc4-8a44-49266c8286bb" />
<img width="1870" height="1064" alt="Screenshot 2026-08-17 072412" src="https://github.com/user-attachments/assets/747bd439-1e76-4fa5-af1d-ae1c5251e62d" />
<img width="1684" height="1002" alt="Screenshot 2026-08-17 072348" src="https://github.com/user-attachments/assets/daead66a-b51f-4d50-bd19-2cecab78b624" />
<img width="1873" height="508" alt="Screenshot 2026-08-17 072330" src="https://github.com/user-attachments/assets/ed213078-b140-4f16-b7eb-ba417457637d" />
<img width="889" height="268" alt="Screenshot 2026-08-17 072056" src="https://github.com/user-attachments/assets/b05b9898-e6d8-4d12-b4f0-7977dc5176f2" />
<img width="865" height="243" alt="Screenshot 2026-08-17 071924" src="https://github.com/user-attachments/assets/a6c02d3d-7614-4802-9f39-63296f69da6f" />











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

<img width="1907" height="657" alt="Screenshot 2026-08-16 231037" src="https://github.com/user-attachments/assets/f4ac9e1a-84c2-4d01-9430-9646123102fd" />


```text
screenshots/16-public-application.png
```

Show the Student Registration System running in a browser.

URL format:

```text
http://<EC2-PUBLIC-IP>:5000
```
<img width="1907" height="657" alt="Screenshot 2026-08-16 231037" src="https://github.com/user-attachments/assets/9f4a6dd7-d6cd-43b3-bcae-8d7057c28d4b" />

---

## Screenshot 17 – Add Student

<img width="1907" height="657" alt="Screenshot 2026-08-16 231037" src="https://github.com/user-attachments/assets/50d8f8a9-ec4f-4eb7-950c-8df10665cb62" />


```text
screenshots/17-add-student.png
```

Show the Add Student form with a test record.

---

## Screenshot 18 – Student Record

<img width="1917" height="704" alt="Screenshot 2026-08-16 231158" src="https://github.com/user-attachments/assets/aeb2d23a-2134-405f-9061-87f8d9f8fa64" />


```text
screenshots/18-student-record.png
```

Show the created student appearing in the application.

---

## Screenshot 19 – Success Email

<img width="1850" height="999" alt="Screenshot 2026-08-17 071615" src="https://github.com/user-attachments/assets/38850869-a7e9-4a76-9317-9fa5f0b8db48" />


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
Changes made to pytest intentionally to obtain failiure
<img width="889" height="268" alt="Screenshot 2026-08-17 072056" src="https://github.com/user-attachments/assets/5a7eeec6-037c-4e25-834d-d69d76b1150a" />
<img width="865" height="243" alt="Screenshot 2026-08-17 071924" src="https://github.com/user-attachments/assets/ebca67b8-2d03-41aa-8877-128f4c856475" />


```text
screenshots/20-intentional-failure.png
```

This screenshot will show an intentionally broken pipeline.

<img width="1873" height="508" alt="Screenshot 2026-08-17 072330" src="https://github.com/user-attachments/assets/46e9b25b-cf33-4ee7-9519-88ee9cb2c987" />


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

<img width="1684" height="1002" alt="Screenshot 2026-08-17 072348" src="https://github.com/user-attachments/assets/0f0c3cf2-b7b8-4e72-bcac-78211ff06c88" />


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
