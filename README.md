# Dockerized Web Application using AWS Services

## 🎯 Project Overview

This project demonstrates a complete end-to-end CI/CD pipeline for deploying a Dockerized web application to AWS using multiple AWS services. The application is a modern DevOps Engineer portfolio website that showcases skills, projects, and services.

### Key Features:
- **Containerized Application**: Web app containerized with Docker
- **Automated CI/CD Pipeline**: Using AWS CodePipeline and CodeBuild
- **Container Registry**: Amazon ECR for storing Docker images
- **Container Orchestration**: Amazon ECS for running containers
- **Infrastructure as Code**: Automated deployment with buildspec.yml
- **Modern UI**: Responsive portfolio website with interactive features

---

## 🏗️ Architecture

### High-Level Architecture

```
┌─────────────────┐
│   Developer     │
│   (Git Push)    │
└────────┬────────┘
         │
         ▼
┌─────────────────────────────────────┐
│      AWS CodePipeline               │
│  ┌───────────────────────────────┐  │
│  │   Source Stage                │  │
│  │   (GitHub/CodeCommit)         │  │
│  └───────────┬───────────────────┘  │
│              │                      │
│              ▼                      │
│  ┌───────────────────────────────┐  │
│  │   Build Stage                 │  │
│  │   (AWS CodeBuild)             │  │
│  │   - Login to ECR              │  │
│  │   - Build Docker Image        │  │
│  │   - Push to ECR               │  │
│  │   - Generate imagedefinitions │  │
│  └───────────┬───────────────────┘  │
│              │                      │
│              ▼                      │
│  ┌───────────────────────────────┐  │
│  │   Deploy Stage                │  │
│  │   (ECS Deployment)            │  │
│  └───────────┬───────────────────┘  │
└──────────────┼──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│      Amazon ECR                     │
│   (Container Registry)              │
│   Repository: ecr-web-app           │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│      Amazon ECS                     │
│   (Container Service)               │
│   - Cluster                         │
│   - Service                         │
│   - Task Definition                 │
│   - Fargate/EC2 Launch Type         │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│   Application Load Balancer (ALB)   │
│   - Public Endpoint                 │
│   - Health Checks                   │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│     End Users                       │
│   (Web Browser)                     │
└─────────────────────────────────────┘
```

---

## ☁️ AWS Services Used

### 1. **Amazon ECR (Elastic Container Registry)**
- **Purpose**: Store and manage Docker container images
- **Key Features**:
  - Private Docker image repository
  - Image scanning for vulnerabilities
  - Lifecycle policies for image management
  - Integration with ECS and CodeBuild

### 2. **Amazon ECS (Elastic Container Service)**
- **Purpose**: Run and manage Docker containers at scale
- **Key Features**:
  - Container orchestration
  - Auto-scaling capabilities
  - Integration with ALB
  - Fargate (serverless) or EC2 launch types

### 3. **AWS CodeBuild**
- **Purpose**: Build and test code
- **Key Features**:
  - Fully managed build service
  - Supports Docker builds
  - Customizable build environments
  - Integration with ECR

### 4. **AWS CodePipeline**
- **Purpose**: Automate release pipelines
- **Key Features**:
  - Visual workflow editor
  - Multiple source providers
  - Automated deployments
  - Pipeline execution history

### 5. **IAM (Identity and Access Management)**
- **Purpose**: Secure access control
- **Key Features**:
  - Role-based access control
  - Service roles for CodeBuild and CodePipeline
  - ECR and ECS permissions
  - Least privilege principle

---

## 📦 Prerequisites

### Required Tools:
1. **AWS CLI** (v2.x or later)
   ```bash
   # Download from: https://aws.amazon.com/cli/
   aws --version
   ```

2. **Docker** (Desktop or Engine)
   ```bash
   # Download from: https://www.docker.com/products/docker-desktop
   docker --version
   ```

3. **Git**
   ```bash
   # Download from: https://git-scm.com/downloads
   git --version
   ```

4. **Python 3.11+**
   ```bash
   python --version
   ```

5. **AWS Account** with appropriate permissions

### AWS Account Setup:
- Active AWS account
- IAM user with programmatic access
- AWS CLI configured with credentials
- Appropriate IAM permissions for:
  - ECR (full access)
  - ECS (full access)
  - CodeBuild (full access)
  - CodePipeline (full access)
  - IAM (role creation)

