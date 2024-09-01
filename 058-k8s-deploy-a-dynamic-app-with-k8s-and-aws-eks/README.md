# Deploy a Dynamic App with Kubernetes and AWS EKS using Terraform and Kubernetes Manifest Files


## Overview

This project demonstrates how to deploy a Dynamic App with Kubernetes and AWS EKS using Terraform and Kubernetes Manifest Files. Automation tools used are Terraform and PowerShell. The setup includes VPC, IAM, R53, EIC Endpoint, S3, ACM, Secrets manager, ECR, NLB, EKS, and RDS.


## Reference Diagram
![Reference Diagram](images/Deploy-a-Dynamic-Web-App-on-AWS-with-Docker-ECR-ECS-GitHub-Actions.png)

## Project Components

1. **VPC**: with internet gateway, public and private subnets in two availability zones.
2. **NAT Gateways**: for outbound internet access.
3. **Security Groups**: for EIC Endpoint, data migration, and database.
4. **EIC endpoint**: provides a secure solution to connect to our instances via SSH.
5. **RDS**: holds our data for our dynamic application.
6. **ACM**: deploys SSL/TLS certificate.
7. **S3**: holds our web files and scripts.
8. **Secrets Manager**: securely stores our credentials.
9. **IAM Role and User**: delegates access permission for our S3, Secrets Manager, EKS cluster and node.
10. **EC2**: serves as setup/ssh server so we can push data to our RDS.
11. **EKS**: managed service to operate and maintain Kubernetes on AWS.
12. **ECR**: docker container registry for our images.
13. **NLB**: enables load balancing of network traffic and routes connections to targets.


## Prerequisites

Before you begin, ensure you have the following:

- An AWS account with appropriate permissions.
- Terraform installed on your local machine.


### Notes

- Ensure that you have the necessary AWS credentials configured on your system for Terraform to authenticate with AWS.
- Review the Terraform configuration files to understand the resource provisioning process.
- Refer to the official Terraform documentation for more information on configuring AWS resources using Terraform.

## Deployment Steps

1. Update .aws credentials file
2. On the terminal, go to /01-scripts: `cd .\01-scripts\`
3. Run `./01-prereq-tf-backend.ps1` to create tf backend and amazon ecr repository
4. On the terminal, go to /02-iac: `cd ..\02-iac\`
5. Update terraform.tfvars as needed.
6. Run terraform init/apply.
7. SSH to the bastion/setup server and run `./migrate_data.sh` and `./set-env-1.sh`
8. Confirm RentZone Site by accessing the rentzone setup server public IP (to check if dummy data is imported on RDS)
9. Open Docker Desktop
10. On the terminal, go to  ..\04-docker-projects\rentzone\ or the folder where your Dockerfile resides: `cd ..\04-docker-projects\rentzone\`
11. Run `..\..\01-scripts\02-docker-build-and-push.ps1`. 
12. Check ecr repo if image was pushed.
13. In Secrets manager, check secrets key and value if correctly updated.
14. Check eks cluster if status is active, nodes/node group is created, eks add-ons is installed, and access entries created.
15. Go to ..\05-k8s-manifests-files\: `cd ..\..\05-k8s-manifests-files\`
16. Run `./rentzone-eks-deployment.ps1`
17. Configure Network Load Balancer listeners (i.e. Add TLS (443) listener and delete the TCP (80) HTTP listener)
18. On R53, Add an A Record forwarded to eks_nlb_tg  on the zone name then wait for the "INSYNC" status. 
19. Confirm RentZone Site by accessing the domain name.

## Clean-up Steps

1. Delete Load Balancer and Target Group
2. Delete IAM policy (rentzone-secret-access-policy) and oidc provider.
3. On the terminal, go to /02-iac: `cd ..\..\02-iac\`
4. Run terraform destroy
5. Go to /01-scripts: `cd ..\01-scripts\`
6. Run `./03-post-tf-cleanup.ps1`
7. Go to Docker Desktop and delete local image/s.

## Resources:
- [Terraform Documentation](https://www.terraform.io/docs/index.html)
- [Docker Documentation](https://docs.docker.com/get-started/)
- [Kubernetes Documentation](https://kubernetes.io/docs/home/)


## Additional Notes
- Customize Terraform scripts and configurations as needed for your specific requirements.
- Ensure proper IAM permissions and security measures are in place for managing AWS resources securely.
