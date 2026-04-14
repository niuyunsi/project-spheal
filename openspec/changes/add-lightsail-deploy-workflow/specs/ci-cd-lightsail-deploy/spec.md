## ADDED Requirements

### Requirement: Manual workflow trigger
The system SHALL support manual triggering of the deployment workflow via GitHub Actions workflow_dispatch event.

#### Scenario: User initiates deployment from GitHub UI
- **WHEN** user navigates to Actions tab and clicks "Run workflow" for deploy-lightsail
- **THEN** workflow execution begins
- **AND** user can select branch to deploy

#### Scenario: Workflow accepts optional branch input
- **WHEN** user triggers workflow without specifying branch
- **THEN** system defaults to `main` branch
- **WHEN** user specifies a branch
- **THEN** system deploys the specified branch

### Requirement: Successful build validation
The system SHALL only proceed with deployment after the Astro build completes successfully.

#### Scenario: Build passes
- **WHEN** `npm run build` completes with exit code 0
- **THEN** deployment proceeds to next step
- **AND** dist/ directory contains built assets

#### Scenario: Build fails
- **WHEN** `npm run build` exits with non-zero code
- **THEN** workflow terminates immediately
- **AND** deployment does not execute
- **AND** workflow status shows as failed

### Requirement: Deployment to Lightsail via SSH
The system SHALL deploy built assets to AWS Lightsail instance using SSH and rsync.

#### Scenario: Successful SSH connection and file transfer
- **WHEN** build succeeds
- **AND** SSH credentials are valid
- **THEN** system connects to Lightsail instance
- **AND** rsync transfers dist/ contents to target directory
- **AND** file permissions are preserved

#### Scenario: SSH connection fails
- **WHEN** SSH credentials are invalid or host unreachable
- **THEN** workflow fails with clear error message
- **AND** no changes are made to remote server

### Requirement: Secure credential management
The system SHALL store all sensitive credentials as GitHub repository secrets.

#### Scenario: Credentials accessed at runtime
- **WHEN** workflow executes
- **THEN** SSH key, host, and user are read from GitHub secrets
- **AND** secrets are never logged to console output

#### Scenario: Missing credentials
- **WHEN** required secrets are not configured
- **THEN** workflow fails before SSH attempt
- **AND** error indicates which secret is missing

### Requirement: Deployment status reporting
The system SHALL provide clear feedback about deployment status.

#### Scenario: Deployment succeeds
- **WHEN** all deployment steps complete successfully
- **THEN** workflow status shows as success
- **AND** summary indicates files deployed
- **AND** deployment timestamp is recorded

#### Scenario: Deployment fails
- **WHEN** any deployment step fails
- **THEN** workflow status shows as failure
- **AND** error logs are available for debugging
- **AND** failure point is clearly identified