---

## 📁 Project Structure

```
AWS_resource/
│
├── app.py                      # Flask application entry point
├── Dockerfile                  # Docker image definition
├── buildspec.yml              # AWS CodeBuild configuration
├── requirements.txt           # Python dependencies
├── README.md                  # This file
│
├── static/
│   ├── style.css             # CSS stylesheet
│   └── script.js             # JavaScript functionality
│
└── templates/
    └── index.html            # HTML template
```

### File Descriptions:

#### `app.py`
- Flask web application
- Single route (`/`) that renders the portfolio template
- Runs on port 5000, accessible from all interfaces (0.0.0.0)

#### `Dockerfile`
- Multi-stage Python 3.11 slim image
- Copies requirements and installs dependencies
- Exposes port 5000
- Runs the Flask application

#### `buildspec.yml`
- CodeBuild configuration file
- Defines build phases: pre_build, build, post_build
- Handles ECR login, Docker build, and image push
- Generates `imagedefinitions.json` for ECS deployment

#### `requirements.txt`
- Python package dependencies
- Currently includes Flask

#### `static/style.css`
- Modern, responsive CSS styling
- Gradient themes, animations, and mobile-responsive design

#### `static/script.js`
- Interactive JavaScript features
- Mobile menu, smooth scrolling, form validation
- Testimonial carousel, animations

#### `templates/index.html`
- Portfolio website HTML structure
- Sections: Hero, About, Portfolio, Services, Contact

---

## 🚀 Step-by-Step Implementation Guide

### Phase 1: Initial Setup

#### Step 1.1: Configure AWS CLI
```bash
aws configure
# Enter:
# - AWS Access Key ID
# - AWS Secret Access Key
# - Default region name: eu-north-1
# - Default output format: json
```

#### Step 1.2: Clone/Prepare Repository
```bash
# If using Git
git clone <your-repository-url>
cd AWS_resource

# Or prepare your local files
```

#### Step 1.3: Test Application Locally
```bash
# Install dependencies
pip install -r requirements.txt

# Run Flask app
python app.py

# Test in browser: http://localhost:5000
```

---

### Phase 2: Create ECR Repository

#### Step 2.1: Create ECR Repository
```bash
aws ecr create-repository \
    --repository-name ecr-web-app \
    --region eu-north-1 \
    --image-scanning-configuration scanOnPush=true \
    --encryption-configuration encryptionType=AES256
```

**Expected Output:**
```json
{
    "repository": {
        "repositoryArn": "arn:aws:ecr:eu-north-1:831516339607:repository/ecr-web-app",
        "registryId": "831516339607",
        "repositoryName": "ecr-web-app",
        "repositoryUri": "831516339607.dkr.ecr.eu-north-1.amazonaws.com/ecr-web-app",
        "createdAt": "2024-01-01T00:00:00.000Z"
    }
}
```

#### Step 2.2: Get ECR Login Token
```bash
aws ecr get-login-password --region eu-north-1 | \
    docker login --username AWS --password-stdin \
    831516339607.dkr.ecr.eu-north-1.amazonaws.com
```

#### Step 2.3: Build and Push Docker Image (Manual Test)
```bash
# Build image
docker build -t ecr-web-app:latest .

# Tag image
docker tag ecr-web-app:latest \
    831516339607.dkr.ecr.eu-north-1.amazonaws.com/ecr-web-app:latest

# Push to ECR
docker push \
    831516339607.dkr.ecr.eu-north-1.amazonaws.com/ecr-web-app:latest
```

---

### Phase 3: Create IAM Roles

#### Step 3.1: Create CodeBuild Service Role

**Policy Document** (`codebuild-trust-policy.json`):
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "codebuild.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

**Create Role:**
```bash
aws iam create-role \
    --role-name CodeBuildServiceRole \
    --assume-role-policy-document file://codebuild-trust-policy.json
```

**Attach Policies:**
```bash
# Attach managed policies
aws iam attach-role-policy \
    --role-name CodeBuildServiceRole \
    --policy-arn arn:aws:iam::aws:policy/CloudWatchLogsFullAccess

aws iam attach-role-policy \
    --role-name CodeBuildServiceRole \
    --policy-arn arn:aws:iam::aws:policy/AmazonEC2ContainerRegistryPowerUser

aws iam attach-role-policy \
    --role-name CodeBuildServiceRole \
    --policy-arn arn:aws:iam::aws:policy/AmazonECS_FullAccess
```

