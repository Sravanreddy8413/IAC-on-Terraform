https://roadmap.sh/projects/iac-digitalocean

# Terraform IaC on DigitalOcean

An intermediate-level **Infrastructure as Code (IaC)** project using **Terraform** to provision a DigitalOcean Droplet with a public IP address and SSH access.

The project also demonstrates how Terraform and Ansible can work together:

```text
Terraform → Infrastructure
Ansible   → Configuration
```

---

# 📌 Project Overview

In a traditional infrastructure setup, a DevOps engineer manually:

```text
Login to DigitalOcean
        ↓
Create Droplet
        ↓
Select OS
        ↓
Configure CPU/RAM
        ↓
Configure SSH
        ↓
Get Public IP
        ↓
SSH into server
        ↓
Install applications
```

With Terraform:

```text
Terraform Code
      ↓
Terraform Plan
      ↓
Terraform Apply
      ↓
DigitalOcean API
      ↓
Droplet
      ↓
Public IP
      ↓
SSH Access
```

Terraform allows the infrastructure configuration to be stored in Git and recreated consistently.

---

# 🎯 Project Goal

Create a Terraform configuration that:

* Uses the DigitalOcean provider
* Creates a DigitalOcean Droplet
* Uses a Linux image
* Assigns a public IP
* Configures SSH access
* Outputs the Droplet IP address
* Allows SSH access using a private key
* Supports destroying the infrastructure through Terraform

### Stretch Goal

Use **Ansible** after Terraform creates the Droplet to configure the server.

---

# 🛠️ Technologies

| Technology   | Purpose                |
| ------------ | ---------------------- |
| Terraform    | Infrastructure as Code |
| DigitalOcean | Cloud infrastructure   |
| SSH          | Secure server access   |
| Linux        | Operating system       |
| Ansible      | Server configuration   |
| Git          | Version control        |
| GitHub       | Source-code management |

---

# 🏗️ Architecture

```text
                    Developer
                        │
                        ▼
                 Terraform Code
                        │
                 terraform init
                        │
                 terraform plan
                        │
                 terraform apply
                        │
                        ▼
              DigitalOcean Provider
                        │
                        ▼
               ┌─────────────────┐
               │ DigitalOcean    │
               │                 │
               │    Droplet      │
               │                 │
               │ Ubuntu/Linux    │
               │ Public IP       │
               │ SSH             │
               └────────┬────────┘
                        │
                        │ SSH
                        ▼
                   Linux Server
```

With the stretch goal:

```text
Terraform
    │
    ▼
DigitalOcean Droplet
    │
    ▼
Ansible
    │
    ├── Update packages
    ├── Install Nginx
    ├── Configure services
    └── Deploy application
```

---

# 📁 Project Structure

Recommended structure:

```text
terraformlinuxdevops/
│
├── main.tf
├── variables.tf
├── outputs.tf
├── versions.tf
├── terraform.tfvars.example
├── .gitignore
├── README.md
│
└── ansible/
    ├── inventory.ini
    ├── setup.yml
    └── roles/
        ├── base/
        ├── nginx/
        └── app/
```

For the basic Terraform project, the important files are:

```text
main.tf
variables.tf
outputs.tf
versions.tf
```

---

# 🧠 What is Terraform?

Terraform is an Infrastructure as Code tool that allows infrastructure to be defined using configuration files.

Instead of manually creating cloud resources, you describe the desired infrastructure:

```text
Code
 ↓
Plan
 ↓
Apply
 ↓
Infrastructure
```

Terraform can manage resources such as:

* Virtual machines
* Networks
* Firewalls
* Load balancers
* Databases
* DNS
* Kubernetes clusters
* Cloud storage

---

# 🧩 Important Terraform Concepts

## Provider

A provider allows Terraform to communicate with a platform.

For this project:

```text
Terraform
    ↓
DigitalOcean Provider
    ↓
DigitalOcean API
```

---

## Resource

A resource represents infrastructure managed by Terraform.

Example:

```hcl
resource "digitalocean_droplet" "web" {
  ...
}
```

---

## Variable

Variables allow configuration to be changed without modifying the main resource definition.

Example:

```hcl
variable "region" {
  type    = string
  default = "blr1"
}
```

---

## Output

Outputs expose useful information after Terraform creates infrastructure.

Example:

```hcl
output "droplet_ip" {
  value = digitalocean_droplet.web.ipv4_address
}
```

---

## State

Terraform maintains a state file:

```text
terraform.tfstate
```

The state tracks infrastructure managed by Terraform.

**Do not commit `terraform.tfstate` to GitHub**, especially when it can contain sensitive information.

