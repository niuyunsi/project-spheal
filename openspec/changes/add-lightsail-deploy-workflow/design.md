## Context

The Astro blog is currently deployed manually to an AWS Lightsail instance. This requires developers to SSH into the instance, pull changes, run builds, and restart services - a process that is both time-consuming and error-prone. The project already has a working build process (`npm run build`) that produces optimized static assets in the `dist/` directory.

GitHub Actions is available as the CI/CD platform, and the repository is hosted on GitHub. AWS Lightsail instances are accessible via SSH using key-based authentication.

## Goals / Non-Goals

**Goals:**
- Enable one-click deployment to AWS Lightsail via GitHub Actions manual trigger
- Automate the build process using existing npm scripts
- Deploy static assets to the Lightsail instance
- Ensure deployment only proceeds after successful build
- Provide clear deployment status feedback

**Non-Goals:**
- Automatic deployment on push (deployments remain manually triggered)
- Database migrations (not applicable for static site)
- Multi-environment deployments (only production target)
- Blue-green deployment strategies

## Decisions

**GitHub Actions over other CI/CD platforms:**
- Rationale: Native integration with GitHub repository, free for public repositories, already widely adopted
- Alternatives considered: GitLab CI, CircleCI, Jenkins

**SSH-based deployment over container deployment:**
- Rationale: Lightsail instance is already configured as a static file server; SSH is the simplest and most direct method
- Alternatives considered: AWS CodeDeploy, Docker containers

**Manual trigger (workflow_dispatch) over automatic deployment:**
- Rationale: Provides control over when deployments occur; allows for validation before deploying
- Alternatives considered: Push-based deployment, scheduled deployment

**rsync over scp for file transfer:**
- Rationale: Efficient incremental transfers, preserves file permissions, handles directory structure well
- Alternatives considered: scp, sftp

## Risks / Trade-offs

**SSH Key exposure in repository secrets**
- Risk: Compromised GitHub account could expose SSH credentials
- Mitigation: Use deploy-specific SSH keys with limited permissions, enable repository secret monitoring

**Build failures during deployment**
- Risk: Deployment fails but leaves site in broken state
- Mitigation: Build on separate runner, only deploy after successful build, keep previous build as backup

**Lightsail instance downtime during deployment**
- Risk: Brief service interruption during file sync
- Mitigation: Use atomic directory swaps or maintain minimal downtime during rsync

**No rollback mechanism**
- Risk: Bad deployment requires manual rollback
- Mitigation: Keep timestamped backups of previous deployments on the Lightsail instance

## Migration Plan

1. Configure GitHub repository secrets:
   - `LIGHTSAIL_HOST`: Lightsail instance IP address or hostname
   - `LIGHTSAIL_USER`: SSH username (typically `ubuntu` or `root`)
   - `LIGHTSAIL_SSH_KEY`: Private SSH key for authentication

2. Create GitHub Actions workflow file at `.github/workflows/deploy-lightsail.yml`

3. Test deployment workflow in a staging environment (if available) or during low-traffic period

4. Document the deployment process for team members

5. Rollback strategy: SSH into instance, restore from backup directory

## Open Questions

- ~~What is the exact deployment directory path on the Lightsail instance?~~ → **Resolved:** `/home/ec2-user/html`
- Should deployments be timestamped/rolled automatically, or is manual rollback acceptable? → Manual rollback is acceptable
- Are there any post-deployment commands needed (e.g., cache clearing, service restarts)? → Not needed for static site