#### Step 3.2: Create CodePipeline Service Role

**Policy Document** (`codepipeline-trust-policy.json`):
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "codepipeline.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

**Create Role:**
```bash
aws iam create-role \
    --role-name CodePipelineServiceRole \
    --assume-role-policy-document file://codepipeline-trust-policy.json
```

**Attach Policies:**
```bash
aws iam attach-role-policy \
    --role-name CodePipelineServiceRole \
    --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess

aws iam attach-role-policy \
    --role-name CodePipelineServiceRole \
    --policy-arn arn:aws:iam::aws:policy/AmazonEC2ContainerRegistryPowerUser

aws iam attach-role-policy \
    --role-name CodePipelineServiceRole \
    --policy-arn arn:aws:iam::aws:policy/AmazonECS_FullAccess

aws iam attach-role-policy \
    --role-name CodePipelineServiceRole \
    --policy-arn arn:aws:iam::aws:policy/AWSCodeBuildDeveloperAccess
```

#### Step 3.3: Create ECS Task Execution Role

**Policy Document** (`ecs-trust-policy.json`):
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "ecs-tasks.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

**Create Role:**
```bash
aws iam create-role \
    --role-name ecsTaskExecutionRole \
    --assume-role-policy-document file://ecs-trust-policy.json

# Attach managed policy
aws iam attach-role-policy \
    --role-name ecsTaskExecutionRole \
    --policy-arn arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy
```

---

### Phase 4: Create ECS Cluster and Service

#### Step 4.1: Create ECS Cluster
```bash
aws ecs create-cluster \
    --cluster-name flask-web-app-cluster \
    --region eu-north-1
```

#### Step 4.2: Create Task Definition

**Task Definition** (`task-definition.json`):
```json
{
  "family": "flask-web-app",
  "networkMode": "awsvpc",
  "requiresCompatibilities": ["FARGATE"],
  "cpu": "256",
  "memory": "512",
  "executionRoleArn": "arn:aws:iam::831516339607:role/ecsTaskExecutionRole",
  "containerDefinitions": [
    {
      "name": "web",
      "image": "831516339607.dkr.ecr.eu-north-1.amazonaws.com/ecr-web-app:latest",
      "portMappings": [
        {
          "containerPort": 5000,
          "protocol": "tcp"
        }
      ],
      "essential": true,
      "logConfiguration": {
        "logDriver": "awslogs",
        "options": {
          "awslogs-group": "/ecs/flask-web-app",
          "awslogs-region": "eu-north-1",
          "awslogs-stream-prefix": "ecs"
        }
      }
    }
  ]
}
```

**Register Task Definition:**
```bash
aws ecs register-task-definition \
    --cli-input-json file://task-definition.json \
    --region eu-north-1
```

#### Step 4.3: Create CloudWatch Log Group
```bash
aws logs create-log-group \
    --log-group-name /ecs/flask-web-app \
    --region eu-north-1
```

#### Step 4.4: Create VPC and Networking (if needed)

**Note**: For Fargate, you need:
- VPC with public subnets
- Security group allowing inbound traffic on port 5000
- Internet Gateway

**Create Security Group:**
```bash
aws ec2 create-security-group \
    --group-name flask-app-sg \
    --description "Security group for Flask web app" \
    --vpc-id <your-vpc-id> \
    --region eu-north-1

# Add inbound rule
aws ec2 authorize-security-group-ingress \
    --group-id <security-group-id> \
    --protocol tcp \
    --port 5000 \
    --cidr 0.0.0.0/0 \
    --region eu-north-1
```

#### Step 4.5: Create ECS Service (Optional - for manual deployment)
```bash
aws ecs create-service \
    --cluster flask-web-app-cluster \
    --service-name flask-web-app-service \
    --task-definition flask-web-app \
    --desired-count 1 \
    --launch-type FARGATE \
    --network-configuration "awsvpcConfiguration={subnets=[<subnet-id>],securityGroups=[<sg-id>],assignPublicIp=ENABLED}" \
    --region eu-north-1
```

---

