# GitHub Integration Templates

This repository contains reusable GitHub Actions workflows and custom actions for Azure App Service management operations.

## Repository Structure

```
.github/
├── actions/                    # Custom GitHub Actions
│   ├── azure-login/           # Azure service principal authentication
│   ├── app-service-operation/ # App Service lifecycle operations
│   └── container-registry/    # Container registry operations
└── workflows/                 # Reusable workflows
    ├── app-lifecycle.yml      # Start/stop/restart operations
    ├── app-configuration.yml  # Configuration management
    └── container-operations.yml # Container build/push/deploy
```

## Reusable Workflows

### App Lifecycle Operations
**File**: `.github/workflows/app-lifecycle.yml`

Handles Azure App Service lifecycle operations (start, restart, stop).

**Usage**:
```yaml
jobs:
  lifecycle:
    uses: 556-DevOps/github-integration-templates/.github/workflows/app-lifecycle.yml@main
    with:
      operation: "start"          # start, restart, stop
      app-name: "my-webapp"
      resource-group-name: "my-rg"
      app-type: "webapp"          # webapp, functionapp
      environment: "qa"
    secrets:
      AZURE_CLIENT_ID: ${{ secrets.AZURE_CLIENT_ID }}
      AZURE_CLIENT_SECRET: ${{ secrets.AZURE_CLIENT_SECRET }}
      AZURE_TENANT_ID: ${{ secrets.AZURE_TENANT_ID }}
```

### App Configuration Operations
**File**: `.github/workflows/app-configuration.yml`

Manages Azure App Service configuration settings (get, set, delete).

**Usage**:
```yaml
jobs:
  config:
    uses: 556-DevOps/github-integration-templates/.github/workflows/app-configuration.yml@main
    with:
      operation: "set"            # get, set, delete
      app-name: "my-webapp"
      resource-group-name: "my-rg"
      app-type: "webapp"
      environment: "qa"
      file-path: "config/settings.json"  # Required for 'set' operation
      setting-names: "KEY1 KEY2"         # Required for 'delete' operation
    secrets:
      AZURE_CLIENT_ID: ${{ secrets.AZURE_CLIENT_ID }}
      AZURE_CLIENT_SECRET: ${{ secrets.AZURE_CLIENT_SECRET }}
      AZURE_TENANT_ID: ${{ secrets.AZURE_TENANT_ID }}
```

### Container Operations
**File**: `.github/workflows/container-operations.yml`

Handles container image build, push, and deployment operations.

**Usage**:
```yaml
jobs:
  container:
    uses: 556-DevOps/github-integration-templates/.github/workflows/container-operations.yml@main
    with:
      operation: "build-push"     # build-push, deploy
      image-name: "my-app:latest"
      registry-name: "myregistry"
      app-name: "my-webapp"
      resource-group-name: "my-rg"
      environment: "qa"
    secrets:
      AZURE_CLIENT_ID: ${{ secrets.AZURE_CLIENT_ID }}
      AZURE_CLIENT_SECRET: ${{ secrets.AZURE_CLIENT_SECRET }}
      AZURE_TENANT_ID: ${{ secrets.AZURE_TENANT_ID }}
```

## Custom Actions

### Azure Login
**Path**: `.github/actions/azure-login`

Authenticates with Azure using service principal credentials.

**Usage**:
```yaml
- name: Azure Login
  uses: 556-DevOps/github-integration-templates/.github/actions/azure-login@main
  with:
    azure-client-id: ${{ secrets.AZURE_CLIENT_ID }}
    azure-client-secret: ${{ secrets.AZURE_CLIENT_SECRET }}
    azure-tenant-id: ${{ secrets.AZURE_TENANT_ID }}
```

### App Service Operation
**Path**: `.github/actions/app-service-operation`

Performs operations on Azure App Services (webapps and function apps).

**Usage**:
```yaml
- name: App Service Operation
  uses: 556-DevOps/github-integration-templates/.github/actions/app-service-operation@main
  with:
    operation: "start"          # start, restart, stop
    app-name: "my-webapp"
    resource-group-name: "my-rg"
    app-type: "webapp"          # webapp, functionapp
```

### Container Registry
**Path**: `.github/actions/container-registry`

Handles Azure Container Registry operations for image management.

**Usage**:
```yaml
- name: Container Registry Operation
  uses: 556-DevOps/github-integration-templates/.github/actions/container-registry@main
  with:
    operation: "push"           # build, push, login
    image-name: "my-app:latest"
    registry-name: "myregistry"
```

## Required Secrets

All workflows and actions require these Azure service principal secrets:

| Secret Name | Description |
|-------------|-------------|
| `AZURE_CLIENT_ID` | Service Principal Application ID |
| `AZURE_CLIENT_SECRET` | Service Principal Secret/Password |
| `AZURE_TENANT_ID` | Azure Active Directory Tenant ID |

## Supported Operations

### App Lifecycle
- **start**: Start an Azure App Service
- **restart**: Restart an Azure App Service  
- **stop**: Stop an Azure App Service

### App Configuration
- **get**: Retrieve current application settings
- **set**: Update application settings from a JSON file
- **delete**: Remove specific application settings

### Container Operations
- **build-push**: Build container image and push to registry
- **deploy**: Deploy container image to App Service

## Usage Examples

### Basic App Restart
```yaml
name: Restart Application
on:
  workflow_dispatch:

jobs:
  restart:
    uses: 556-DevOps/github-integration-templates/.github/workflows/app-lifecycle.yml@main
    with:
      operation: "restart"
      app-name: "my-production-app"
      resource-group-name: "production-rg"
      app-type: "webapp"
      environment: "prod"
    secrets:
      AZURE_CLIENT_ID: ${{ secrets.AZURE_CLIENT_ID }}
      AZURE_CLIENT_SECRET: ${{ secrets.AZURE_CLIENT_SECRET }}
      AZURE_TENANT_ID: ${{ secrets.AZURE_TENANT_ID }}
```

### Update Configuration
```yaml
name: Update Configuration
on:
  push:
    paths: ['config/**']

jobs:
  update-config:
    uses: 556-DevOps/github-integration-templates/.github/workflows/app-configuration.yml@main
    with:
      operation: "set"
      app-name: "my-webapp"
      resource-group-name: "qa-rg"
      app-type: "webapp"
      environment: "qa"
      file-path: "config/qa-settings.json"
    secrets:
      AZURE_CLIENT_ID: ${{ secrets.AZURE_CLIENT_ID }}
      AZURE_CLIENT_SECRET: ${{ secrets.AZURE_CLIENT_SECRET }}
      AZURE_TENANT_ID: ${{ secrets.AZURE_TENANT_ID }}
```

## Architecture

This template repository follows GitHub Actions best practices:

- **Reusable Workflows**: Centralized workflow logic that can be called from other repositories
- **Composite Actions**: Modular actions that can be combined in different workflows
- **Input Validation**: Required parameters ensure workflows fail fast with clear error messages
- **Security**: Service principal authentication with minimal required permissions

## Contributing

1. Test changes in a development environment first
2. Ensure all required parameters are documented
3. Add usage examples for new workflows or actions
4. Follow existing naming conventions and structure