---

# 🔑 DigitalOcean API Token

Terraform needs credentials to communicate with DigitalOcean.

Create a DigitalOcean API token through your DigitalOcean account.

Set it as an environment variable instead of hardcoding it:

```bash
export DIGITALOCEAN_TOKEN="YOUR_TOKEN"
```

Verify:

```bash
echo $DIGITALOCEAN_TOKEN
```

Do not commit the token to Git.

---

# ⚠️ Security Warning

Never put credentials directly inside:

```hcl
main.tf
```

For example, avoid:

```hcl
token = "my-secret-token"
```

Use environment variables or another secure secret-management mechanism.

---

# 📦 Install Terraform

Verify whether Terraform is installed:

```bash
terraform version
```

If Terraform is not installed, install the appropriate version for your operating system using the official Terraform installation instructions.

After installation:

```bash
terraform version
```

Expected output will resemble:

```text
Terraform v1.x.x
```

---

# 📄 Terraform Version Configuration

Create:

```text
versions.tf
```

Example:

```hcl
terraform {
  required_version = ">= 1.5.0"

  required_providers {
    digitalocean = {
      source  = "digitalocean/digitalocean"
      version = "~> 2.0"
    }
  }
}
```

The provider version constraint should be reviewed and updated as your project evolves.

---

# 🌎 Configure Provider

Create:

```text
main.tf
```

Example:

```hcl
terraform {
  required_providers {
    digitalocean = {
      source  = "digitalocean/digitalocean"
    }
  }
}

provider "digitalocean" {
  token = var.digitalocean_token
}
```

---

# 🔐 Terraform Variable

Create:

```text
variables.tf
```

Example:

```hcl
variable "digitalocean_token" {
  description = "DigitalOcean API token"
  type        = string
  sensitive   = true
}

variable "region" {
  description = "DigitalOcean region"
  type        = string
  default     = "blr1"
}

variable "droplet_size" {
  description = "DigitalOcean Droplet size"
  type        = string
  default     = "s-1vcpu-1gb"
}

variable "image" {
  description = "Linux image"
  type        = string
  default     = "ubuntu-24-04-x64"
}

variable "droplet_name" {
  description = "Droplet name"
  type        = string
  default     = "terraform-devops-server"
}
```

Choose a region, image, and Droplet size that are actually available in your DigitalOcean account and current region.

---

# 🔑 Configure SSH Key

Terraform needs an SSH public key to configure access to the Droplet.

Generate a key pair if necessary:

```bash
ssh-keygen -t ed25519
```

Example files:

```text
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
```

Check the public key:

```bash
cat ~/.ssh/id_ed25519.pub
```

---

# 🔐 DigitalOcean SSH Key Resource

Add to `main.tf`:

```hcl
resource "digitalocean_ssh_key" "devops" {
  name       = "terraform-devops-key"
  public_key = file("~/.ssh/id_ed25519.pub")
}
```

This allows Terraform to register the public key with DigitalOcean.

---

# 🖥️ Create the Droplet

Add:

```hcl
resource "digitalocean_droplet" "web" {
  name   = var.droplet_name
  region = var.region
  size   = var.droplet_size
  image  = var.image

  ssh_keys = [
    digitalocean_ssh_key.devops.fingerprint
  ]

  tags = [
    "terraform",
    "devops",
    "web"
  ]
}
```

---

# 📤 Terraform Outputs

Create:

```text
outputs.tf
```

Example:

```hcl
output "droplet_id" {
  description = "DigitalOcean Droplet ID"
  value       = digitalocean_droplet.web.id
}

output "droplet_name" {
  description = "Droplet name"
  value       = digitalocean_droplet.web.name
}

output "droplet_ip" {
  description = "Public IPv4 address"
  value       = digitalocean_droplet.web.ipv4_address
}

output "ssh_command" {
  description = "SSH command"
  value       = "ssh root@${digitalocean_droplet.web.ipv4_address}"
}
```

---

# ⚙️ Terraform Variables File

Create:

```text
terraform.tfvars
```

Example:

```hcl
digitalocean_token = "YOUR_TOKEN"

region       = "blr1"
droplet_size = "s-1vcpu-1gb"
image        = "ubuntu-24-04-x64"

droplet_name = "terraform-devops-server"
```

### Important

Do **not** commit this file if it contains your API token.

Add it to `.gitignore`.

---

# 🚫 Git Ignore

Create:

```text
.gitignore
```

Add:

```gitignore
.terraform/
*.tfstate
*.tfstate.*
crash.log
crash.*.log
terraform.tfvars
*.tfplan
```

Never commit:

```text
terraform.tfstate
terraform.tfvars
```

when they contain secrets or sensitive infrastructure data.

---

# 🚀 Initialize Terraform

Run:

```bash
terraform init
```

Terraform will:

```text
Read configuration
      ↓
Download provider
      ↓
Create .terraform/
      ↓
Prepare working directory
```

---

# 🔍 Format Terraform Code

Run:

```bash
terraform fmt
```

Check:

```bash
terraform fmt -check
```

---

# 🧪 Validate Configuration

Run:

```bash
terraform validate
```

Expected:

```text
Success! The configuration is valid.
```

---

# 📋 Create Execution Plan

Run:

```bash
terraform plan
```

Terraform will show the resources it intends to create.

Example:

```text
Plan: 2 to add, 0 to change, 0 to destroy.
```

The exact number depends on the resources in your configuration.

---

# 🚀 Create Infrastructure

Run:

```bash
terraform apply
```

Terraform will ask for confirmation.

Enter:

```text
yes
```

Terraform then creates:

```text
SSH Key
   ↓
DigitalOcean Droplet
   ↓
Public IP
```

---

# 📤 Display Outputs

Run:

```bash
terraform output
```

Get only the IP:

```bash
terraform output -raw droplet_ip
```

---

# 🔐 SSH Into Droplet

Use the private key corresponding to the public key registered with DigitalOcean.

For example:

```bash
chmod 600 ~/.ssh/id_ed25519
```

Then:

```bash
ssh root@$(terraform output -raw droplet_ip)
```

Or:

```bash
ssh -i ~/.ssh/id_ed25519 root@<DROPLET_IP>
```

---

# 🧪 Verify the Server

After connecting:

```bash
hostname
```

Check OS:

```bash
cat /etc/os-release
```

Check IP:

```bash
ip addr
```

Check uptime:

```bash
uptime
```

Check disk:

```bash
df -h
```

Check memory:

```bash
free -m
```

---

# 🔄 Terraform Workflow

The standard workflow is:

```text
                 Terraform Code
                       │
                       ▼
                terraform init
                       │
                       ▼
               terraform validate
                       │
                       ▼
                 terraform plan
                       │
                       ▼
                terraform apply
                       │
                       ▼
             DigitalOcean Droplet
```

When infrastructure changes:

```text
Modify Code
    ↓
terraform fmt
    ↓
terraform validate
    ↓
terraform plan
    ↓
terraform apply
```

---

# 🔁 Change Infrastructure

For example, change:

```hcl
droplet_size = "s-1vcpu-1gb"
```

to another available Droplet size.

Then:

```bash
terraform plan
```

Terraform determines what needs to change.

Apply:

```bash
terraform apply
```

---

# 🗑️ Destroy Infrastructure

When the project is complete:

```bash
terraform destroy
```

Terraform asks for confirmation.

Enter:

```text
yes
```

This removes the resources managed by the configuration.

Always review the destroy plan carefully before confirming, especially in shared or production environments.

---

# 🧠 Terraform State

Terraform creates:

```text
terraform.tfstate
```

It represents Terraform's understanding of the resources it manages.

Example:

```text
Terraform State
      │
      ├── Droplet ID
      ├── IP Address
      ├── Region
      ├── Size
      └── Resource metadata
```

For a team or production environment, use a supported remote backend and state locking strategy rather than sharing a local state file.

---

# 🔐 Terraform Security

Follow these rules:

```text
❌ Do not hardcode API tokens
❌ Do not commit terraform.tfstate
❌ Do not commit terraform.tfvars containing secrets
❌ Do not commit private SSH keys
```

Instead:

```text
Environment Variables
        +
Secret Management
        +
Remote State
        +
Least Privilege
```

---

# 🏗️ Stretch Goal — Terraform + Ansible

The recommended DevOps workflow is:

```text
Terraform
   │
   │ Creates infrastructure
   ▼
DigitalOcean Droplet
   │
   │ SSH
   ▼
Ansible
   │
   ├── Update packages
   ├── Install Nginx
   ├── Install Docker
   ├── Configure users
   ├── Configure SSH
   └── Deploy application
```

Terraform handles:

```text
Infrastructure
```

Ansible handles:

```text
Configuration
```

This separation makes the automation easier to maintain.

---

# 📁 Ansible Inventory

After Terraform creates the server, create:

```text
ansible/inventory.ini
```

Example:

```ini
[webservers]
web01 ansible_host=<DROPLET_IP> ansible_user=root
```

Test:

```bash
ansible all -i ansible/inventory.ini -m ping
```

Expected:

```text
web01 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

---

# ⚙️ Run Ansible

Example:

```bash
ansible-playbook \
  -i ansible/inventory.ini \
  ansible/setup.yml
```

Terraform creates the machine.

Ansible configures it.

```text
Terraform
   ↓
Droplet
   ↓
Ansible
   ↓
Nginx
   ↓
Application
```

---

# 🌐 Example Production Workflow

A more advanced implementation could look like:

```text
Developer
    │
    ▼
GitHub
    │
    ▼
GitHub Actions
    │
    ├───────────────┐
    ▼               ▼
Terraform         Ansible
    │               │
    ▼               │
DigitalOcean       │
    │               │
    └───────┬───────┘
            ▼
       Configured Server
            │
            ▼
          Nginx
            │
            ▼
       Application
```

---

# 🔄 CI/CD Integration

GitHub Actions can automate Terraform.

Example workflow:

```text
Git Push
   ↓
GitHub Actions
   ↓
Terraform fmt
   ↓
Terraform validate
   ↓
Terraform plan
   ↓
Approval
   ↓
Terraform apply
   ↓
Infrastructure Created
   ↓
Ansible Deployment
```

For production, use protected environments, secure secrets, controlled approvals, and a remote Terraform state/backend.

---

# 🧪 Useful Terraform Commands

Initialize:

```bash
terraform init
```

Format:

```bash
terraform fmt
```

Validate:

```bash
terraform validate
```

Plan:

```bash
terraform plan
```

Apply:

```bash
terraform apply
```

Show outputs:

```bash
terraform output
```

Show state:

```bash
terraform show
```

List resources:

```bash
terraform state list
```

Destroy:

```bash
terraform destroy
```

---

# 🐛 Troubleshooting

## Authentication Error

Check:

```bash
echo $DIGITALOCEAN_TOKEN
```

If empty:

```bash
export DIGITALOCEAN_TOKEN="YOUR_TOKEN"
```

Do not print or expose the token in shared logs.

---

## SSH Connection Failed

Check the Droplet IP:

```bash
terraform output -raw droplet_ip
```

Check the private key:

```bash
ls -l ~/.ssh/id_ed25519
```

Set appropriate permissions:

```bash
chmod 600 ~/.ssh/id_ed25519
```

Test:

```bash
ssh -v root@<DROPLET_IP>
```

---

## Terraform Provider Error

Run:

```bash
terraform init
```

Then:

```bash
terraform validate
```

If provider constraints have changed:

```bash
terraform init -upgrade
```

Review the provider documentation before upgrading production configurations.

---

## Droplet Not Found

Check:

```bash
terraform state list
```

Then:

```bash
terraform show
```

If the actual cloud infrastructure was changed outside Terraform, investigate the state and configuration before attempting destructive operations.

---

# 📊 Validation Checklist

Run:

```bash
terraform fmt -check
terraform validate
terraform plan
terraform apply
terraform output
```

Then:

```bash
ssh root@<DROPLET_IP>
```

Verify:

```bash
hostname
cat /etc/os-release
ip addr
uptime
df -h
free -m
```

Finally test:

```bash
terraform destroy
```

Only use `destroy` when you are intentionally cleaning up the project infrastructure.

---

# 📚 Learning Outcomes

After completing this project, you should understand:

* Infrastructure as Code
* Terraform fundamentals
* Terraform providers
* Terraform resources
* Terraform variables
* Terraform outputs
* Terraform state
* `terraform init`
* `terraform fmt`
* `terraform validate`
* `terraform plan`
* `terraform apply`
* `terraform destroy`
* DigitalOcean Droplets
* SSH key configuration
* Cloud infrastructure provisioning
* Terraform security practices
* Terraform and Ansible integration
* Basic IaC automation

---

# 💼 Real-World DevOps Use Case

Suppose a company needs:

```text
Development Environment
        │
        ├── Web Server
        ├── Application Server
        └── Monitoring Server
```

Instead of creating each server manually, Terraform can define the infrastructure:

```text
Git Repository
      │
      ▼
Terraform
      │
      ├── Droplet 1
      ├── Droplet 2
      └── Droplet 3
```

Then Ansible configures each server:

```text
Terraform
   ↓
Infrastructure
   ↓
Ansible
   ↓
Software Configuration
   ↓
Application
```

---

# 🏢 Production-Level Evolution

This project can be extended into a larger DevOps platform:

```text
                    GitHub
                       │
                       ▼
                GitHub Actions
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
        Terraform              Ansible
             │                   │
             ▼                   │
       Cloud Infrastructure     │
             │                   │
       ┌─────┼─────┐             │
       ▼     ▼     ▼             │
      VPC   VM    LB             │
             │                   │
             └─────────┬─────────┘
                       ▼
                  Application
                       │
                       ▼
              Prometheus/Grafana