### Phase 5: Create CodeBuild Project

#### Step 5.1: Create S3 Bucket for Artifacts (Optional)
```bash
aws s3 mb s3://codebuild-artifacts-flask-app --region eu-north-1
```

#### Step 5.2: Create CodeBuild Project

**Build Project Configuration** (`codebuild-project.json`):
```json
{
  "name": "flask-web-app-build",
  "description": "Build and push Docker image to ECR",
  "source": {
    "type": "CODEPIPELINE",
    "buildspec": "AWS_resource/buildspec.yml"
  },
  "artifacts": {
    "type": "CODEPIPELINE"
  },
  "environment": {
    "type": "LINUX_CONTAINER",
    "image": "aws/codebuild/standard:5.0",
    "computeType": "BUILD_GENERAL1_SMALL",
    "privilegedMode": true,
    "environmentVariables": [
      {
        "name": "AWS_DEFAULT_REGION",
        "value": "eu-north-1"
      },
      {
        "name": "ECR_REPO_URI",
        "value": "831516339607.dkr.ecr.eu-north-1.amazonaws.com/ecr-web-app"
      },
      {
        "name": "IMAGE_TAG",
        "value": "latest"
      }
    ]
  },
  "serviceRole": "arn:aws:iam::831516339607:role/CodeBuildServiceRole"
}
```

**Create CodeBuild Project:**
```bash
aws codebuild create-project \
    --cli-input-json file://codebuild-project.json \
    --region eu-north-1
```

---

### Phase 6: Create CodePipeline

#### Step 6.1: Create S3 Bucket for Pipeline Artifacts
```bash
aws s3 mb s3://codepipeline-artifacts-flask-app --region eu-north-1
```

#### Step 6.2: Create Pipeline Configuration

**Pipeline Configuration** (`pipeline.json`):
```json
{
  "pipeline": {
    "name": "flask-web-app-pipeline",
    "roleArn": "arn:aws:iam::831516339607:role/CodePipelineServiceRole",
    "artifactStore": {
      "type": "S3",
      "location": "codepipeline-artifacts-flask-app"
    },
    "stages": [
      {
        "name": "Source",
        "actions": [
          {
            "name": "Source",
            "actionTypeId": {
              "category": "Source",
              "owner": "AWS",
              "provider": "S3",
              "version": "1"
            },
            "outputArtifacts": [
              {
                "name": "SourceOutput"
              }
            ],
            "configuration": {
              "S3Bucket": "your-source-bucket",
              "S3ObjectKey": "source.zip"
            }
          }
        ]
      },
      {
        "name": "Build",
        "actions": [
          {
            "name": "Build",
            "actionTypeId": {
              "category": "Build",
              "owner": "AWS",
              "provider": "CodeBuild",
              "version": "1"
            },
            "inputArtifacts": [
              {
                "name": "SourceOutput"
              }
            ],
            "outputArtifacts": [
              {
                "name": "BuildOutput"
              }
            ],
            "configuration": {
              "ProjectName": "flask-web-app-build"
            }
          }
        ]
      },
      {
        "name": "Deploy",
        "actions": [
          {
            "name": "Deploy",
            "actionTypeId": {
              "category": "Deploy",
              "owner": "AWS",
              "provider": "ECS",
              "version": "1"
            },
            "inputArtifacts": [
              {
                "name": "BuildOutput"
              }
            ],
            "configuration": {
              "ClusterName": "flask-web-app-cluster",
              "ServiceName": "flask-web-app-service",
              "FileName": "imagedefinitions.json"
            }
          }
        ]
      }
    ]
  }
}
```

**Note**: For GitHub/CodeCommit source, modify the Source stage accordingly.

**Create Pipeline:**
```bash
aws codepipeline create-pipeline \
    --cli-input-json file://pipeline.json \
    --region eu-north-1
```

---

## 🔄 Workflow Diagrams

### Complete CI/CD Workflow

