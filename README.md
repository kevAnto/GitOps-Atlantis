# GitOps-Atlantis Setup Guide

This guide helps you set up a local GitOps environment using **Atlantis**, **ngrok**, and **aws-vault** to test Terraform infrastructure changes with GitHub integration.


## ✅ Prerequisites

- GitHub account and personal access token (PAT)
- AWS account access
- Terraform installed
- Ubuntu/Linux shell


## 📁 Project Structure

GitOps-Atlantis/

├── scripts/

│ ├── local-setup.sh

│ └── start-atlantis.sh

├── atlantis

├── atlantis_linux_386.zip

├── atlantis.var

├── atlantis.yaml

├── ngrok

├── ngrok.yml

├── repos.yaml

└── README.md

## Step 1 – Initial Setup

### Make the setup script executable and run it:

chmod +x scripts/local-setup.sh
./scripts/local-setup.sh

This will: Download and unzip Atlantis then Download and extract ngrok
Generate a random secret

🔐 Step 2 – Set Secrets and Vars
Create the atlantis.var file with your GitHub and Atlantis details:

```bash
SECRET=your_generated_secret
TOKEN=your_github_personal_access_token
URL="https://<your_ngrok_url>"
USERNAME=your_github_username
REPO_ALLOWLIST="github.com/<your_user_or_org>/*"
REPO_CONFIG="./repos.yaml" 
```
⚠️ Important: Add atlantis.var to .gitignore to avoid pushing secrets:

echo "atlantis.var" >> .gitignore

🌐 Step 3 – Start ngrok
Make sure ngrok.yml is configured correctly, then start the tunnel:

```bash
ngrok start --config=ngrok.yml my-tunnel
```
This will expose Atlantis via a public URL (used in GitHub webhook settings).

🚀 Step 4 – Start Atlantis Server
Make the script executable and run it:

```bash
chmod +x scripts/start-atlantis.sh
./scripts/start-atlantis.sh
```

Atlantis will run using the values set in atlantis.var.

☁️ Step 5 – AWS Vault Setup (Secure AWS Credentials)
Install aws-vault:
```bash
curl -Lo aws-vault https://github.com/99designs/aws-vault/releases/latest/download/aws-vault-linux-amd64
chmod +x aws-vault
sudo mv aws-vault /usr/local/bin/
Verify: aws-vault --version
```
Add your profile:
```bash
sudo apt install awscli
aws-vault add main-admin --backend=file
```
Start a session: aws-vault exec main-admin

🧪 Step 6 – Trigger Atlantis via GitHub Pull Request
Push Terraform code to your repo.

Create a pull request.

Comment with: atlantis plan
Once validated, comment: atlantis apply