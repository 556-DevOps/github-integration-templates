# GitHub Actions Workflows for Azure App Service Management

This directory contains GitHub Actions workflows that have been converted from Azure DevOps pipelines. These workflows provide functionality for managing Azure App Services including deployment, configuration management, and container image operations.

## Available Workflows

### Application Management
- **app-service-start.yaml**: Starts an Azure App Service
- **app-service-restart.yaml**: Restarts an Azure App Service
- **app-service-stop.yaml**: Stops an Azure App Service

### Configuration Management
- **app-get-config.yaml**: Gets the configuration settings from an Azure App Service
- **app-set-config.yaml**: Sets configuration settings for an Azure App Service from a JSON file
- **app-delete-config.yaml**: Deletes specific configuration settings from an Azure App Service

### Container Image Management
- **build-and-push-image-to-acrs.yaml**: Builds a Docker image and pushes it to Azure Container Registries (DEV and QA)
- **change-image.yaml**: Updates the container image used by an Azure App Service

### Main Workflow
- **test_dev_pipeline.yaml**: The main workflow that coordinates all other workflows based on user input

## Required Secrets

These workflows require the following secrets to be configured in your GitHub repository:

- `AZURE_CREDENTIALS`: Azure service principal credentials in JSON format
- `DEV_ACR_PASSWORD`: Password for the development Azure Container Registry
- `QA_ACR_PASSWORD`: Password for the QA Azure Container Registry

## Usage

To use these workflows, place them in the `.github/workflows` directory of your repository. You can then run the main workflow `test_dev_pipeline.yaml` through the GitHub Actions UI or via the GitHub API.

### Azure Service Principal

To create the AZURE_CREDENTIALS secret, run the following Azure CLI commands:

```bash
az ad sp create-for-rbac --name "GitHubActionsApp" --role contributor \
                         --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                         --sdk-auth
```

This will output a JSON object that you should store as the `AZURE_CREDENTIALS` secret in your GitHub repository.