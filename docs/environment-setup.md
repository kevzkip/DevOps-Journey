# Establishing an Isolated, CLI-Driven DevOps Workstation

## Architecture & Design Rationale

To master DevOps, Site Reliability Engineering (SRE), and Cloud Engineering, maintaining a reproducible, isolated, and command-line-driven environment is paramount. This documentation logs the foundational architecture implemented to establish a clean separation between host operating system operations and engineering workflows.

### Core Specifications

* **Host Operating System:** Windows 11 (Configured on a dedicated local administrator account for total isolation from legacy software and personal user data).
* **Storage Strategy:** Root workspace isolated on a dedicated secondary partition or directory structure (e.g., `~/DevOps-Journey` or a custom data drive) to optimize system performance and safeguard version-controlled artifacts.
* **Terminal & Shell Engine:** Windows Terminal paired with PowerShell and native WSL 2 (Ubuntu distribution) for authentic Linux kernel virtualization.
* **Package Management:** Command-line installation via `winget` (Windows) and `apt` (Linux).
* **Version Control & Security:** Git version control backed by modern Ed25519 SSH cryptographic authentication.

---

## Workstation Bootstrapping & Toolchain Deployment

### 1. Package Management & Terminal Setup
Core utilities are deployed using declarative command-line flags via `winget` to avoid bloated GUI installations:

```powershell
winget install --id Microsoft.WindowsTerminal -e --accept-source-agreements --accept-package-agreements
winget install --id Git.Git -e --accept-source-agreements --accept-package-agreements
winget install --id Microsoft.VisualStudioCode -e --accept-source-agreements --accept-package-agreements
```

### 2. Workspace Directory Structure
The local repository is initialized within a clean workspace root, organized with subdirectories separating logs, configurations, and scripts:

```powershell
# Example generalized path initialization (adjust drive/folder to your preference)
cd <your-preferred-storage-path>\DevOps-Journey
git init
mkdir docs, configs, scripts
```

---

## Cryptographic Authentication & SSH Configuration

Secure communication with remote version control requires public-key cryptography. Because modern development workflows span both Windows PowerShell and the WSL 2 Linux environment, distinct Ed25519 key pairs are established for each runtime to simulate real-world hybrid infrastructure.

### Windows PowerShell SSH Configuration
```powershell
Set-Service -Name ssh-agent -StartupType Automatic
Start-Service -Name ssh-agent
ssh-keygen -t ed25519 -C "your.email@example.com"
ssh-add $env:USERPROFILE\.ssh\id_ed25519
```

### WSL 2 Ubuntu Linux SSH Configuration
Inside the Ubuntu WSL environment, a native Linux SSH key pair mirrors production Linux server behavior:
```bash
ssh-keygen -t ed25519 -C "your.email@example.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

To seamlessly export the public Linux key to the Windows clipboard for GitHub registration without text corruption:
```bash
cat ~/.ssh/id_ed25519.pub | clip.exe
```

---

## Guide: Creating and Linking Your Remote GitHub Repository

Since this repository is managed locally from scratch, follow this precise procedure to establish your remote cloud backup and portfolio mirror on GitHub.

### Phase 1: Creating the Repository on GitHub
1. Open your web browser and navigate to [github.com](https://github.com). Log into your account.
2. In the top-right corner of the interface, click the **`+`** icon and select **New repository**.
3. Configure the repository parameters:
   * **Repository name:** `DevOps-Journey`
   * **Description:** *A step-by-step engineering log and artifact repository documenting my journey mastering DevOps, SRE, and Cloud Engineering.*
   * **Visibility:** Select **Public** (ideal for portfolio sharing and blog rendering via Hashnode/DEV.to) or **Private**.
   * **Important:** **Do NOT** check the boxes for "Add a README file", "Add .gitignore", or "Choose a license". Since your local repository is already initialized with local history and folder structures, adding remote boilerplate files will create conflicting histories.
4. Click **Create repository**.

### Phase 2: Linking Local Repository to Remote GitHub
Once created, GitHub will display your repository URLs. Copy your repository's SSH URL (formatted as `git@github.com:<your-username>/DevOps-Journey.git`).

Open your terminal inside your repository root (`DevOps-Journey`) and run the following commands to link your local history to the remote origin:

```bash
# Link remote origin URL
git remote add origin git@github.com:<your-username>/DevOps-Journey.git

# Ensure branch is designated as main
git branch -M main

# Stage, commit, and push your documentation logs
git add .
git commit -m "docs: initialize generalized workstation setup architecture"
git push -u origin main
```

### Verification
Verify your remote connection status and sync state:
```bash
git remote -v
git status
```
The terminal should confirm that your local branch is successfully tracked and up to date with `origin/main`.