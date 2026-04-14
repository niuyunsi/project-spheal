# GitHub Actions Deployment Workflow

This directory contains the workflow for deploying the Astro blog to AWS Lightsail.

## Deploy to AWS Lightsail

The `.github/workflows/deploy-lightsail.yml` workflow enables manual deployment of the blog to an AWS Lightsail instance via GitHub Actions.

### Prerequisites

Before using this workflow, you must configure the following GitHub repository secrets:

| Secret Name | Description | Example |
|------------|-------------|---------|
| `LIGHTSAIL_HOST` | IP address or hostname of your Lightsail instance | `123.45.67.89` or `blog.example.com` |
| `LIGHTSAIL_USER` | SSH username for connecting to the instance | `ubuntu` or `root` |
| `LIGHTSAIL_SSH_KEY` | Private SSH key for authentication | Paste full private key content |

### Setting Up Secrets

1. Go to your repository on GitHub
2. Navigate to **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Add each secret from the table above

### Generating an SSH Key Pair

If you don't have an SSH key for deployment:

```bash
# Generate a new SSH key pair
ssh-keygen -t ed25519 -C "github-actions-deploy" -f ~/.ssh/lightsail_deploy

# Copy the public key to your Lightsail instance
ssh-copy-id -i ~/.ssh/lightsail_deploy.pub ubuntu@your-lightsail-ip

# Copy the private key content for the LIGHTSAIL_SSH_KEY secret
cat ~/.ssh/lightsail_deploy
```

### Configuration

The workflow uses these environment variables (configurable in the workflow file):

| Variable | Default | Description |
|----------|---------|-------------|
| `LIGHTSAIL_DEPLOY_DIR` | `/home/ec2-user/html` | Target directory on the Lightsail instance |
| `NODE_VERSION` | `24` | Node.js version for building |

To change the deployment directory, edit the workflow file:

```yaml
env:
  LIGHTSAIL_DEPLOY_DIR: '/your/custom/path'
```

**Note:** The deployment directory must exist on the Lightsail instance and be writable by the `LIGHTSAIL_USER`. To create it:

```bash
mkdir -p /home/ec2-user/html
sudo chown -R ec2-user:ec2-user /home/ec2-user/html
```

### Usage

1. Go to the **Actions** tab in your repository
2. Select **Deploy to AWS Lightsail** from the workflows list
3. Click **Run workflow**
4. Optionally select a different branch (defaults to `main`)
5. Click **Run workflow** to confirm

The workflow will:
1. Validate that all required secrets are configured
2. Checkout the specified branch
3. Install dependencies and build the site
4. Upload build artifacts
5. Deploy to Lightsail via SSH/rsync
6. Report deployment status

### Troubleshooting

#### Workflow fails with "LIGHTSAIL_* secret is not configured"

- Ensure all three required secrets are added to your repository
- Check that secret names match exactly: `LIGHTSAIL_HOST`, `LIGHTSAIL_USER`, `LIGHTSAIL_SSH_KEY`

#### SSH connection fails

- Verify your Lightsail instance allows SSH connections from GitHub Actions IPs
- Check that the SSH key is correctly formatted (include all lines including `BEGIN`/`END` markers)
- Ensure the public key is added to `~/.ssh/authorized_keys` on the Lightsail instance

#### Permission denied when deploying

- Verify `LIGHTSAIL_USER` has write permissions to the deployment directory
- Check directory ownership: `sudo chown -R ec2-user:ec2-user /home/ec2-user/html`
- Ensure the directory exists: `mkdir -p /home/ec2-user/html`

#### rsync: failed to connect to No route to host

- Verify `LIGHTSAIL_HOST` is correct and reachable
- Check that your Lightsail security group allows SSH (port 22) from external IPs
- Ensure the Lightsail instance is running

#### Files are not updated on the server

- Check that `LIGHTSAIL_DEPLOY_DIR` points to the correct web root
- Verify web server is serving from the deployment directory
- Check for permission issues in the deployment directory

#### Build fails

- Ensure `package.json` is up to date with all dependencies
- Check that the build command works locally: `npm run build`
- Verify Node.js version compatibility

### Security Notes

- Use a dedicated SSH key for deployment (not your personal key)
- Limit the SSH key's permissions on the Lightsail instance
- Rotate deployment keys periodically
- Monitor GitHub Actions logs for suspicious activity
- Never commit private keys to the repository

### Rollback Strategy

If a deployment causes issues:

1. SSH into your Lightsail instance
2. Restore from backup if you have one, or
3. Revert the commit and redeploy:
   ```bash
   git revert <commit-hash>
   git push
   ```
4. Trigger the workflow again with the reverted commit

### Post-Deployment Commands

The current workflow does not run any post-deployment commands. If you need to:

- Clear a cache
- Restart a service
- Run database migrations

Add a new step after the deployment step in the workflow file.
