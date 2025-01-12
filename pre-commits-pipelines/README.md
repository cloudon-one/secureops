# Infrastructure Checks Pipeline

This repository implements a comprehensive infrastructure validation pipeline using GitHub Actions. The pipeline performs various security, cost, and quality checks on infrastructure code, particularly focusing on Terraform configurations.

## Pipeline Overview

The pipeline runs automatically on:
- Pull requests that modify infrastructure files (`.tf`, `.tfvars`, `.hcl`, `Dockerfile`)
- Push events to main/master branch with changes to infrastructure files
- Changes to GitHub workflow files or pre-commit configuration

## Security Scanning

### GitGuardian Security Scan
- Performs deep security scanning of the entire git history
- Detects secrets, credentials, and sensitive information
- Requires `GITGUARDIAN_API_KEY` secret to be configured

### TFSec Analysis
- Scans Terraform code for security issues and best practice violations
- Generates JSON output with detailed findings
- Runs in soft-fail mode to avoid blocking pipelines
- Includes statistics about the scan results

## Cost Management

### Infracost Integration
The pipeline includes comprehensive cost management through Infracost:

#### Pull Request Checks
- Generates cost estimates for infrastructure changes
- Compares costs between base and PR branches
- Posts detailed cost difference comments on PRs
- Requires `INFRACOST_API_KEY` secret

#### Main Branch Updates
- Tracks cost changes on the main/master branch
- Updates Infracost Cloud with latest cost data
- Continues even if policy checks fail

#### PR Status Tracking
- Updates PR status in Infracost when PRs are merged or closed
- Maintains accurate cost history

## Infrastructure Validation

### Docker-based Validation Suite
The pipeline runs a comprehensive suite of checks in a Docker container:

1. **Pre-commit Hooks**
   - Runs all configured pre-commit checks
   - Ensures code quality and formatting standards

2. **TFLint**
   - Lints Terraform files in all directories
   - Validates against Terraform best practices

3. **Terraform Operations**
   - Runs `terraform init` with backend disabled
   - Validates Terraform configurations
   - Checks for required_providers blocks in all Terraform files

## Required Secrets

The following secrets must be configured in your GitHub repository:
- `GITGUARDIAN_API_KEY`: For security scanning
- `INFRACOST_API_KEY`: For cost estimation and tracking

## Pipeline Features

- **Concurrency Control**: Automatically cancels redundant runs
- **Caching**: Uses GitHub Actions cache for Docker builds
- **Comprehensive Validation**: Checks both security and functionality
- **Cost Awareness**: Maintains visibility of infrastructure costs
- **Pull Request Integration**: Provides feedback directly on PRs

## Prerequisites

- Terraform files must include `required_providers` blocks
- Docker must be available in the GitHub Actions environment
- Appropriate secrets must be configured
- Pre-commit configuration must be present in `.pre-commit-config.yaml`

## Error Handling

- TFSec runs in soft-fail mode to provide warnings without blocking
- Main branch Infracost updates continue even with policy failures
- Infrastructure validation fails if any required_providers blocks are missing
- Clear error messages are provided for failed checks

## Usage

The pipeline runs automatically based on the configured triggers. No manual intervention is required unless errors are detected.

1. Make your infrastructure changes
2. Create a pull request
3. Review the automated checks:
   - Security scan results
   - Cost impact analysis
   - Infrastructure validation outcomes
4. Address any issues identified by the pipeline
5. Merge when all checks pass

## Contributing

When contributing to this repository, ensure your changes:
- Include appropriate Terraform provider configurations
- Follow security best practices
- Consider cost implications
- Pass all pipeline checks
