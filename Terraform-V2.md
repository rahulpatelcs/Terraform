# 🚀 Terraform for Beginners — Complete Guide

> A step-by-step guide covering everything you need to go from zero to confidently building AWS infrastructure with Terraform.

---

## 📖 Table of Contents

- [1. Setup Terraform](#1-setup-terraform)
  - [macOS](#-macos)
  - [Linux](#-linux)
  - [Windows](#-windows)
- [2. Setup an AWS Account](#2-setup-an-aws-account)
  - [Remove Root Access Keys](#-remove-root-access-keys)
  - [Create an IAM User](#-create-an-iam-user)
  - [Enable MFA](#-enable-mfa)
- [3. Configure Billing Alerts](#3-configure-billing-alerts)
- [4. Terraform Basics and Configuration](#4-terraform-basics-and-configuration)
  - [Structure](#structure)
  - [Workflow](#workflow)
  - [Terraform Language (HCL)](#terraform-language-hcl)
  - [Terraform Registry](#terraform-registry)
  - [Terraform Provider](#terraform-provider)
  - [Terraform Resources](#terraform-resources)
  - [Terraform Variables](#terraform-variables)
  - [Assigning Values to Variables](#assigning-values-to-variables)
  - [Terraform Local Values](#terraform-local-values)
  - [Terraform Output](#terraform-output)
  - [Terraform State](#terraform-state)
  - [Terraform Expressions](#terraform-expressions)
  - [Debugging Terraform](#debugging-terraform)
- [5. Setup Amazon Q with VS Code](#5-setup-amazon-q-with-vs-code)
- [Conclusion](#conclusion)

---

## 1. Setup Terraform

Terraform is a free, open-source tool that lets you define and create cloud infrastructure by writing simple text files. Instead of clicking through the AWS console, you write what you want — and Terraform builds it.

### What you need before starting

- ✅ A terminal (macOS/Linux) or PowerShell (Windows)
- ✅ Admin/sudo access on your computer
- ✅ Internet connection

---

### 🍎 macOS

The recommended way to install Terraform on macOS is via **Homebrew**.

#### Do you have Homebrew?

```bash
brew --version
```

- **Sees a version number** → Skip to Step 2
- **Sees `command not found`** → Do Step 1 first

#### Step 1 — Install Homebrew

Open **Terminal** (`Cmd + Space` → type `terminal` → Enter):

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

> ⏳ Takes 1–2 minutes. Your Mac password may be required — that's normal.

#### Step 2 — Add HashiCorp's package source

```bash
brew tap hashicorp/tap
```

#### Step 3 — Install Terraform

```bash
brew install hashicorp/tap/terraform
```

#### Step 4 — Verify

```bash
terraform --version
# Terraform v1.x.x
# on darwin_arm64
```

#### Updating Terraform later

```bash
brew update && brew upgrade hashicorp/tap/terraform
```

> 💡 **Apple Silicon (M1/M2/M3)?** Homebrew automatically installs the native `arm64` build — no extra steps needed.

---

### 🐧 Linux

#### Ubuntu / Debian

```bash
# Step 1 — Install dependencies
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common curl

# Step 2 — Add HashiCorp's GPG key
wget -O- https://apt.releases.hashicorp.com/gpg | \
  gpg --dearmor | \
  sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null

# Step 3 — Add the repository
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
  https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
  sudo tee /etc/apt/sources.list.d/hashicorp.list

# Step 4 — Install
sudo apt-get update && sudo apt-get install terraform

# Step 5 — Verify
terraform --version
```

#### RHEL / CentOS / Fedora

```bash
# Step 1 — Add the repository
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo \
  https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo

# Step 2 — Install (use dnf instead of yum on Fedora)
sudo yum -y install terraform

# Step 3 — Verify
terraform --version
```

---

### 🪟 Windows

#### Method 1: Winget (Windows 11 / updated Windows 10 — easiest)

Open **PowerShell** and run:

```powershell
winget install HashiCorp.Terraform
```

Close and reopen PowerShell, then verify:

```powershell
terraform --version
```

#### Method 2: Chocolatey

Open **PowerShell as Administrator** (right-click Start → "Windows PowerShell (Admin)"):

```powershell
# Install Chocolatey
Set-ExecutionPolicy Bypass -Scope Process -Force;
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072;
iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

# Install Terraform
choco install terraform

# Verify (open a new PowerShell window first)
terraform --version
```

#### Method 3: Manual download

1. Visit https://developer.hashicorp.com/terraform/downloads
2. Download **Windows AMD64** zip
3. Extract `terraform.exe` to `C:\terraform`
4. Add `C:\terraform` to your System PATH:
   - `Win + S` → search **"Environment Variables"** → Edit system environment variables → Environment Variables → System Variables → `Path` → Edit → New → type `C:\terraform` → OK all windows
5. Open a **new** PowerShell window and run `terraform --version`

> ⚠️ **Execution policy error?** Run `Set-ExecutionPolicy RemoteSigned -Scope CurrentUser` as Administrator.

---

## 2. Setup an AWS Account

A fresh AWS account comes with a **root user** — the all-powerful account owner login. Using the root user day-to-day is dangerous. This section walks through securing your account before touching any infrastructure.

> 🔐 Think of the root user like the master key to your house. You keep it locked in a safe and use a regular key for everyday use.

---

### ❌ Remove Root Access Keys

Root access keys allow programmatic (API) access to your AWS account with unlimited permissions — a serious security risk if leaked.

**Steps to remove root access keys:**

1. Sign into the AWS Console at https://console.aws.amazon.com
2. Click your account name (top right) → **Security credentials**
3. Scroll down to the **Access keys** section
4. If any keys exist, click **Delete** next to each one
5. Confirm deletion

> ✅ **Best practice:** The root user should have **zero access keys**. Use IAM users for all programmatic access.

---

### 👤 Create an IAM User

IAM (Identity and Access Management) lets you create users with only the permissions they need. You'll use this user — not root — for all your Terraform work.

**Steps:**

1. In the AWS Console, search for **IAM** and open it
2. In the left menu, click **Users** → **Create user**
3. Enter a username (e.g., `terraform-user`) → click **Next**
4. Choose **Attach policies directly**
5. For learning purposes, search for and select **AdministratorAccess**

   > ⚠️ `AdministratorAccess` is broad. In a real production environment, follow the principle of least privilege — only grant the specific permissions your user needs (e.g., `AmazonEC2FullAccess`, `AmazonS3FullAccess`).

6. Click **Next** → **Create user**

**Create access keys for Terraform:**

1. Click on your new user → **Security credentials** tab
2. Scroll to **Access keys** → **Create access key**
3. Select **Command Line Interface (CLI)**
4. Check the acknowledgement box → **Next** → **Create access key**
5. **Save both the Access Key ID and Secret Access Key now** — you cannot view the secret key again

**Configure AWS credentials on your computer:**

```bash
# Install the AWS CLI first if you haven't:
# macOS:    brew install awscli
# Linux:    sudo apt-get install awscli
# Windows:  winget install Amazon.AWSCLI

aws configure
```

You'll be prompted for:

```
AWS Access Key ID:     [paste your Access Key ID]
AWS Secret Access Key: [paste your Secret Access Key]
Default region name:   us-east-1
Default output format: json
```

Terraform will automatically use these credentials when connecting to AWS.

**Recommended IAM permissions by use case:**

| Use Case | Suggested Policy |
|---|---|
| Learning / experiments | `AdministratorAccess` |
| EC2 management only | `AmazonEC2FullAccess` |
| S3 management only | `AmazonS3FullAccess` |
| Full infrastructure team | Custom policy with specific services |

---

### 🔒 Enable MFA

MFA (Multi-Factor Authentication) adds a second layer of security. Even if your password is stolen, an attacker cannot log in without your physical device.

**Steps (using an authenticator app like Google Authenticator or Authy):**

1. In IAM → **Users** → click your user → **Security credentials**
2. Under **Multi-factor authentication (MFA)** → **Assign MFA device**
3. Enter a device name → choose **Authenticator app** → **Next**
4. Open your authenticator app, scan the QR code shown on screen
5. Enter **two consecutive codes** from your app → **Add MFA**

> ✅ Also enable MFA on your **root user**: Click your account name → Security credentials → Assign MFA device.

**Enforce MFA for IAM users via policy:**

To require MFA before any API actions, attach this policy to your user or group:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "NotAction": [
        "iam:CreateVirtualMFADevice",
        "iam:EnableMFADevice",
        "iam:GetUser",
        "iam:ListMFADevices",
        "iam:ResyncMFADevice",
        "sts:GetSessionToken"
      ],
      "Resource": "*",
      "Condition": {
        "BoolIfExists": {
          "aws:MultiFactorAuthPresent": "false"
        }
      }
    }
  ]
}
```

---

## 3. Configure Billing Alerts

AWS charges by usage. A misconfigured Terraform resource can rack up unexpected charges. Set up billing alerts **before** you start provisioning anything.

### Step 1 — Enable Billing Alerts

1. Sign in as **root** (billing settings require root access)
2. Click your account name (top right) → **Account**
3. Scroll to **Billing preferences**
4. Enable **Receive Billing Alerts** → Save

### Step 2 — Create a Budget

1. In the AWS Console, search for **Billing and Cost Management**
2. In the left menu → **Budgets** → **Create budget**
3. Choose **Use a template** → **Monthly cost budget**
4. Set your budget amount (e.g., `$10` for learning)
5. Enter your email address for notifications
6. Click **Create budget**

AWS will email you when:
- Your costs reach 85% of your budget
- Your costs reach 100% of your budget
- Your forecasted spend is on track to exceed your budget

### Step 3 — Create a CloudWatch Billing Alarm (more granular)

```
1. Go to CloudWatch → Alarms → Create Alarm
2. Click "Select metric" → Billing → Total Estimated Charge
3. Set threshold: Greater than $5 (adjust as needed)
4. Under "Notification": Create new SNS topic → enter your email
5. Name the alarm (e.g., "Monthly-Billing-Alert")
6. Create alarm
7. Check your email and confirm the SNS subscription
```

> 💡 **Tip:** Set the alarm threshold below your actual limit. If your budget is $10/month, set the alarm at $5 so you get an early warning.

---

## 4. Terraform Basics and Configuration

---

### Structure

A typical Terraform project is organized like this:

```
my-terraform-project/
├── main.tf           # Main configuration — resources go here
├── variables.tf      # Variable declarations
├── outputs.tf        # Output value declarations
├── terraform.tfvars  # Variable values (not committed to git)
├── providers.tf      # Provider configuration
└── README.md         # Documentation
```

You can put everything in one `main.tf` file when starting out. As projects grow, splitting into separate files keeps things manageable. **Terraform reads all `.tf` files in a folder together** — the filename doesn't matter to Terraform, only to you.

---

### Workflow

Every Terraform project follows this cycle:

```
  ┌─────────┐      ┌─────────┐      ┌─────────┐      ┌─────────┐
  │  Write  │  →   │  Init   │  →   │  Plan   │  →   │  Apply  │
  │ .tf     │      │ download│      │ preview │      │ create  │
  │ files   │      │providers│      │ changes │      │resources│
  └─────────┘      └─────────┘      └─────────┘      └─────────┘
```

| Step | Command | What happens |
|---|---|---|
| **Init** | `terraform init` | Downloads providers, sets up the project. Run once per project. |
| **Plan** | `terraform plan` | Shows what Terraform *will* do — no changes made yet. |
| **Apply** | `terraform apply` | Makes the real changes. Asks for confirmation. |
| **Destroy** | `terraform destroy` | Deletes everything Terraform manages. |

> 💡 Always run `plan` before `apply`. Read it carefully — especially lines starting with `-` (deletions) or `~` (modifications).

---

### Terraform Language (HCL)

Terraform uses **HCL (HashiCorp Configuration Language)** — a simple, human-readable language designed specifically for defining infrastructure.

#### Basic syntax

```hcl
# This is a comment

# A block has a type, optional labels, and a body
block_type "label_one" "label_two" {
  argument_name = "argument_value"
  number_argument = 42
  boolean_argument = true
}
```

#### Data types

```hcl
# String
name = "my-server"

# Number
port = 8080

# Boolean
enabled = true

# List (ordered collection)
availability_zones = ["us-east-1a", "us-east-1b", "us-east-1c"]

# Map (key-value pairs)
tags = {
  Environment = "dev"
  Owner       = "alice"
}
```

#### String interpolation

```hcl
# Use ${} to embed values inside strings
resource_name = "server-${var.environment}"
# If var.environment = "dev", result is "server-dev"
```

---

### Terraform Registry

The **Terraform Registry** (https://registry.terraform.io) is the official library of providers and modules — like an app store for Terraform.

**Providers** — Plugins that connect Terraform to a platform (AWS, Azure, GCP, GitHub, Datadog, etc.)

**Modules** — Pre-built, reusable Terraform configurations. Instead of writing 200 lines to create a VPC, you can use a community module in 10 lines.

```hcl
# Using a module from the registry
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "5.0.0"

  name = "my-vpc"
  cidr = "10.0.0.0/16"
}
```

> ✅ Always pin a specific `version` when using modules or providers. This prevents unexpected breaking changes when new versions are released.

---

### Terraform Provider

A **provider** is a plugin that teaches Terraform how to talk to a specific service. You must declare which providers you need and configure them before Terraform can manage resources on those platforms.

#### Declaring and configuring the AWS provider

```hcl
# providers.tf

terraform {
  required_version = ">= 1.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"   # Use any 5.x version
    }
  }
}

provider "aws" {
  region = "us-east-1"

  # Credentials are read automatically from:
  # 1. AWS_ACCESS_KEY_ID / AWS_SECRET_ACCESS_KEY environment variables
  # 2. ~/.aws/credentials file (set by `aws configure`)
  # 3. IAM role (when running on an EC2 instance)
  # Never hardcode credentials in .tf files!

  default_tags {
    tags = {
      ManagedBy   = "Terraform"
      Environment = "dev"
    }
  }
}
```

After adding or changing providers, always run `terraform init` again to download them.

#### Version constraint operators

| Operator | Meaning | Example |
|---|---|---|
| `= 5.0.0` | Exact version only | Only `5.0.0` |
| `>= 5.0` | This version or newer | `5.0`, `5.1`, `6.0` |
| `~> 5.0` | Only rightmost version can increment | `5.0`, `5.1` but NOT `6.0` |
| `~> 5.0.0` | Patch versions only | `5.0.0`, `5.0.9` but NOT `5.1.0` |

---

### Terraform Resources

**Resources** are the core of Terraform — they represent the actual infrastructure components you want to create (servers, databases, networks, DNS records, etc.).

#### Syntax

```hcl
resource "PROVIDER_RESOURCE_TYPE" "LOCAL_NAME" {
  argument = "value"
}
```

- `PROVIDER_RESOURCE_TYPE` — the resource type from the provider docs (e.g., `aws_instance`, `aws_s3_bucket`)
- `LOCAL_NAME` — a name you choose to reference this resource elsewhere in your config

#### Examples

**S3 Bucket**

```hcl
resource "aws_s3_bucket" "my_bucket" {
  bucket = "my-unique-bucket-name-2024"

  tags = {
    Name        = "My Bucket"
    Environment = "dev"
  }
}
```

**EC2 Instance**

```hcl
resource "aws_instance" "web_server" {
  ami           = "ami-0c55b159cbfafe1f0"   # Amazon Linux 2 AMI (us-east-1)
  instance_type = "t2.micro"                # Free tier eligible

  tags = {
    Name = "web-server"
  }
}
```

**Referencing another resource**

Resources can reference each other using `RESOURCE_TYPE.LOCAL_NAME.ATTRIBUTE`:

```hcl
resource "aws_s3_bucket" "my_bucket" {
  bucket = "my-project-bucket"
}

resource "aws_s3_bucket_versioning" "my_bucket_versioning" {
  bucket = aws_s3_bucket.my_bucket.id   # References the bucket created above

  versioning_configuration {
    status = "Enabled"
  }
}
```

Terraform automatically figures out the correct creation order based on these references.

---

### Terraform Variables

Variables make your Terraform configurations reusable and flexible. Instead of hardcoding `"us-east-1"` in ten places, you define it once as a variable.

#### Declaring Input Variables

Variables are declared using a `variable` block, typically in `variables.tf`:

```hcl
variable "variable_name" {
  # arguments go here
}
```

#### Input Variable Arguments

**`default`** — The value used if no other value is provided. Making a variable optional.

```hcl
variable "region" {
  default = "us-east-1"
}
```

---

**`type`** — Enforces what kind of value is allowed. If someone passes the wrong type, Terraform errors immediately.

```hcl
# Primitive types
variable "instance_count" {
  type = number
}

variable "instance_name" {
  type = string
}

variable "enable_monitoring" {
  type = bool
}

# Collection types
variable "allowed_ports" {
  type = list(number)
  # Example value: [80, 443, 8080]
}

variable "environment_tags" {
  type = map(string)
  # Example value: { env = "dev", owner = "alice" }
}

variable "db_config" {
  type = object({
    username = string
    port     = number
    encrypted = bool
  })
}
```

---

**`description`** — Documents what the variable is for. Shown in `terraform plan` output and on Terraform Cloud.

```hcl
variable "instance_type" {
  type        = string
  description = "The EC2 instance type to use. Must be t2.micro for free tier."
  default     = "t2.micro"
}
```

---

**`validation`** — Custom rules that prevent invalid values from being used.

```hcl
variable "environment" {
  type        = string
  description = "Deployment environment"

  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be one of: dev, staging, prod."
  }
}

variable "instance_count" {
  type        = number
  description = "Number of instances to create"

  validation {
    condition     = var.instance_count >= 1 && var.instance_count <= 10
    error_message = "Instance count must be between 1 and 10."
  }
}
```

---

**`sensitive`** — Marks a variable as secret. Terraform will redact its value from all logs and plan output, showing `(sensitive value)` instead.

```hcl
variable "db_password" {
  type        = string
  description = "Database administrator password"
  sensitive   = true
}

variable "api_key" {
  type      = string
  sensitive = true
}
```

> ⚠️ `sensitive = true` hides the value in output only. The value is still stored in plain text in `terraform.tfstate`. Keep your state file secure.

---

#### Full variable example

```hcl
# variables.tf

variable "environment" {
  type        = string
  description = "Deployment environment (dev, staging, prod)"
  default     = "dev"

  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Must be dev, staging, or prod."
  }
}

variable "instance_type" {
  type        = string
  description = "EC2 instance type"
  default     = "t2.micro"
}

variable "db_password" {
  type        = string
  description = "RDS database password"
  sensitive   = true
}
```

---

### Assigning Values to Variables

Declaring a variable is separate from giving it a value. Here are all the ways to provide values, listed from **highest priority** (overrides everything) to **lowest priority**:

#### 1. Command-line option (`-var`)

Passed directly when running a command. Useful for one-off overrides.

```bash
terraform apply -var="environment=prod"
terraform apply -var="instance_type=t3.small" -var="environment=staging"
```

#### 2. Variable definitions file (`.tfvars`)

The most common approach. Create a file ending in `.tfvars` and Terraform automatically loads `terraform.tfvars`. Other filenames must be specified with `-var-file`.

```hcl
# terraform.tfvars (auto-loaded)
environment   = "dev"
instance_type = "t2.micro"
```

```hcl
# prod.tfvars (must specify explicitly)
environment   = "prod"
instance_type = "t3.large"
```

```bash
# Using a specific var file
terraform apply -var-file="prod.tfvars"
```

> ⚠️ **Never commit `terraform.tfvars` to git if it contains secrets.** Add it to `.gitignore`.

```bash
# .gitignore
*.tfvars
*.tfstate
*.tfstate.backup
.terraform/
```

#### 3. Environment variables

Prefix your variable name with `TF_VAR_`. Useful in CI/CD pipelines.

```bash
# bash / zsh
export TF_VAR_environment="prod"
export TF_VAR_db_password="my-secret-password"
terraform apply

# PowerShell (Windows)
$env:TF_VAR_environment = "prod"
$env:TF_VAR_db_password = "my-secret-password"
terraform apply
```

#### 4. Terraform Cloud workspace variables

In Terraform Cloud, you can set variables in the workspace UI under **Variables**. Mark sensitive variables as **Sensitive** — they will be write-only and never shown after saving.

#### 5. Default values

If none of the above are provided, the `default` value in the `variable` block is used. If there is no default and no value is provided, Terraform will prompt you interactively.

#### Priority order (highest wins)

```
-var flag  >  *.tfvars  >  TF_VAR_ env vars  >  Terraform Cloud  >  default
```

---

#### Accessing Input Variables

Use `var.variable_name` anywhere in your configuration:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = var.instance_type   # References the variable

  tags = {
    Environment = var.environment
  }
}
```

---

### Terraform Local Values

**Locals** let you define intermediate values — calculated or repeated expressions — that you can reuse throughout your configuration. Think of them as named constants or helper variables.

#### Declaring a Local Value

```hcl
locals {
  local_name = expression
}
```

#### Accessing a Local Value

Use `local.local_name` (note: `local` not `locals`):

```hcl
resource "aws_instance" "web" {
  tags = local.common_tags
}
```

#### Practical examples

```hcl
# locals.tf (or in main.tf)

locals {
  # Build a name prefix from variables
  name_prefix = "${var.project}-${var.environment}"

  # Common tags applied to all resources
  common_tags = {
    Project     = var.project
    Environment = var.environment
    ManagedBy   = "Terraform"
    Owner       = "platform-team"
  }

  # Compute a value based on environment
  instance_count = var.environment == "prod" ? 3 : 1
}

# Using locals in resources
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = var.instance_type
  count         = local.instance_count

  tags = merge(local.common_tags, {
    Name = "${local.name_prefix}-web-server"
  })
}

resource "aws_s3_bucket" "data" {
  bucket = "${local.name_prefix}-data-bucket"
  tags   = local.common_tags
}
```

> 💡 **When to use locals vs variables:**
> - Use **variables** for values that come from *outside* (user input, environment-specific settings)
> - Use **locals** for values *derived* from other values — calculations, string concatenations, repeated expressions

---

### Terraform Output

**Outputs** display useful values after `terraform apply` — like IP addresses, URLs, or resource IDs. They're also used to pass values between Terraform modules.

#### Declaring outputs

```hcl
# outputs.tf

output "output_name" {
  value       = resource_type.resource_name.attribute
  description = "Human-readable description"
  sensitive   = false  # Set true to hide from logs
}
```

#### Practical examples

```hcl
output "instance_public_ip" {
  value       = aws_instance.web_server.public_ip
  description = "Public IP address of the web server"
}

output "bucket_name" {
  value       = aws_s3_bucket.my_bucket.bucket
  description = "Name of the S3 bucket"
}

output "db_connection_string" {
  value       = "postgresql://${var.db_user}@${aws_db_instance.main.endpoint}/${var.db_name}"
  description = "Database connection string"
  sensitive   = true
}
```

After running `terraform apply`, outputs are shown:

```
Apply complete! Resources: 2 added.

Outputs:

bucket_name = "my-project-dev-data-bucket"
instance_public_ip = "54.123.45.67"
db_connection_string = <sensitive>
```

View outputs at any time:

```bash
terraform output                        # Show all outputs
terraform output instance_public_ip     # Show a specific output
terraform output -json                  # Show all outputs as JSON
```

---

### Terraform State

Terraform keeps a **state file** (`terraform.tfstate`) that maps your configuration to real-world infrastructure. Understanding state is essential to understanding how Terraform works.

#### Desired State vs Current State

| | Desired State | Current State |
|---|---|---|
| **What it is** | What your `.tf` files describe | What actually exists in the cloud right now |
| **Where it lives** | Your `.tf` configuration files | `terraform.tfstate` file |
| **When it's used** | You edit it to make changes | Terraform reads it to know what already exists |

**The reconciliation loop:**

```
Desired State (.tf files)
         ↓
    terraform plan
         ↓
  Difference = Changes needed
         ↓
    terraform apply
         ↓
  Current State (cloud) updated
  State file (tfstate) updated
         ↓
  Desired == Current  ✅
```

#### Example scenario

```
Your main.tf says:    aws_instance with instance_type = "t2.micro"
Your tfstate says:    aws_instance with instance_type = "t2.micro"
Plan result:          No changes ✅

You edit main.tf to:  aws_instance with instance_type = "t3.small"
Plan result:          ~ Update instance (t2.micro → t3.small)
After apply:          tfstate updated to t3.small ✅
```

#### State best practices

```hcl
# Store state remotely so your team can share it
# (never use local state for team projects)
terraform {
  backend "s3" {
    bucket         = "my-terraform-state-bucket"
    key            = "project/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-state-lock"   # Prevents concurrent modifications
    encrypt        = true
  }
}
```

**Key state rules:**
- 🔒 **Never delete** `terraform.tfstate` — Terraform will lose track of your infrastructure
- 🔒 **Never manually edit** the state file
- 🚫 **Never commit** state files to git (they contain sensitive data)
- 👥 **Use remote state** (S3, Terraform Cloud) for any team projects

Useful state commands:

```bash
terraform state list              # List all managed resources
terraform state show aws_instance.web  # Inspect a specific resource
terraform state rm aws_instance.web    # Remove resource from state (does NOT delete it)
terraform refresh                 # Sync state with real infrastructure
```

---

### Terraform Expressions

Expressions are how you compute and transform values in HCL. Here are the most useful ones for beginners.

#### References

```hcl
# Reference a resource attribute
aws_instance.web.public_ip

# Reference a variable
var.environment

# Reference a local value
local.name_prefix

# Reference a module output
module.vpc.vpc_id
```

#### Conditional expression (ternary)

```hcl
# Syntax: condition ? value_if_true : value_if_false

instance_type = var.environment == "prod" ? "t3.large" : "t2.micro"

count = var.create_instance ? 1 : 0
```

#### `for` expressions

```hcl
# Transform a list
variable "names" {
  default = ["alice", "bob", "charlie"]
}

locals {
  upper_names = [for name in var.names : upper(name)]
  # Result: ["ALICE", "BOB", "CHARLIE"]

  # With a filter condition
  long_names = [for name in var.names : name if length(name) > 4]
  # Result: ["alice", "charlie"]
}

# Transform a map
locals {
  tagged_names = {for name in var.names : name => "user-${name}"}
  # Result: { alice = "user-alice", bob = "user-bob", ... }
}
```

#### Built-in functions

```hcl
locals {
  # String functions
  env_upper   = upper(var.environment)           # "DEV"
  name_lower  = lower("My-Bucket")               # "my-bucket"
  name_joined = join("-", ["my", "app", "dev"])  # "my-app-dev"

  # Collection functions
  combined_tags = merge(local.common_tags, { Extra = "value" })
  item_count    = length(var.allowed_ports)

  # Conditional functions
  coalesced = coalesce(var.custom_name, "default-name")  # First non-null value
}
```

#### `count` — create multiple resources

```hcl
resource "aws_instance" "web" {
  count         = 3              # Creates 3 identical instances
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"

  tags = {
    Name = "web-server-${count.index}"  # web-server-0, web-server-1, web-server-2
  }
}

# Reference a specific instance
output "first_ip" {
  value = aws_instance.web[0].public_ip
}
```

#### `for_each` — create resources from a map or set

```hcl
variable "buckets" {
  default = {
    logs    = "us-east-1"
    backups = "us-west-2"
    assets  = "eu-west-1"
  }
}

resource "aws_s3_bucket" "multi" {
  for_each = var.buckets
  bucket   = "my-project-${each.key}"

  tags = {
    Region = each.value
  }
}
```

---

### Debugging Terraform

When something goes wrong, these techniques help you find out why.

#### Enable verbose logging

Set the `TF_LOG` environment variable to control log verbosity:

```bash
# Log levels (from least to most verbose): ERROR, WARN, INFO, DEBUG, TRACE

# macOS / Linux
export TF_LOG=DEBUG
terraform apply

# Save logs to a file
export TF_LOG=DEBUG
export TF_LOG_PATH=./terraform-debug.log
terraform apply

# Turn off logging
unset TF_LOG

# Windows PowerShell
$env:TF_LOG = "DEBUG"
$env:TF_LOG_PATH = "terraform-debug.log"
terraform apply
```

| Log Level | What you see |
|---|---|
| `ERROR` | Only fatal errors |
| `WARN` | Warnings and errors |
| `INFO` | General operation info (default) |
| `DEBUG` | Detailed request/response info |
| `TRACE` | Everything — very verbose |

#### Validate your configuration

```bash
terraform validate
```

Catches syntax errors and invalid references before you even run plan.

#### Format your code

```bash
terraform fmt
```

Fixes indentation and formatting. Run this before every commit.

#### Inspect the plan in detail

```bash
# Save plan to a file
terraform plan -out=tfplan

# Read the saved plan
terraform show tfplan

# Output plan as JSON for scripting
terraform show -json tfplan
```

#### Reading plan output

```
# What the symbols mean:
+ resource will be CREATED
- resource will be DESTROYED
~ resource will be UPDATED in-place
-/+ resource will be DESTROYED and re-created (replacement)
```

#### Check the state

```bash
# List everything Terraform is managing
terraform state list

# Inspect a specific resource in detail
terraform state show aws_instance.web_server
```

#### Common error patterns

**Error: Provider configuration not present**

```
Run terraform init to download the required provider.
```

**Error: Reference to undeclared resource**

```hcl
# You referenced something that doesn't exist in your config
# Check spelling: aws_instance.web vs aws_instance.web_server
```

**Error: Cycle in dependency graph**

```
# Two resources depend on each other — remove the circular reference
```

**Error: Error acquiring the state lock**

```bash
# Another terraform process is running, or a previous run crashed
# Wait a few minutes, then force-unlock if needed:
terraform force-unlock LOCK_ID
```

---

## 5. Setup Amazon Q with VS Code

**Amazon Q** is an AI coding assistant from AWS that understands Terraform, AWS services, and cloud architecture. It can generate Terraform code, explain errors, and suggest best practices — directly inside VS Code.

### Step 1 — Install VS Code

Download from https://code.visualstudio.com if not already installed.

### Step 2 — Install the HashiCorp Terraform Extension

1. Open VS Code
2. Click the **Extensions** icon in the left sidebar (or press `Ctrl+Shift+X` / `Cmd+Shift+X`)
3. Search for **"HashiCorp Terraform"**
4. Click **Install**

This gives you syntax highlighting, auto-complete, and inline documentation for Terraform files.

### Step 3 — Install the Amazon Q Extension

1. In the Extensions panel, search for **"Amazon Q"**
2. Click **Install** on the **Amazon Q** extension by AWS

### Step 4 — Sign in to Amazon Q

1. Click the **Amazon Q** icon in the VS Code sidebar
2. Click **Sign in**
3. Choose one of:
   - **AWS Builder ID** (free — sign up at https://profile.aws.amazon.com) — recommended for individuals
   - **AWS IAM Identity Center** — for organisations with SSO

4. A browser window opens → complete sign-in → return to VS Code

### Step 5 — Using Amazon Q with Terraform

**Inline code suggestions**

As you type in a `.tf` file, Amazon Q suggests completions. Press `Tab` to accept.

```hcl
# Start typing and Amazon Q suggests the full resource block:
resource "aws_s3_bucket" "   # ← Amazon Q suggests arguments here
```

**Chat with Amazon Q**

Open the Amazon Q chat panel (click the icon or press `Ctrl+Shift+A`):

```
You: Create a Terraform resource for an S3 bucket with versioning enabled
     and server-side encryption using KMS.

Amazon Q: Here's a Terraform configuration for an S3 bucket with
          versioning and KMS encryption:

          resource "aws_s3_bucket" "secure_bucket" {
            ...
          }
```

**Explain code**

Highlight any Terraform code → right-click → **Send to Amazon Q** → **Explain**

**Fix errors**

Copy a Terraform error message → paste it into the Amazon Q chat → ask "How do I fix this?"

### Amazon Q Free tier limits

| Feature | Free |
|---|---|
| Code suggestions | Unlimited |
| Chat messages | 50/month |
| Inline suggestions | Unlimited |

> 💡 **Useful prompts to try with Amazon Q:**
> - *"Write a Terraform module for a VPC with public and private subnets"*
> - *"What does `for_each` do in Terraform and when should I use it instead of `count`?"*
> - *"Review this Terraform code for security issues"*
> - *"Explain this error: [paste error]"*

---

## Conclusion

You've covered the full foundation of Terraform. Here's a summary of what you've learned:

| Topic | Key takeaway |
|---|---|
| **Installation** | Use Homebrew (Mac), apt/yum (Linux), or Winget (Windows) |
| **AWS Security** | Never use root for daily work; IAM user + MFA + no root access keys |
| **Billing** | Set a budget alert before provisioning anything |
| **Structure** | Split configs into `main.tf`, `variables.tf`, `outputs.tf` |
| **Workflow** | Always: init → plan → apply. Read the plan before applying. |
| **Variables** | Declare in `variables.tf`, set values in `.tfvars` or env vars |
| **Locals** | Use for derived/repeated values within your configuration |
| **Outputs** | Expose useful values (IPs, URLs) after apply |
| **State** | Terraform's memory — never delete it, never commit it to git |
| **Expressions** | Conditionals, for loops, and functions make configs dynamic |
| **Debugging** | `TF_LOG=DEBUG`, `terraform validate`, `terraform state show` |
| **Amazon Q** | AI assistant that helps write, explain, and debug Terraform |

### Recommended next steps

1. **Provision a real AWS resource** — start with a free-tier S3 bucket or EC2 instance
2. **Learn Terraform modules** — package and reuse configurations
3. **Set up remote state** — store `tfstate` in S3 with DynamoDB locking
4. **Explore the AWS provider docs** — https://registry.terraform.io/providers/hashicorp/aws/latest/docs
5. **Try Terraform Cloud** — free tier available, great for teams and CI/CD

---

## 📚 Resources

| Resource | Link |
|---|---|
| Terraform Documentation | https://developer.hashicorp.com/terraform/docs |
| Interactive Tutorials | https://developer.hashicorp.com/terraform/tutorials |
| AWS Provider Reference | https://registry.terraform.io/providers/hashicorp/aws/latest/docs |
| Terraform Registry | https://registry.terraform.io |
| AWS Free Tier | https://aws.amazon.com/free |
| Amazon Q for VS Code | https://aws.amazon.com/q/developer |
| HashiCorp Community Forum | https://discuss.hashicorp.com/c/terraform-core |

---

> ⚠️ **Security reminder:** Never commit `terraform.tfvars`, `*.tfstate`, or any file containing credentials to git. Use a `.gitignore` and consider tools like `git-secrets` or `trufflehog` to scan for accidental commits.

```gitignore
# .gitignore for Terraform projects
**/.terraform/*
*.tfstate
*.tfstate.*
*.tfvars
*.tfvars.json
override.tf
override.tf.json
*_override.tf
*_override.tf.json
.terraformrc
terraform.rc
```
