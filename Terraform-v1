# 🚀 Terraform for Beginners

> **What is Terraform?**
> Terraform is a free, open-source tool that lets you create and manage cloud infrastructure (servers, databases, networks, etc.) by writing simple text files. Instead of clicking through a cloud provider's website, you describe what you want in code — and Terraform builds it for you.

---

## 📖 Table of Contents

- [Before You Start](#before-you-start)
- [Key Concepts (Plain English)](#key-concepts-plain-english)
- [Installation](#installation)
  - [macOS](#-macos)
  - [Linux](#-linux)
  - [Windows](#-windows)
- [Verify Your Installation](#verify-your-installation)
- [Your First Terraform Project](#your-first-terraform-project)
- [Understanding the Files Terraform Creates](#understanding-the-files-terraform-creates)
- [The Basic Terraform Workflow](#the-basic-terraform-workflow)
- [Essential Commands Cheat Sheet](#essential-commands-cheat-sheet)
- [Common Beginner Mistakes](#common-beginner-mistakes)
- [Troubleshooting](#troubleshooting)
- [What to Learn Next](#what-to-learn-next)

---

## Before You Start

You do **not** need to know any programming language to use Terraform. However, you should be comfortable:

- ✅ Opening a terminal (macOS/Linux) or PowerShell (Windows)
- ✅ Typing commands and pressing Enter
- ✅ Understanding what a file path is (e.g., `/Users/yourname/projects/`)

That's it. Let's get started.

---

## Key Concepts (Plain English)

Before installing anything, here are the five words you'll hear constantly with Terraform:

| Word | What it means in plain English |
|---|---|
| **Provider** | The cloud platform you want to use — AWS, Azure, Google Cloud, etc. Terraform talks to them on your behalf. |
| **Resource** | One thing you want to create — a virtual machine, a database, a storage bucket. |
| **Configuration file** | A text file (ending in `.tf`) where you write what you want Terraform to build. |
| **Plan** | Terraform's preview of what it *will* do before actually doing anything. Like a receipt before you pay. |
| **State** | A file Terraform keeps to remember what it has already built. |

---

## Installation

Pick your operating system below. If you're not sure which to follow, check:
- Mac users → [macOS](#-macos)
- Linux users → [Linux](#-linux)
- PC users → [Windows](#-windows)

---

### 🍎 macOS

The easiest way to install Terraform on a Mac is with **Homebrew** — a free package manager for macOS.

#### Do you have Homebrew installed?

Open **Terminal** (press `Cmd + Space`, type `terminal`, press Enter) and run:

```bash
brew --version
```

- If you see a version number → Homebrew is installed, skip to **Step 2**
- If you see `command not found` → follow Step 1 first

#### Step 1 — Install Homebrew (skip if already installed)

Paste this into Terminal and press Enter:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

> ⏳ This takes a minute or two. It may ask for your Mac password — that's normal.

#### Step 2 — Add HashiCorp's package source

```bash
brew tap hashicorp/tap
```

> This tells Homebrew where to find Terraform. You only need to do this once.

#### Step 3 — Install Terraform

```bash
brew install hashicorp/tap/terraform
```

#### Step 4 — Confirm it worked

```bash
terraform --version
```

You should see something like:

```
Terraform v1.9.0
on darwin_arm64
```

🎉 **Done! Terraform is installed.**

#### Updating Terraform later

```bash
brew update
brew upgrade hashicorp/tap/terraform
```

---

### 🐧 Linux

Instructions differ slightly depending on your Linux distribution. Find yours below.

---

#### Ubuntu or Debian

These are the most common Linux distributions for beginners.

**Step 1 — Install required tools**

```bash
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common curl
```

> `sudo` means "run as administrator". You may be asked for your password.

**Step 2 — Add HashiCorp's trusted security key**

```bash
wget -O- https://apt.releases.hashicorp.com/gpg | \
  gpg --dearmor | \
  sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
```

> This verifies that packages you download are genuinely from HashiCorp and haven't been tampered with.

**Step 3 — Add HashiCorp's package repository**

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

**Step 5 — Confirm it worked**

```bash
terraform --version
```

---

#### RHEL, CentOS, or Fedora

**Step 1 — Add HashiCorp's package repository**

```bash
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo \
  https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
```

**Step 2 — Install Terraform**

```bash
# CentOS or RHEL:
sudo yum -y install terraform

# Fedora:
sudo dnf install terraform
```

**Step 3 — Confirm it worked**

```bash
terraform --version
```

---

### 🪟 Windows

There are two easy ways to install Terraform on Windows. **Winget** is built into Windows 11 and requires no setup.

---

#### Method 1: Winget (Windows 11 / updated Windows 10)

Open **PowerShell** (press `Win + X`, choose "Windows PowerShell") and run:

```powershell
winget install HashiCorp.Terraform
```

> If winget asks you to accept terms, type `Y` and press Enter.

Close and reopen PowerShell, then confirm:

```powershell
terraform --version
```

**Update Terraform later:**

```powershell
winget upgrade HashiCorp.Terraform
```

---

#### Method 2: Chocolatey

Chocolatey is a popular package manager for Windows (similar to Homebrew on Mac).

**Step 1 — Install Chocolatey**

Open **PowerShell as Administrator** (right-click the Start menu → "Windows PowerShell (Admin)") and paste:

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force;
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072;
iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

**Step 2 — Install Terraform**

```powershell
choco install terraform
```

**Step 3 — Confirm it worked**

Close and reopen PowerShell, then run:

```powershell
terraform --version
```

---

#### Method 3: Download manually (no package manager required)

If you'd rather not install a package manager:

1. Go to https://developer.hashicorp.com/terraform/downloads
2. Click **Windows** → **AMD64** to download the `.zip` file
3. Open the zip and copy `terraform.exe` to a folder, e.g. `C:\terraform`
4. Add that folder to your PATH:
   - Press `Win + S`, search for **"Environment Variables"**
   - Click **"Edit the system environment variables"**
   - Click **"Environment Variables..."**
   - Under **System Variables**, find **Path**, click **Edit**
   - Click **New** and type `C:\terraform`
   - Click OK on all windows
5. Open a **new** PowerShell window and run `terraform --version`

> ⚠️ You must open a **new** terminal window after changing PATH for it to take effect.

---

## Verify Your Installation

No matter which OS you used, run this command to confirm everything is working:

```bash
terraform --version
```

Expected output:

```
Terraform v1.9.0
on <your operating system>
```

If you see that — you're ready to go. ✅

---

## Your First Terraform Project

Let's build something real. This example creates a simple text file using Terraform — no cloud account needed. It's the perfect way to learn the workflow safely.

### Step 1 — Create a project folder

```bash
# macOS / Linux
mkdir ~/my-first-terraform
cd ~/my-first-terraform
```

```powershell
# Windows
mkdir $HOME\my-first-terraform
cd $HOME\my-first-terraform
```

### Step 2 — Create your configuration file

Create a new file called `main.tf` in that folder. You can use any text editor (VS Code, Notepad, nano, etc.).

Paste in the following:

```hcl
# This tells Terraform which version to use and which providers to download
terraform {
  required_version = ">= 1.0"
  required_providers {
    local = {
      source  = "hashicorp/local"
      version = "~> 2.0"
    }
  }
}

# This creates a file on your computer
resource "local_file" "my_first_file" {
  content  = "Hello! Terraform created this file."
  filename = "${path.module}/hello.txt"
}

# This prints a message after Terraform runs
output "message" {
  value = "Success! File created at: ${local_file.my_first_file.filename}"
}
```

> 💡 Lines starting with `#` are comments — they explain what the code does and are ignored by Terraform.

### Step 3 — Run Terraform

Now run these three commands one at a time:

```bash
terraform init
```

> **What this does:** Downloads the "local" provider (the plugin Terraform needs to create files). You'll see it create a `.terraform` folder.

```bash
terraform plan
```

> **What this does:** Shows you what Terraform *will* do — without actually doing it yet. Look for the line `Plan: 1 to add`. That means it will create 1 resource (your file).

```bash
terraform apply
```

> **What this does:** Actually creates the file. Terraform will ask you to type `yes` to confirm — this is a safety check.

Type `yes` and press Enter.

You should see:

```
Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:

message = "Success! File created at: ./hello.txt"
```

Look in your folder — `hello.txt` should now exist! 🎉

### Step 4 — Clean up

When you want to remove everything Terraform created:

```bash
terraform destroy
```

Type `yes` when asked. Terraform will delete `hello.txt`.

---

## Understanding the Files Terraform Creates

After running `terraform init` and `terraform apply`, you'll notice new files and folders:

```
my-first-terraform/
├── main.tf                  ← Your configuration (you wrote this)
├── hello.txt                ← The resource Terraform created
├── terraform.tfstate        ← Terraform's memory of what it built
├── terraform.tfstate.backup ← Backup of the previous state
└── .terraform/              ← Downloaded providers (don't edit this)
    └── providers/
        └── ...
```

| File / Folder | What it is |
|---|---|
| `main.tf` | Your instructions to Terraform |
| `hello.txt` | The resource that was created |
| `terraform.tfstate` | Terraform's record of everything it manages — **don't delete this** |
| `.terraform/` | Cached provider plugins — safe to delete, Terraform will re-download |

> ⚠️ **Never manually edit `terraform.tfstate`**. If Terraform loses track of what it built, it can accidentally create duplicates or fail to clean up resources.

---

## The Basic Terraform Workflow

Every Terraform project follows the same three steps:

```
Write          Plan           Apply
──────         ──────         ──────
Edit your  →   Preview    →   Create /
.tf files      the changes    update /
                              destroy
```

1. **Write** — Edit your `.tf` configuration files
2. **Plan** — Run `terraform plan` to preview what will change
3. **Apply** — Run `terraform apply` to make the changes real

Repeat this cycle whenever you want to change your infrastructure.

---

## Essential Commands Cheat Sheet

| Command | What it does | When to use it |
|---|---|---|
| `terraform init` | Downloads providers and sets up your project | Once per project, or after adding a new provider |
| `terraform plan` | Previews changes without doing anything | Before every apply |
| `terraform apply` | Creates or updates resources | When you're ready to make changes |
| `terraform destroy` | Deletes all resources | When you want to tear everything down |
| `terraform fmt` | Automatically fixes formatting in your `.tf` files | Before committing to git |
| `terraform validate` | Checks for errors in your config files | When something looks wrong |
| `terraform output` | Shows output values | After apply, to see results |
| `terraform show` | Shows the current state in a readable format | To see what Terraform is managing |

---

## Common Beginner Mistakes

**❌ Forgetting to run `terraform init` first**
Every new project (or after adding a new provider) needs `terraform init`. If you skip it, other commands will fail.

**❌ Deleting the `.tfstate` file**
Terraform uses this file to know what it has built. If you delete it, Terraform thinks nothing exists and may try to create duplicates.

**❌ Running `terraform apply` without `terraform plan`**
Always run `plan` first to see what will change. Especially important when working with real cloud resources — unexpected deletions can be costly.

**❌ Editing cloud resources manually**
If you create a server with Terraform and then rename it manually in the AWS console, Terraform's state file becomes out of sync. Always make changes through Terraform.

**❌ Committing your state file to git**
The `terraform.tfstate` file can contain sensitive data (passwords, access keys). Add it to `.gitignore`:

```bash
# .gitignore
.terraform/
*.tfstate
*.tfstate.backup
.terraform.lock.hcl
```

---

## Troubleshooting

**"command not found: terraform"**

Your terminal can't find Terraform. This usually means the install folder isn't in your PATH.
- macOS/Linux: Run `echo $PATH` and confirm `/usr/local/bin` is listed
- Windows: Confirm `C:\terraform` (or wherever you installed it) is in your System PATH
- Try closing and reopening your terminal window

---

**"Error: Failed to query available provider packages"**

Terraform can't reach the internet to download providers.
- Check your internet connection
- If you're behind a corporate proxy, you may need to configure proxy settings

---

**"This plan will destroy X resources" — but I didn't change anything**

This can happen when your state file is out of sync with reality (e.g. someone changed a resource manually in the cloud console). Run `terraform refresh` to re-sync, or carefully review what changed.

---

**Windows: "execution of scripts is disabled on this system"**

Run this in PowerShell as Administrator:

```powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

---

**"Error: Unsupported argument" or "An argument named X is not expected here"**

There's a typo or incorrect argument in your `.tf` file. Run `terraform validate` for a more detailed error message pointing to the exact line.

---

## What to Learn Next

Once you're comfortable with the basics, here's a suggested learning path:

1. **Variables** — Stop hardcoding values; make your configs reusable
   ```hcl
   variable "filename" {
     default = "hello.txt"
   }
   ```

2. **Outputs** — Expose values after a run (IP addresses, URLs, etc.)

3. **A real provider** — Try the [AWS Free Tier](https://aws.amazon.com/free/) or [Google Cloud Free Tier](https://cloud.google.com/free) with Terraform

4. **Modules** — Reusable, shareable bundles of Terraform configuration

5. **Remote state** — Store your state file in S3 or Terraform Cloud so your team can share it safely

6. **`terraform.tfvars`** — Separate your variable values from your configuration

---

## 📚 Helpful Resources

| Resource | Link |
|---|---|
| Official Docs | https://developer.hashicorp.com/terraform/docs |
| Beginner Tutorials (interactive) | https://developer.hashicorp.com/terraform/tutorials |
| Terraform Registry (find providers) | https://registry.terraform.io |
| VS Code Extension | Search "HashiCorp Terraform" in the VS Code marketplace |
| Community Forum | https://discuss.hashicorp.com/c/terraform-core |

---

> 💬 **Stuck?** The [HashiCorp community forum](https://discuss.hashicorp.com/c/terraform-core) and [Stack Overflow](https://stackoverflow.com/questions/tagged/terraform) are great places to ask questions. Most Terraform errors are well-documented and easy to find with a quick search.
