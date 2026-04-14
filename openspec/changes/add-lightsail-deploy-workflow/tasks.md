## 1. GitHub Actions Workflow Setup

- [x] 1.1 Create `.github/workflows/` directory structure
- [x] 1.2 Create `deploy-lightsail.yml` workflow file with workflow_dispatch trigger
- [x] 1.3 Add branch input parameter (default: "main")

## 2. Build Configuration

- [x] 2.1 Configure Node.js setup step in workflow
- [x] 2.2 Add npm install and build steps with proper error handling
- [x] 2.3 Configure build artifact upload for deployment step

## 3. SSH Deployment Implementation

- [x] 3.1 Add SSH key setup step using GitHub secrets
- [x] 3.2 Configure SSH known_hosts for Lightsail instance
- [x] 3.3 Implement rsync deployment command with proper flags
- [x] 3.4 Add target directory configuration

## 4. Status Reporting and Error Handling

- [x] 4.1 Add job summary output for deployment status
- [x] 4.2 Configure error messages for SSH connection failures
- [x] 4.3 Add validation for required GitHub secrets
- [x] 4.4 Implement deployment timestamp reporting

## 5. Documentation and Configuration

- [x] 5.1 Create README for GitHub Actions workflow usage
- [x] 5.2 Document required GitHub secrets (LIGHTSAIL_HOST, LIGHTSAIL_USER, LIGHTSAIL_SSH_KEY)
- [x] 5.3 Document deployment directory path configuration
- [x] 5.4 Add troubleshooting guide for common issues

## 6. Validation

- [x] 6.1 Verify workflow syntax is valid
- [ ] 6.2 Test workflow_dispatch trigger functionality (requires GitHub)
- [ ] 6.3 Validate build succeeds on workflow run (requires GitHub)
- [ ] 6.4 Confirm deployment files reach Lightsail instance (requires GitHub + Lightsail)