```
┌─────────────────────────────────────────────────────────────────┐
│                    CI/CD PIPELINE WORKFLOW                       │
└─────────────────────────────────────────────────────────────────┘

Developer
    │
    │ git push
    ▼
┌─────────────────┐
│  Source Stage   │
│  (CodeCommit/   │
│   GitHub/S3)    │
└────────┬────────┘
         │
         │ Triggers Pipeline
         ▼
┌─────────────────┐
│  Build Stage    │
│  (CodeBuild)    │
│                 │
│  ┌───────────┐  │
│  │ Pre-Build │  │
│  │ - ECR     │  │
│  │   Login   │  │
│  └─────┬─────┘  │
│        │        │
│        ▼        │
│  ┌───────────┐  │
│  │   Build   │  │
│  │ - Docker  │  │
│  │   Build   │  │
│  └─────┬─────┘  │
│        │        │
│        ▼        │
│  ┌───────────┐  │
│  │ Post-Build│  │
│  │ - Push to │  │
│  │   ECR     │  │
│  │ - Generate│  │
│  │   image   │  │
│  │   defs    │  │
│  └─────┬─────┘  │
└────────┼────────┘
         │
         │ Artifacts
         ▼
┌─────────────────┐
│  Deploy Stage   │
│  (ECS)          │
│                 │
│  - Update       │
│    Service      │
│  - Deploy New   │
│    Task         │
│  - Health Check │
└────────┬────────┘
         │
         │ Application Running
         ▼
┌─────────────────┐
│   End Users     │
│   (Browser)     │
└─────────────────┘
```

### Build Process Flow

```
┌─────────────────────────────────────────────────────────────┐
│              CODEBUILD EXECUTION FLOW                        │
└─────────────────────────────────────────────────────────────┘

Start Build
    │
    ▼
┌──────────────────┐
│  Environment     │
│  Setup           │
│  - Pull Image    │
│  - Install Tools │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  Pre-Build Phase │
│                  │
│  1. ECR Login    │
│     aws ecr      │
│     get-login-   │
│     password     │
│                  │
│  2. Docker Login │
│     docker login │
│     ECR          │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  Build Phase     │
│                  │
│  1. cd AWS_      │
│     resource     │
│                  │
│  2. docker build │
│     -t $ECR_     │
│     REPO_URI:    │
│     $IMAGE_TAG   │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  Post-Build      │
│  Phase           │
│                  │
│  1. docker push  │
│     $ECR_REPO_   │
│     URI:$TAG     │
│                  │
│  2. Generate     │
│     imagedefini- │
│     tions.json   │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  Artifacts       │
│  - imagedefini-  │
│    tions.json    │
└──────────────────┘
```

### ECS Deployment Flow

```
┌─────────────────────────────────────────────────────────────┐
│              ECS DEPLOYMENT WORKFLOW                        │
└─────────────────────────────────────────────────────────────┘

CodePipeline
Deploy Action
    │
    ▼
┌──────────────────┐
│  Read            │
│  imagedefini-    │
│  tions.json      │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  Update ECS      │
│  Service         │
│                  │
│  - New Task      │
│    Definition    │
│  - Force New     │
│    Deployment    │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  ECS Service     │
│  Actions         │
│                  │
│  1. Stop Old     │
│     Tasks        │
│  2. Start New    │
│     Tasks        │
│  3. Health       │
│     Checks       │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  Task Running    │
│  - Container     │
│    Started       │
│  - Port 5000     │
│    Exposed       │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  ALB Health      │
│  Check           │
│  - /health       │
│  - 200 OK        │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  Traffic         │
│  Routing         │
│  - New Tasks     │
│    Receive       │
│    Traffic       │
└──────────────────┘
```

---

## ⚙️ Configuration Files

### buildspec.yml

```yaml
version: 0.2

env:
  variables:
    AWS_DEFAULT_REGION: eu-north-1
    ECR_REPO_URI: 831516339607.dkr.ecr.eu-north-1.amazonaws.com/ecr-web-app
    IMAGE_TAG: latest

phases:
  pre_build:
    commands:
      - echo Logging in to Amazon ECR
      - aws ecr get-login-password --region $AWS_DEFAULT_REGION |
        docker login --username AWS --password-stdin $ECR_REPO_URI

  build:
    commands:
      - echo Build started
      - cd AWS_resource
      - docker build -t $ECR_REPO_URI:$IMAGE_TAG .

  post_build:
    commands:
      - echo Push image
      - docker push $ECR_REPO_URI:$IMAGE_TAG
      - printf '[{"name":"web","imageUri":"%s"}]' $ECR_REPO_URI:$IMAGE_TAG > imagedefinitions.json

artifacts:
  files:
    - AWS_resource/imagedefinitions.json
```

