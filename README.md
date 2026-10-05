# Secure Azure Enterprise Portfolio

## Project Scope

This project is a personal learning environment for developing Azure cloud engineering skills

### Goal

Build, secure, automate, and monitor a small Azure environment using infrastructure as code.

### Planned Components

- Azure virtual networks and subnets
- Network security groups
- Role-based access control and managed identities
- Terraform infrastructure deployment
- Powershell and Python automation
- GitHub Actions workflows
- Monitoring, alerts, and troubleshooting runbooks

### Boundaries

This project uses synthetic data only.
It will not contain employer, customer, or government information.

### Current Status

The repository is initialized. Infrastructure deployment has not started.

## Network Deployment Evidence

### Spoke Subnetes

The spoke VNet contains separate web, application, and data subnets.

![Spoke subnet configuration](docs/images/01-spoke-subnets.jpg)

### Application-Tier Security Rules

The app NSG allows inbound TCP 8080 from the web subnet at priority 100 and denies other inbound traffic at priority 200.

![Application NSG inbound rules](docs/images/02-app-nsg-inbound-rules.jpg)

### Hub-Spoke Peering

The spoke-to-hub peering shows Connected.

![Connected spoke-to-hub peering](docs/images/03-spoke-to-hub-peering.jpg)

These screenshots demonstrate deployed configuration.
Live workload connectivity testing has not yet been performed.

## Private Endpoint Lab

Configured private access to Azure Blob Storage with public network access disabled. Verifed the endpoint approval, private IP, DNS record, and spoke VNet link.

[View configuration details and screenshots](docs/network-design.md#blobl-storage-private-endpoint)

Live test verified private DNS resolution, HTTPS connectivity, and successful blob upload/download using the VM's managed identity.

## Identity and RBAC Lab

Configured a user-assigned managed identity with Reader access at the lab resource-group scope.

Live tests confirmed that reading resource-group properties successded (HTTP 200), while changing a tag was denied (HTTP 403, AuthrorizationFailed),

[View the access maxtrix, test evidence and cleanup](docs/rbac-matrix.md)

## Key Vault Lab

Configured an Azure RBAC-enabled Key Vault with access restricted to my client IP. Created and read a synthetic secret, then deleted and recovered it using soft delete.

Verified that recovery preserved the secret value.

[View configuration, recovery evidence, and cleanup status](docs/key-vault.md)

### Azure Policy: Required Tag Audit

Created a custom Audit policy to detect resources missing an environment tag.
Tested non-compliance, corrected the tags, and verified all eight evaluated resources became compliant.

[Configuration, test results, and screenshots](docs/azure-policy.md)