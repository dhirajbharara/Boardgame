BoardGame DevOps Project

This repository demonstrates the implementation of a full DevOps CI/CD pipeline that integrates SonarQube, GitHub Actions, AWS ECR, Docker, Trivy, and Amazon EKS (Kubernetes). It provides an end-to-end workflow starting from code quality checks to container security scans, image storage, automated deployment, and monitoring with Grafana.

🚀 Project Overview
    
    ✅ Containerization – Application packaged into Docker image
    
    ✅ Static code analysis – Code quality checks using SonarQube
    
    ✅ Continuous Integration – Automated builds and scans with GitHub Actions
    
    ✅ Security scanning – Container image scanned with Trivy
    
    ✅ Image repository – Secure storage and versioning in AWS ECR
    
    ✅ Deployment – Automated deployment into an Amazon EKS cluster
    
    ✅ Monitoring – Metrics and observability with Grafana

📋 Prerequisites

    Before starting, ensure you have the following installed and configured:
    
    An AWS account with:
    
    IAM role → GitHubActionsECRPushRole
    
    OIDC identity provider for GitHub
    
    Terraform (for infrastructure provisioning)
    
    Docker (for container build & push)
    
    AWS CLI (configured with ap-south-1 region)
    
    eksctl, kubectl, and helm

⚙️ Setup Steps

1. Create AWS ECR Repository
    aws ecr create-repository \
      --repository-name boardgame \
      --image-tag-mutability IMMUTABLE \
      --region ap-south-1

2. Deploy SonarQube with Terraform
   Provision an EC2 instance with Terraform and run SonarQube in Docker.
    terraform apply \
      -var="vpc_id=vpc-xxxx" \
      -var="public_subnet_id=subnet-xxxx" \
      -var="allowed_cidrs=[\"<your-ip>\"]" \
      -var="key_name=dhiraj.pem"
    <img width="1140" height="764" alt="image" src="https://github.com/user-attachments/assets/d621a3b9-bd6b-4820-bf20-44330b4386c0" />

4. Generate SonarQube Token

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

    Create a workflow file .github/workflows/ci.yml. The pipeline includes:
    
    🔎 SonarQube Scan & Quality Gate
    
    🐳 Build Docker Image
    
    🔐 Trivy Image Scan
    
    📤 Push Docker Image to AWS ECR
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
