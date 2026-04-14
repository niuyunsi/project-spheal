## Why

Currently there is no automated deployment process for the Astro blog to production. Deploying to the AWS Lightsail instance requires manual SSH access and build steps, which is time-consuming and error-prone. A GitHub Actions workflow will enable one-click deployments with proper build validation.

## What Changes

- Add GitHub Actions workflow file at `.github/workflows/deploy-lightsail.yml`
- Configure manual trigger (workflow_dispatch) for controlled deployments
- Automate build process using existing `npm run build` command
- Deploy built assets to AWS Lightsail instance via SSH
- Add necessary AWS/Lightsail connection secrets to repository

## Capabilities

### New Capabilities
- `ci-cd-lightsail-deploy`: Automated deployment pipeline to AWS Lightsail with manual trigger and build validation

### Modified Capabilities
- None (this is a new capability)

## Impact

**Affected Systems:**
- GitHub Actions: New workflow added
- AWS Lightsail: Target deployment instance
- Repository: Requires secrets configuration (SSH key, host, user)

**Dependencies:**
- Existing build command: `npm run build`
- SSH access to Lightsail instance
- No new npm dependencies required