**Explanation:**
- **version**: Buildspec format version
- **env.variables**: Environment variables used throughout build
- **pre_build**: Authenticate with ECR before building
- **build**: Build Docker image from Dockerfile
- **post_build**: Push image to ECR and generate ECS deployment file
- **artifacts**: Files to pass to next pipeline stage

### Dockerfile

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]
```

**Explanation:**
- **FROM**: Base Python 3.11 slim image
- **WORKDIR**: Set working directory
- **COPY requirements.txt**: Copy dependency file
- **RUN pip install**: Install Python packages
- **COPY . .**: Copy application files
- **EXPOSE**: Expose port 5000
- **CMD**: Run Flask application

---

## 🚢 Deployment Process

### Manual Deployment Steps

1. **Build and Push to ECR**
   ```bash
   aws ecr get-login-password --region eu-north-1 | \
       docker login --username AWS --password-stdin \
       831516339607.dkr.ecr.eu-north-1.amazonaws.com
   
   docker build -t ecr-web-app:latest .
   
   docker tag ecr-web-app:latest \
       831516339607.dkr.ecr.eu-north-1.amazonaws.com/ecr-web-app:latest
   
   docker push \
       831516339607.dkr.ecr.eu-north-1.amazonaws.com/ecr-web-app:latest
   ```

2. **Update ECS Service**
   ```bash
   aws ecs update-service \
       --cluster flask-web-app-cluster \
       --service flask-web-app-service \
       --force-new-deployment \
       --region eu-north-1
   ```

3. **Monitor Deployment**
   ```bash
   aws ecs describe-services \
       --cluster flask-web-app-cluster \
       --services flask-web-app-service \
       --region eu-north-1
   ```

### Automated Deployment via Pipeline

1. **Trigger Pipeline**
   - Push code to source repository
   - Or manually start pipeline in AWS Console

2. **Monitor Pipeline**
   - View progress in CodePipeline console
   - Check build logs in CodeBuild
   - Monitor ECS service deployment

3. **Verify Deployment**
   - Check ECS service status
   - Test application endpoint
   - Review CloudWatch logs

---

## 🔧 Troubleshooting

### Common Issues and Solutions

#### 1. ECR Login Failed
**Error**: `Unable to locate credentials`
**Solution**:
```bash
# Verify AWS credentials
aws sts get-caller-identity

# Re-configure AWS CLI
aws configure
```

#### 2. Docker Build Failed
**Error**: `Cannot connect to Docker daemon`
**Solution**:
- Ensure Docker is running
- Check CodeBuild environment has `privilegedMode: true`

#### 3. ECS Deployment Failed
**Error**: `Service does not have a stable state`
**Solution**:
```bash
# Check service events
aws ecs describe-services \
    --cluster flask-web-app-cluster \
    --services flask-web-app-service

# Check task status
aws ecs list-tasks \
    --cluster flask-web-app-cluster \
    --service-name flask-web-app-service
```

#### 4. Image Pull Failed
**Error**: `CannotPullContainerError`
**Solution**:
- Verify ECR repository exists
- Check IAM permissions for ECS task execution role
- Ensure image tag is correct

#### 5. Port Not Accessible
**Error**: Application not reachable
**Solution**:
- Verify security group allows inbound traffic on port 5000
- Check ALB target group health checks
- Verify ECS task is running and healthy

---

## 📥 Downloadable Resources

### Configuration Files

1. **buildspec.yml** - CodeBuild configuration
   - Location: `AWS_resource/buildspec.yml`
   - Download: Already in repository

2. **Dockerfile** - Docker image definition
   - Location: `AWS_resource/Dockerfile`
   - Download: Already in repository

3. **task-definition.json** - ECS task definition template
   - Create using the template provided in Step 4.2

4. **IAM Policy Documents** - Trust policies for IAM roles
   - Create using templates provided in Phase 3

### AWS CLI Commands Script

Create `setup.sh` with all setup commands:
```bash
#!/bin/bash

# ECR Setup
aws ecr create-repository --repository-name ecr-web-app --region eu-north-1

# ECS Cluster
aws ecs create-cluster --cluster-name flask-web-app-cluster --region eu-north-1

