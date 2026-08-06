https://roadmap.sh/projects/iac-digitalocean

# Terraform AWS EC2 Infrastructure as Code

## Project Overview

This project demonstrates Infrastructure as Code (IaC) using Terraform on AWS.

Terraform provisions:

- EC2 Instance
- Security Group
- SSH Access
- Public IP
- Nginx Web Server

## Technologies Used

- Terraform
- AWS EC2
- Amazon Linux 2023
- Nginx
- SSH
- Git
- GitHub

## Architecture

Terraform
↓
AWS Provider
↓
EC2 Instance
↓
Security Group
↓
Public IP
↓
Nginx

## Files

- provider.tf
- main.tf
- variables.tf
- outputs.tf

## Commands

Initialize

```bash
terraform init
```

Validate

```bash
terraform validate
```

Plan

```bash
terraform plan
```

Apply

```bash
terraform apply
```

Destroy

```bash
terraform destroy
```

## Result

Successfully provisioned an EC2 instance using Terraform and deployed an Nginx web server accessible through the public IP.

## Author

Sravan Reddy