```

You can later introduce:

* Terraform modules
* Remote state
* State locking
* Multiple environments
* Variables and workspaces
* DigitalOcean VPC
* Firewalls
* Load balancers
* Managed databases
* Object storage
* DNS
* TLS
* Ansible
* Docker
* Kubernetes
* Prometheus
* Grafana
* GitHub Actions

---

# 🎤 Interview Questions

### 1. What is Infrastructure as Code?

IaC is the practice of defining and managing infrastructure through machine-readable configuration files instead of manually creating infrastructure.

### 2. What is Terraform?

Terraform is an IaC tool that allows infrastructure to be defined, provisioned, and managed using declarative configuration.

### 3. What is a Terraform provider?

A provider is a plugin that allows Terraform to communicate with a cloud provider or other API.

### 4. What is a Terraform resource?

A resource represents an infrastructure object managed by Terraform.

Example:

```hcl
resource "digitalocean_droplet" "web" {
}
```

### 5. What is Terraform state?

Terraform state records information about resources Terraform manages and helps Terraform determine what changes are required.

### 6. What is the difference between `terraform plan` and `terraform apply`?

`terraform plan` previews the changes. `terraform apply` executes the approved changes.

### 7. Why should Terraform state not be committed to Git?

State can contain sensitive information and should generally be stored securely using an appropriate remote backend in collaborative environments.

### 8. What is Terraform drift?

Drift occurs when infrastructure changes outside Terraform and the actual infrastructure no longer matches the configuration/state Terraform expects.

### 9. Why use Terraform and Ansible together?

A common separation is:

```text
Terraform → Provision infrastructure
Ansible   → Configure operating systems and applications
```

### 10. What is idempotency?

Idempotency means applying the same desired configuration repeatedly should not produce unnecessary changes after the desired state has been reached.

---

# 📝 Project Checklist

```text
[ ] Create DigitalOcean account
[ ] Generate API token
[ ] Install Terraform
[ ] Generate SSH key
[ ] Create Terraform project
[ ] Create versions.tf
[ ] Create main.tf
[ ] Create variables.tf
[ ] Create outputs.tf
[ ] Create .gitignore
[ ] Configure DigitalOcean provider
[ ] Configure SSH key
[ ] Configure Droplet
[ ] Run terraform init
[ ] Run terraform fmt
[ ] Run terraform validate
[ ] Run terraform plan
[ ] Run terraform apply
[ ] Get Droplet public IP
[ ] SSH into Droplet
[ ] Verify Linux server
[ ] Modify infrastructure
[ ] Test terraform plan
[ ] Run terraform destroy
[ ] Add Ansible stretch goal
[ ] Push project to GitHub
```

---

# 📤 Upload to GitHub

Initialize:

```bash
git init
```

Add files:

```bash
git add .
```

Commit:

```bash
git commit -m "Add Terraform DigitalOcean IaC project"
```

Set branch:

```bash
git branch -M main
```

Add repository:

```bash
git remote add origin https://github.com/<YOUR_USERNAME>/terraformlinuxdevops.git
```

Push:

```bash
git push -u origin main
```

---

# 📌 Suggested GitHub Description

```text
Terraform Infrastructure as Code project that provisions a DigitalOcean Droplet with SSH access, public networking, Terraform outputs, and optional Ansible server configuration.
```

### Suggested Topics

```text
terraform
digitalocean
iac
infrastructure-as-code
devops
ansible
linux
cloud
automation
ssh
terraform-provider
github-actions
cicd
```

---

# 🎯 Final Result

The basic project should achieve:

```text
                 Terraform
                     │
                     ▼
            DigitalOcean API
                     │
                     ▼
              ┌─────────────┐
              │   Droplet   │
              │             │
              │   Linux     │
              │   Public IP │
              │   SSH       │
              └──────┬──────┘
                     │
                     │ SSH
                     ▼
                DevOps User
```

With the stretch goal:

```text
Terraform
    │
    ▼
DigitalOcean Droplet
    │
    ▼
Ansible
    │
    ├── Base configuration
    ├── Nginx
    ├── SSH
    └── Application
```

The key DevOps principle demonstrated by this project is:

> **Terraform provisions the infrastructure; Ansible configures the infrastructure.**

This project provides practical experience with **Terraform, DigitalOcean, Infrastructure as Code, SSH, cloud provisioning, Terraform state, variables, outputs, and Ansible integration**, forming a solid foundation for more advanced AWS/Azure/GCP infrastructure automation.
