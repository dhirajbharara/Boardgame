BoardGame DevOps Project

This project demonstrates a full DevOps pipeline integrating SonarQube, GitHub Actions, AWS ECR, Docker, Trivy, and EKS (Kubernetes) for CI/CD and deployment.

🚀 Project Overview

Containerized application pushed to AWS ECR

CI/CD pipeline with GitHub Actions

Code quality checks using SonarQube

Security scanning using Trivy

Deployment to Amazon EKS cluster with monitoring via Grafana

📋 Prerequisites

AWS account with IAM role (GitHubActionsECRPushRole) and OIDC identity provider

Terraform installed

Docker installed

AWS CLI configured (ap-south-1 region)

eksctl, kubectl, and helm installed

⚙️ Setup Steps
1. Create AWS ECR Repository
aws ecr create-repository \
  --repository-name boardgame \
  --image-tag-mutability IMMUTABLE \
  --region ap-south-1

2. Deploy SonarQube with Terraform
terraform apply \
  -var="vpc_id=vpc-xxxx" \
  -var="public_subnet_id=subnet-xxxx" \
  -var="allowed_cidrs=[\"<your-ip>\"]" \
  -var="key_name=dhiraj.pem"
<img width="1140" height="764" alt="image" src="https://github.com/user-attachments/assets/d621a3b9-bd6b-4820-bf20-44330b4386c0" />

3. Generate SonarQube Token

Login at http://<ec2-public-ip>:9000

Go to My Account → Security → Tokens

Generate SONAR_TOKEN and save it as a GitHub secret

4. Configure GitHub Secrets

In your repository → Settings → Secrets & Variables → Actions:

SONAR_TOKEN

SONAR_HOST_URL (e.g. http://13.233.20.93:9000/)

AWS credentials and any other required secrets
<img width="823" height="403" alt="image" src="https://github.com/user-attachments/assets/8dcec2f5-6e9c-49db-85c4-38737b786a51" />


5. Add GitHub Actions Workflow

Create .github/workflows/ci.yml including:

SonarQube Scan & Quality Gate

Docker Build

Trivy Image Scan & Report Upload

Push Docker image to AWS ECR
<img width="1604" height="321" alt="image" src="https://github.com/user-attachments/assets/4311854f-9be3-42a3-88c2-f82a603c4253" />

<img width="480" height="820" alt="image" src="https://github.com/user-attachments/assets/f1c4c3a9-bad1-4c0d-9408-1547dddebf5d" />


6. Create and Configure EKS Cluster
eksctl create cluster \
  --name boardgame \
  --version 1.29 \
  --region ap-south-1 \
  --nodegroup-name ng-1 \
  --node-type t3.medium \
  --nodes 2 --nodes-min 2 --nodes-max 4 \
  --managed


Update kubeconfig:

aws eks update-kubeconfig --name boardgame --region ap-south-1
kubectl get nodes


Verify cluster:

aws cloudformation describe-stacks \
  --region ap-south-1 \
  --stack-name eksctl-boardgame-cluster \
  --query 'Stacks[0].StackStatus'

aws eks describe-cluster \
  --name boardgame \
  --region ap-south-1 \
  --query 'cluster.status'

📊 Monitoring

Grafana is integrated for cluster and workload monitoring.

🔑 Credentials

Default SonarQube credentials:

Username: admin

Password: #######

⚠️ Change after first login.

📝 License

This project is for educational and demo purposes.
