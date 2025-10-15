# DevOps Kubernetes Deployment Project

![Kubernetes](https://img.shields.io/badge/kubernetes-326ce5.svg?&style=for-the-badge&logo=kubernetes&logoColor=white)
![AWS](https://img.shields.io/badge/Amazon_AWS-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)
![Terraform](https://img.shields.io/badge/terraform-5835CC?style=for-the-badge&logo=terraform&logoColor=white)
![Docker](https://img.shields.io/badge/docker-0db7ed?style=for-the-badge&logo=docker&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=for-the-badge&logo=github-actions&logoColor=white)

Automated deployment of a containerized Nginx web application to Amazon EKS using Terraform, Docker, Kubernetes, Helm, and GitHub Actions CI/CD.

## 🌐 Live Application

**Application URL:** http://aba590d76268e4dfd8d8c5d2e1d19d11-1820437635.us-east-1.elb.amazonaws.com

**Grafana Monitoring:** http://ab9b16f198a5c4b798e9d55d148efb60-1084615911.us-east-1.elb.amazonaws.com
- Username: `admin`
- Password: `admin123`

## 🏗️ Architecture
```
┌─────────────────────────────────────────────────────────────────┐
│                         GitHub Repository                        │
│                    (Source Code + Workflows)                     │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             │ Push to main
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                      GitHub Actions CI/CD                        │
│  • Build Docker Image                                            │
│  • Push to Amazon ECR                                            │
│  • Deploy to EKS Cluster                                         │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Amazon EKS Cluster                            │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  Kubernetes Deployment (2-3 Replicas)                    │   │
│  │  • Nginx Pods on Port 8080                               │   │
│  │  • Health Checks & Resource Limits                       │   │
│  └──────────────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  LoadBalancer Service                                     │   │
│  │  • Exposes Port 80 → Pods 8080                           │   │
│  └──────────────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  Prometheus & Grafana (Monitoring)                        │   │
│  │  • Metrics Collection                                     │   │
│  │  • Pre-built Dashboards                                  │   │
│  └──────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

## 🚀 Features

### Core Requirements ✅
- ✅ **Infrastructure as Code**: Terraform provisions complete EKS cluster with VPC, subnets, NAT gateways
- ✅ **Containerization**: Optimized multi-stage Dockerfile with Alpine Linux (20MB image)
- ✅ **Kubernetes Deployment**: Production-ready manifests with health checks and resource limits
- ✅ **CI/CD Pipeline**: GitHub Actions automates build, push, and deployment
- ✅ **LoadBalancer Service**: Public access via AWS Application Load Balancer

### Bonus Features 🌟
- ✅ **Helm Chart**: Templated deployment for easy configuration management
- ✅ **Secrets Management**: GitHub Secrets for AWS credentials and sensitive data
- ✅ **Monitoring**: Prometheus and Grafana stack with pre-configured dashboards
- ✅ **Security**: Non-root container user, vulnerability scanning in ECR

## 📁 Project Structure
```
devops-k8s-deployment/
├── app/
│   ├── Dockerfile              # Optimized Alpine-based Nginx image
│   ├── index.html              # Application frontend
│   ├── nginx.conf              # Nginx server configuration
│   └── .dockerignore           # Docker build exclusions
├── terraform/
│   ├── main.tf                 # Provider and main configuration
│   ├── vpc.tf                  # VPC, subnets, NAT gateways
│   ├── eks.tf                  # EKS cluster and node groups
│   ├── ecr.tf                  # ECR repository
│   ├── variables.tf            # Input variables
│   └── outputs.tf              # Output values
├── k8s/
│   ├── deployment.yaml         # Kubernetes deployment
│   ├── service.yaml            # LoadBalancer service
│   └── namespace.yaml          # Namespace definition
├── helm/
│   └── devops-k8s-app/         # Helm chart
│       ├── Chart.yaml
│       ├── values.yaml
│       └── templates/
├── .github/
│   └── workflows/
│       └── deploy.yml          # GitHub Actions CI/CD pipeline
├── README.md
└── MONITORING.md
```

## 🛠️ Technology Stack

| Component | Technology |
|-----------|------------|
| **Cloud Provider** | Amazon Web Services (AWS) |
| **Container Orchestration** | Amazon EKS (Kubernetes 1.28) |
| **Infrastructure as Code** | Terraform |
| **Container Registry** | Amazon ECR |
| **CI/CD** | GitHub Actions |
| **Application** | Nginx (Alpine Linux) |
| **Package Manager** | Helm 3 |
| **Monitoring** | Prometheus + Grafana |

## 📋 Prerequisites

- AWS Account with appropriate permissions
- AWS CLI configured
- Terraform >= 1.0
- kubectl
- Helm 3
- Docker
- Git

## 🚀 Deployment Instructions

### 1. Clone the Repository
```bash
git clone https://github.com/Kamlesh-Sabale/devops-k8s-deployment.git
cd devops-k8s-deployment
```

### 2. Configure AWS Credentials
```bash
aws configure
# Enter: Access Key ID, Secret Access Key, Region (us-east-1)
```

### 3. Deploy Infrastructure with Terraform
```bash
cd terraform
terraform init
terraform plan
terraform apply -auto-approve
```

**This provisions:**
- VPC with public/private subnets
- NAT Gateways and Internet Gateway
- EKS Cluster (v1.28)
- EKS Node Group (2 t3.medium instances)
- ECR Repository

**Time:** ~15 minutes

### 4. Configure kubectl
```bash
aws eks update-kubeconfig --region us-east-1 --name devops-k8s-cluster
kubectl get nodes
```

### 5. Build and Push Docker Image
```bash
cd ../app

# Login to ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com

# Build and push
docker build -t <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/devops-k8s-app:latest .
docker push <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/devops-k8s-app:latest
```

### 6. Deploy Application

**Option A: Using kubectl**
```bash
cd ../k8s
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl get service devops-k8s-app-service
```

**Option B: Using Helm**
```bash
helm install devops-k8s-app ./helm/devops-k8s-app
helm list
```

### 7. Setup CI/CD (GitHub Actions)

1. Go to **Settings → Secrets → Actions**
2. Add secrets:
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
   - `AWS_REGION` = `us-east-1`
   - `ECR_REPOSITORY` = `devops-k8s-app`
   - `EKS_CLUSTER_NAME` = `devops-k8s-cluster`

3. Push changes to `main` branch to trigger pipeline

### 8. Install Monitoring (Optional)
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

helm install prometheus prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --create-namespace \
  --set grafana.adminPassword=admin123 \
  --set grafana.service.type=LoadBalancer
```

## 🎯 Design Decisions

### 1. **Alpine Linux Base Image**
- Reduced image size from 150MB to 20MB
- Faster builds and deployments
- Smaller attack surface

### 2. **Non-root Container User**
- Security best practice
- Changed from port 80 to 8080 (non-privileged port)
- Prevents privilege escalation

### 3. **High Availability**
- Multi-AZ deployment (2 availability zones)
- 2-3 pod replicas with anti-affinity
- LoadBalancer distributes traffic

### 4. **Infrastructure as Code**
- Version-controlled infrastructure
- Reproducible deployments
- Easy disaster recovery

### 5. **GitOps Workflow**
- Single source of truth (Git)
- Automated deployments on push
- Audit trail of all changes

## 📊 Monitoring

Access Grafana dashboards to view:
- Cluster resource utilization
- Pod metrics (CPU, memory, network)
- Node health
- Application performance

**Key Dashboards:**
- Kubernetes / Compute Resources / Cluster
- Kubernetes / Compute Resources / Namespace
- Node Exporter / Nodes

## 🔒 Security Features

- ECR image vulnerability scanning
- Non-root container execution
- Resource limits and quotas
- Network policies
- Secrets stored in GitHub Secrets
- IAM roles with least privilege

## 💰 Cost Optimization

- EKS: ~$73/month (control plane)
- EC2 (t3.medium × 2): ~$60/month
- NAT Gateways: ~$65/month
- LoadBalancers: ~$20/month
- **Total: ~$220/month**

**To minimize costs:**
```bash
# Destroy infrastructure after testing
cd terraform
terraform destroy -auto-approve
```

## 🧪 Testing
```bash
# Check deployment
kubectl get deployments
kubectl get pods
kubectl get services

# Test application
APP_URL=$(kubectl get service devops-k8s-app-service -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')
curl http://$APP_URL
curl http://$APP_URL/health

# View logs
kubectl logs -l app=devops-k8s-app

# Scale deployment
kubectl scale deployment devops-k8s-app --replicas=5
```

## 🐛 Troubleshooting

### Pods not starting
```bash
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

### LoadBalancer pending
```bash
kubectl describe service devops-k8s-app-service
# Wait 2-3 minutes for AWS to provision
```

### Image pull errors
```bash
# Verify ECR authentication
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <ECR_URL>
```

## 📚 Additional Resources

- [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [Amazon EKS Documentation](https://docs.aws.amazon.com/eks/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Helm Documentation](https://helm.sh/docs/)
- [GitHub Actions](https://docs.github.com/en/actions)

## 👤 Author

**Kamlesh Sabale**
- GitHub: [@Kamlesh-Sabale](https://github.com/Kamlesh-Sabale)

## 📄 License

This project is created for DevOps learning and demonstration purposes.

---

**⭐ If you found this project helpful, please give it a star!**