# CloudWatch Logs
aws logs create-log-group --log-group-name /ecs/flask-web-app --region eu-north-1

# CodeBuild Project (use JSON file)
aws codebuild create-project --cli-input-json file://codebuild-project.json

# CodePipeline (use JSON file)
aws codepipeline create-pipeline --cli-input-json file://pipeline.json
```

### Documentation

- **AWS ECR User Guide**: https://docs.aws.amazon.com/ecr/
- **AWS ECS User Guide**: https://docs.aws.amazon.com/ecs/
- **AWS CodeBuild User Guide**: https://docs.aws.amazon.com/codebuild/
- **AWS CodePipeline User Guide**: https://docs.aws.amazon.com/codepipeline/

---

## 📚 References

### AWS Service Documentation

1. **Amazon ECR**
   - Documentation: https://docs.aws.amazon.com/ecr/
   - Pricing: https://aws.amazon.com/ecr/pricing/
   - Best Practices: https://docs.aws.amazon.com/ecr/latest/userguide/best-practices.html

2. **Amazon ECS**
   - Documentation: https://docs.aws.amazon.com/ecs/
   - Getting Started: https://docs.aws.amazon.com/ecs/latest/developerguide/getting-started.html
   - Task Definitions: https://docs.aws.amazon.com/ecs/latest/developerguide/task_definitions.html

3. **AWS CodeBuild**
   - Documentation: https://docs.aws.amazon.com/codebuild/
   - Buildspec Reference: https://docs.aws.amazon.com/codebuild/latest/userguide/buildspec-ref.html
   - Environment Variables: https://docs.aws.amazon.com/codebuild/latest/userguide/build-env-ref.html

4. **AWS CodePipeline**
   - Documentation: https://docs.aws.amazon.com/codepipeline/
   - Pipeline Structure: https://docs.aws.amazon.com/codepipeline/latest/userguide/pipelines.html
   - Integration with ECS: https://docs.aws.amazon.com/codepipeline/latest/userguide/ecs-cd-pipeline.html

5. **IAM**
   - Documentation: https://docs.aws.amazon.com/iam/
   - Best Practices: https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html
   - Service Roles: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html

### Related Technologies

- **Docker**: https://docs.docker.com/
- **Flask**: https://flask.palletsprojects.com/
- **Python**: https://www.python.org/doc/

### Additional Resources

- **AWS Well-Architected Framework**: https://aws.amazon.com/architecture/well-architected/
- **AWS Architecture Center**: https://aws.amazon.com/architecture/
- **AWS Training**: https://aws.amazon.com/training/

---

## 🎓 Learning Path

### For Beginners:
1. Understand Docker basics
2. Learn AWS fundamentals (EC2, VPC, IAM)
3. Study ECR and ECS concepts
4. Practice with AWS CLI commands
5. Build simple CI/CD pipelines

### For Intermediate:
1. Implement Infrastructure as Code (Terraform/CloudFormation)
2. Add monitoring and logging (CloudWatch)
3. Implement auto-scaling
4. Add security scanning
5. Optimize costs

### For Advanced:
1. Multi-region deployments
2. Blue/Green deployments
3. Canary deployments
4. Advanced monitoring and alerting
5. Disaster recovery strategies

---

## 📝 Summary

This project demonstrates a complete CI/CD pipeline for containerized applications on AWS:

1. **Source**: Code stored in Git repository
2. **Build**: CodeBuild builds Docker image
3. **Registry**: ECR stores container images
4. **Deploy**: ECS runs containers
5. **Automation**: CodePipeline orchestrates the entire process

### Key Takeaways:
- ✅ Containerized Flask application
- ✅ Automated CI/CD pipeline
- ✅ Secure image storage in ECR
- ✅ Scalable container orchestration with ECS
- ✅ Infrastructure automation
- ✅ Best practices for AWS services

---

## 🤝 Contributing

To contribute to this project:
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

---

## 📄 License

This project is provided as-is for educational purposes.

---

## 👤 Author

**DevOps Engineer**
- Portfolio: This application
- Technologies: AWS, Docker, Flask, CI/CD

---

## 📞 Support

For issues or questions:
- Check AWS documentation
- Review troubleshooting section
- Check CloudWatch logs
- Verify IAM permissions

---

**Last Updated**: 2024
**Version**: 1.0.0


