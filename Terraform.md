# Terraform Setup Guide

> Cross-platform installation and configuration guide for macOS, Linux, and Windows.

---

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [macOS Installation](#macos-installation)
- [Linux Installation](#linux-installation)
- [Windows Installation](#windows-installation)
- [Post-Installation Configuration](#post-installation-configuration)
- [Core Commands](#core-commands)
- [Your First Configuration](#your-first-configuration)
- [Version Management with tfenv](#version-management-with-tfenv)
- [Troubleshooting](#troubleshooting)
- [References](#references)

---

## Overview

Terraform is an open-source Infrastructure as Code (IaC) tool by HashiCorp that lets you define, provision, and manage cloud infrastructure using **HCL (HashiCorp Configuration Language)**.

| Concept | Description |
|---|---|
| Infrastructure as Code | Define infrastructure in versioned, human-readable config files |
| Provider | Plugin that interfaces with cloud platforms (AWS, Azure, GCP, etc.) |
| Resource | A single piece of infrastructure (e.g., VM, bucket, network) |
| State File | Terraform's record of real infrastructure (`terraform.tfstate`) |
| Plan | Preview changes before applying them |
| Apply | Execute planned changes and provision resources |

---

## Prerequisites

Before installing Terraform, ensure you have:

- Administrator / sudo privileges
- Internet access to download packages
- A terminal or command prompt
- At least 200 MB of free disk space

---

## macOS Installation

### Method 1: Homebrew (Recommended)

**Step 1 — Install Homebrew**

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Verify:

```bash
brew --version
```

**Step 2 — Add the HashiCorp Tap**

```bash
brew tap hashicorp/tap
```

**Step 3 — Install Terraform**

```bash
brew install hashicorp/tap/terraform
```

**Step 4 — Verify Installation**

```bash
terraform --version

# Expected output:
# Terraform v1.x.x
# on darwin_amd64
```

**Step 5 — Update Terraform**

```bash
brew update
brew upgrade hashicorp/tap/terraform
```

---

### Method 2: Manual Binary Installation

1. Download the latest macOS binary from https://developer.hashicorp.com/terraform/downloads
2. Unzip the downloaded package and move the binary to your PATH:

```bash
unzip terraform_*.zip
sudo mv terraform /usr/local/bin/
terraform --version
```

> **Note (Apple Silicon):** On M1/M2/M3 Macs, download the `darwin_arm64` build for native performance.

---

## Linux Installation

### Ubuntu / Debian

**Step 1 — Install Dependencies**

```bash
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common curl
```

**Step 2 — Add HashiCorp GPG Key**

```bash
wget -O- https://apt.releases.hashicorp.com/gpg | \
  gpg --dearmor | \
  sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
```

**Step 3 — Add the HashiCorp Repository**

```bash
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
  https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
  sudo tee /etc/apt/sources.list.d/hashicorp.list
```

**Step 4 — Install Terraform**

```bash
sudo apt-get update
sudo apt-get install terraform
```

**Step 5 — Verify**

```bash
terraform --version
```

---

### RHEL / CentOS / Fedora

**Step 1 — Add the HashiCorp Repository**

```bash
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo \
  https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
```

**Step 2 — Install Terraform**

```bash
# RHEL / CentOS
sudo yum -y install terraform

# Fedora
sudo dnf install terraform
```

**Step 3 — Verify**

```bash
terraform --version
```

---

### Manual Binary Installation (All Distros)

```bash
# Set the desired version
TERRAFORM_VERSION="1.9.0"

# Download
wget https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_amd64.zip

# Install unzip if needed
sudo apt-get install -y unzip   # Debian/Ubuntu
# sudo yum install -y unzip    # RHEL/CentOS

# Unzip and install
unzip terraform_${TERRAFORM_VERSION}_linux_amd64.zip
sudo mv terraform /usr/local/bin/

# Verify
terraform --version
```

> **Note (ARM64):** For ARM-based servers (AWS Graviton, Raspberry Pi), download the `linux_arm64` binary instead.

---

## Windows Installation

### Method 1: Chocolatey (Recommended)

**Step 1 — Install Chocolatey**

Open PowerShell as Administrator:

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force;
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072;
iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

**Step 2 — Install Terraform**

```powershell
choco install terraform
```

**Step 3 — Verify**

```powershell
terraform --version
```

**Update Terraform**

```powershell
choco upgrade terraform
```

---

### Method 2: Winget

```powershell
# Install
winget install HashiCorp.Terraform

# Update
winget upgrade HashiCorp.Terraform
```

---

### Method 3: Manual ZIP Installation

1. Visit https://developer.hashicorp.com/terraform/downloads
2. Download the **Windows AMD64** zip file
3. Extract `terraform.exe` to a folder (e.g., `C:\terraform`)
4. Add the folder to your System PATH:

```powershell
# Requires Administrator
[Environment]::SetEnvironmentVariable(
  "PATH",
  $env:PATH + ";C:\terraform",
  [EnvironmentVariableTarget]::Machine
)

# Restart PowerShell, then verify
terraform --version
```

> **Note:** If you receive a script execution error, run `Set-ExecutionPolicy RemoteSigned -Scope CurrentUser` in an Administrator PowerShell session.

---

## Post-Installation Configuration

### Enable Tab Completion

```bash
# Bash / Zsh (macOS & Linux)
terraform -install-autocomplete

# Zsh — also add to ~/.zshrc:
autoload -U +X bashcompinit && bashcompinit

# PowerShell (Windows)
terraform -install-autocomplete
```

---

## Core Commands

| Command | Description |
|---|---|
| `terraform init` | Initialize working directory and download providers |
| `terraform plan` | Preview changes Terraform will make |
| `terraform apply` | Apply changes and provision infrastructure |
| `terraform destroy` | Destroy all managed resources |
| `terraform validate` | Check configuration for syntax errors |
| `terraform fmt` | Auto-format `.tf` files to canonical style |
| `terraform output` | Show values of output variables |
| `terraform state list` | List all resources in the state file |
| `terraform show` | Display human-readable state or plan output |
| `terraform import` | Import existing infrastructure into state |

---

## Your First Configuration

Create a file named `main.tf`:

```hcl
terraform {
  required_version = ">= 1.0"
  required_providers {
    local = {
      source  = "hashicorp/local"
      version = "~> 2.0"
    }
  }
}

resource "local_file" "hello" {
  content  = "Hello from Terraform!"
  filename = "${path.module}/hello.txt"
}

output "file_path" {
  value = local_file.hello.filename
}
```

Run the workflow:

```bash
terraform init     # Download the local provider
terraform plan     # Preview: will create hello.txt
terraform apply    # Create the file (type 'yes' to confirm)
terraform destroy  # Clean up (type 'yes' to confirm)
```

---

## Version Management with tfenv

Use `tfenv` to manage multiple Terraform versions across projects.

```bash
# Install tfenv (macOS / Linux)
git clone --depth=1 https://github.com/tfutils/tfenv.git ~/.tfenv
echo 'export PATH="$HOME/.tfenv/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# Install a specific version
tfenv install 1.9.0
tfenv use 1.9.0

# Install and use the latest
tfenv install latest
tfenv use latest

# List available remote versions
tfenv list-remote
```

Pin a version per project by adding a `.terraform-version` file to your repo root:

```
1.9.0
```

---

## Troubleshooting

| Issue | Solution |
|---|---|
| `command not found: terraform` | Ensure `/usr/local/bin` (Linux/macOS) or your install path (Windows) is in `PATH`. Restart your terminal after changes. |
| `Permission denied` | Run `sudo chmod +x /usr/local/bin/terraform` (Linux/macOS) or open PowerShell as Administrator (Windows). |
| GPG key error (Linux) | Re-download the HashiCorp GPG key and repeat the repository setup steps. |
| SSL / certificate errors | Update system certificates: `sudo apt-get install ca-certificates` (Debian) or `update-ca-trust` (RHEL). |
| Checksum mismatch | Delete the downloaded zip and re-download — this indicates a corrupted file. |

---

## References

- [Official Documentation](https://developer.hashicorp.com/terraform/docs)
- [Terraform Registry](https://registry.terraform.io)
- [HashiCorp Tutorials](https://developer.hashicorp.com/terraform/tutorials)
- [GitHub Releases](https://github.com/hashicorp/terraform/releases)
- [tfenv (Version Manager)](https://github.com/tfutils/tfenv)
- [Chocolatey Package](https://community.chocolatey.org/packages/terraform)

---

> **License:** Terraform v1.6.0+ is licensed under the [Business Source License (BSL) 1.1](https://github.com/hashicorp/terraform/blob/main/LICENSE). Earlier versions remain under the Mozilla Public License 2.0.
