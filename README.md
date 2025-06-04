# n8n-docker-caddy

Get up and running with n8n on the following platforms:

* [DigitalOcean tutorial](https://docs.n8n.io/hosting/server-setups/digital-ocean/)
* [Hetzner Cloud tutorial](https://docs.n8n.io/hosting/server-setups/hetzner/)

If you have questions after trying the tutorials, check out the [forums](https://community.n8n.io/).

## Prerequisites

Self-hosting n8n requires technical knowledge, including:

* Setting up and configuring servers and containers
* Managing application resources and scaling
* Securing servers and applications
* Configuring n8n

n8n recommends self-hosting for expert users. Mistakes can lead to data loss, security issues, and downtime. If you aren't experienced at managing servers, n8n recommends [n8n Cloud](https://n8n.io/cloud/).

## Secure Deployment with GitHub Actions and Bitwarden Secrets Manager

This repository includes a GitHub Actions workflow for secure deployment to a Hetzner server using Bitwarden Secrets Manager. This approach keeps sensitive information like passwords and API keys out of your code repository.

### Setup Instructions

#### 1. Bitwarden Secrets Manager Setup

1. Sign up for Bitwarden Secrets Manager (separate from the password manager)
2. Create a new project for your n8n deployment
3. Add the following secrets to your project:
   - `n8n-domain-name`: Your domain name (e.g., `koiniin.site`)
   - `n8n-subdomain`: Your subdomain (e.g., `n8n`)
   - `n8n-postgres-password`: Password for PostgreSQL
   - `n8n-auth-user`: n8n admin username
   - `n8n-auth-password`: n8n admin password
   - `n8n-ssh-private-key`: Your SSH private key for server access
   - `n8n-ssh-host`: Your Hetzner server's IP address
   - `n8n-ssh-user`: Your SSH username (usually `root` or a dedicated deploy user)

4. Create a Service Account:
   - In your Bitwarden Secrets Manager, go to Service Accounts
   - Create a new service account with read access to your project
   - Generate and save the access token securely

#### 2. GitHub Repository Setup

1. Add the following secret to your GitHub repository:
   - `BW_ACCESS_TOKEN`: Your Bitwarden Secrets Manager service account access token
   
   Note: SSH credentials are now securely stored in Bitwarden Secrets Manager instead of GitHub Secrets

#### 3. Server Preparation

1. Set up a Hetzner server with Ubuntu
2. Install Docker and Docker Compose:
   ```bash
   apt update && apt upgrade -y
   apt install -y docker.io docker-compose
   ```
3. Create a dedicated deployment user (optional but recommended):
   ```bash
   adduser deploy
   usermod -aG docker deploy
   mkdir -p /home/deploy/.ssh
   # Add your SSH public key to /home/deploy/.ssh/authorized_keys
   chown -R deploy:deploy /home/deploy/.ssh
   chmod 700 /home/deploy/.ssh
   chmod 600 /home/deploy/.ssh/authorized_keys
   ```

#### 4. Deployment

Once everything is set up, simply push to your main branch or manually trigger the workflow from the GitHub Actions tab. The workflow will:

1. Fetch secrets from Bitwarden
2. Connect to your Hetzner server via SSH
3. Deploy the application with the secure environment variables
4. Perform a health check to ensure everything is running

### Security Benefits

- No sensitive data in the repository
- Secrets are fetched at deployment time
- Environment files are created directly on the server
- Restricted permissions for sensitive files
- Credentials are never exposed in logs
