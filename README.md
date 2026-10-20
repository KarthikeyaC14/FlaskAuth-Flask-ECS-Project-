# Flask ECS Project 🚀

Simple flask application on AWS using containerisation with Docker, orchestration via AWS ECS (Elastic Container Service), infrastructure provisioning via Terraform, and CI/CD using Jenkins


## Overview

This project demonstrates deploying a simple Flask application on AWS using containerization with Docker, orchestration via AWS ECS (Elastic Container Service), infrastructure provisioning via Terraform, and CI/CD using Jenkins.
It is intended as a full end-to-end “DevOps + Cloud” demo for a micro-service style app: from code → container → infra → deploy → run.


## Features

* A basic Python Flask web application.
* Dockerfile to containerize the Flask app.
* Terraform scripts to provision AWS infrastructure (ECS cluster, task definitions, VPC/subnets, etc).
* Jenkinsfile to define a CI/CD pipeline (build image → push to registry → deploy to ECS).
* `.github/workflows` directory for GitHub actions (if applicable) for further automation.
* Infrastructure as Code (IaC) and container orchestration best practices.


## Architecture

1. Developer pushes code to GitHub.
2. Jenkins (or GitHub Actions) triggers the pipeline:

   * Build the Docker image from `Dockerfile`.
   * Push the image to a container registry (ECR or similar).
   * Trigger Terraform to apply changes if needed (or use Terraform manually).
   * Deploy the container image to AWS ECS (Fargate or EC2 launch type).
3. The Flask app is accessible over HTTP in the AWS environment.
4. Terraform manages underlying infrastructure: VPC, subnets, ECS cluster, task definitions, IAM roles.
5. Monitoring/logging can be added (not included by default) for production readiness.


## Getting Started

### Prerequisites

* AWS account with appropriate permissions (IAM role/user for ECS, ECR, VPC, etc).
* AWS CLI configured locally (`aws configure`).
* Terraform installed (>= 0.12 recommended).
* Docker installed locally for building images.
* Jenkins (optional) setup for CI/CD (or GitHub Actions if preferred).
* (Optional) An ECR (Elastic Container Registry) repository or other registry.

### Clone the repository

```bash
git clone https://github.com/KarthikeyaC14/flask-ecs-project.git
cd flask-ecs-project
```

### Build the Docker image (locally test)

```bash
docker build -t flask-ecs-app:latest .
docker run -p 5000:5000 flask-ecs-app:latest
# Then visit http://localhost:5000 to verify the app runs
```

### Provision infrastructure with Terraform

```bash
cd terraform
terraform init
terraform plan    # review changes
terraform apply   # approve to deploy
```


### Deploy to ECS

* Push Docker image to the registry you configured in Terraform (e.g., AWS ECR).
* Update the task definition in Terraform or manually to reference the new image tag.
* Terraform apply or ECS deploy to rollout the new container.
* Once running, access the service endpoint (load-balancer or public ECS task).

### CI/CD with Jenkins

* The `Jenkinsfile` defines pipeline stages: Build → Push → Deploy.
* Configure Jenkins with credentials (AWS, Docker Registry) and connect to the GitHub repo.
* Set pipeline trigger (e.g., on commits to `main`).
* On successful build, image is deployed automatically to ECS.


## Directory Structure

```
├── app/                  # Flask application source code
│   ├── main.py
│   ├── requirements.txt
│   └── …
├── Dockerfile            # Container definition
├── Jenkinsfile           # CI/CD pipeline definition
├── terraform/            # Infrastructure as Code for AWS
│   ├── main.tf
│   ├── variables.tf
│   └── …
└── .github/
    └── workflows/        # (Optional) GitHub Actions workflows
```


## Customization & Best Practices

* Update `requirements.txt` and Flask app to serve your actual business logic.
* Use versioned Docker images and tag them with Git commit SHA or similar for traceability.
* Secure credentials via AWS IAM roles, Secrets Manager, or GitHub/Jenkins secrets.
* Use Terraform state backend (e.g., S3 + DynamoDB) for team collaboration and state locking.
* Set up rolling deployments, blue/green or canary releases for zero downtime.
* Add health-checks, logging and monitoring (CloudWatch, X-Ray, etc) for production readiness.
* Use environment variables for config (e.g., FLASK_ENV, database URL, etc).
* Implement clean shutdown and signal handling in your Flask container if needed.


## Troubleshooting

* **App not reachable**: Check that ECS service has a public load balancer or public IP, and that the security group allows inbound HTTP (port 80/5000) and the subnet is public.
* **Container image build failing**: Verify your Dockerfile syntax and that `requirements.txt` includes correct dependencies.
* **Terraform errors**: Ensure required AWS permissions, correct region, and that variable values are set. Also check for resource conflicts (VPC, subnets already existing).
* **CI/CD pipeline failing**: Check Jenkins credentials, Docker login step, AWS permissions, and that pipeline environment variables are correctly set.


## What’s Next / Future Enhancements

* Add a database (e.g., RDS or DynamoDB) and integrate with the Flask app.
* Add caching layer (Redis/Elasticache) for performance.
* Add automated tests (unit, integration) and include in pipeline.
* Add environment promotion (dev → staging → prod) with separate Terraform workspaces.
* Enable auto-scaling of ECS tasks based on load.
* Use a service mesh if multiple microservices are involved.
* Container image scanning for security vulnerabilities in CI.
