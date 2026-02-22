# Azure DevOps Integration with GitHub

This guide explains how to integrate your GitHub repositories with Azure DevOps for continuous integration and continuous deployment (CI/CD).

## Table of Contents

1. [Overview](#overview)
2. [Prerequisites](#prerequisites)
3. [Setting Up Azure DevOps](#setting-up-azure-devops)
4. [Connecting GitHub to Azure Pipelines](#connecting-github-to-azure-pipelines)
5. [Creating Your First Pipeline](#creating-your-first-pipeline)
6. [Advanced Configuration](#advanced-configuration)
7. [Troubleshooting](#troubleshooting)

## Overview

Azure DevOps provides powerful CI/CD capabilities that integrate seamlessly with GitHub. You can:
- Automatically build and test your code on every push
- Deploy to Azure services automatically
- Monitor build and release pipelines
- Manage work items alongside your code

### Key Benefits

- **Native Integration**: GitHub Actions alternative with Azure-specific features
- **Scalability**: Build agents for different platforms (Windows, Linux, macOS)
- **Cost-Effective**: Free tier includes 1 parallel job and 1,800 minutes/month
- **Advanced Reporting**: Detailed analytics and insights on your pipelines

## Prerequisites

- [Azure DevOps Account](https://dev.azure.com) (free)
- [GitHub Account](https://github.com) with a repository
- Azure subscription (optional, for deploying to Azure services)
- Basic understanding of YAML configuration

## Setting Up Azure DevOps

### Step 1: Create an Azure DevOps Organization

1. Navigate to [dev.azure.com](https://dev.azure.com)
2. Click **Create new organization**
3. Accept the terms and click **Continue**
4. Provide a name for your organization
5. Choose your region and click **Continue**

### Step 2: Create a Project

1. In your Azure DevOps organization, click **Create project**
2. Enter a project name (can match your GitHub repo name)
3. Choose visibility (Private/Public)
4. Click **Create**

## Connecting GitHub to Azure Pipelines

### Step 1: Authorize GitHub

1. In your Azure DevOps project, go to **Pipelines** > **Create Pipeline**
2. Select **GitHub** as your source
3. Click **Authorize Azure Pipelines**
4. Sign in with your GitHub account
5. Grant necessary permissions
6. Select the repository you want to connect

### Step 2: Create a Personal Access Token (PAT)

For more secure authentication:

1. In GitHub, go to **Settings** > **Developer settings** > **Personal access tokens**
2. Click **Generate new token**
3. Give it a name: `azure-devops-integration`
4. Select scopes:
   - `repo` (full control of private repositories)
   - `admin:repo_hook` (write access to hooks)
5. Copy the token and save it securely

### Step 3: Configure in Azure DevOps

1. In Azure DevOps, go to **Project Settings** > **Service connections**
2. Click **Create service connection**
3. Select **GitHub**
4. Paste your GitHub PAT
5. Name it appropriately (e.g., `GitHub-Connection`)

## Creating Your First Pipeline

### Step 1: Create a Pipeline YAML File

Create `.azure-pipelines/azure-pipelines.yml` in your GitHub repository:

```yaml
trigger:
  - main
  - develop

pool:
  vmImage: 'ubuntu-latest'

variables:
  buildConfiguration: 'Release'
  dotnetVersion: '6.x'

stages:
  - stage: Build
    displayName: Build Stage
    jobs:
      - job: BuildJob
        displayName: Build and Test
        steps:
          - task: UseDotNet@2
            inputs:
              version: $(dotnetVersion)
            displayName: 'Install .NET SDK'

          - task: DotNetCoreCLI@2
            inputs:
              command: 'restore'
            displayName: 'Restore NuGet packages'

          - task: DotNetCoreCLI@2
            inputs:
              command: 'build'
              arguments: '--configuration $(buildConfiguration)'
            displayName: 'Build project'

          - task: DotNetCoreCLI@2
            inputs:
              command: 'test'
              arguments: '--configuration $(buildConfiguration) --no-build'
            displayName: 'Run unit tests'

  - stage: Publish
    displayName: Publish Stage
    dependsOn: Build
    condition: succeeded()
    jobs:
      - job: PublishJob
        displayName: Publish Artifacts
        steps:
          - task: DotNetCoreCLI@2
            inputs:
              command: 'publish'
              publishWebProjects: true
              arguments: '--configuration $(buildConfiguration) --output $(Build.ArtifactStagingDirectory)'
            displayName: 'Publish application'

          - task: PublishBuildArtifacts@1
            inputs:
              pathToPublish: '$(Build.ArtifactStagingDirectory)'
              artifactName: 'drop'
            displayName: 'Publish artifacts'
```

### Step 2: Create the Pipeline in Azure DevOps

1. In **Pipelines** > **Create Pipeline**
2. Select **GitHub** and your repository
3. Select **Existing Azure Pipelines YAML file**
4. Choose the path: `.azure-pipelines/azure-pipelines.yml`
5. Click **Continue** and then **Save and run**

### Step 3: Monitor the Build

- Check the build status in Azure DevOps
- View logs for each task
- See test results and coverage reports

## Advanced Configuration

### Setting Up Environments for Deployment

```yaml
stages:
  - stage: Deploy
    displayName: Deploy Stage
    jobs:
      - deployment: DeployToAzure
        displayName: Deploy to Azure App Service
        environment: 'production'
        strategy:
          runOnce:
            deploy:
              steps:
                - task: DownloadBuildArtifacts@0
                  inputs:
                    buildType: 'current'
                    downloadType: 'single'
                    artifactName: 'drop'

                - task: AzureWebApp@1
                  inputs:
                    azureSubscription: 'Azure-Subscription'
                    appType: 'webAppLinux'
                    appName: 'my-app-name'
                    package: '$(Pipeline.Workspace)/drop'
                  displayName: 'Deploy to App Service'
```

### Running Tests with Code Coverage

```yaml
- task: DotNetCoreCLI@2
  inputs:
    command: 'test'
    arguments: '--configuration $(buildConfiguration) --no-build /p:CollectCoverage=true /p:CoverletOutputFormat=cobertura'
    publishTestResults: true
  displayName: 'Run tests with coverage'

- task: PublishCodeCoverageResults@1
  inputs:
    codeCoverageTool: Cobertura
    summaryFileLocation: '$(Agent.TempDirectory)/**/coverage.cobertura.xml'
```

### Scheduled Builds

```yaml
schedules:
  - cron: "0 2 * * *"
    displayName: Daily 2 AM UTC
    branches:
      include:
        - main
    always: true
```

## Troubleshooting

### Issue: "Pipeline not found"
**Solution**: Ensure the `.azure-pipelines/azure-pipelines.yml` file exists in your repository and the path is correct.

### Issue: "Authentication failed"
**Solution**: 
- Verify your GitHub PAT hasn't expired
- Check that the PAT has the correct scopes
- Regenerate the PAT if needed

### Issue: "Build agent offline"
**Solution**: 
- For Microsoft-hosted agents, this is usually temporary
- Try rerunning the pipeline
- Consider setting up self-hosted agents for persistent issues

### Issue: "Permission denied for deployment"
**Solution**:
- Ensure your Azure service connection has the correct permissions
- Verify the service principal has contributor role on the resource
- Check firewall/network rules aren't blocking deployment

## Related Resources

- [Azure Pipelines Documentation](https://docs.microsoft.com/azure/devops/pipelines)
- [GitHub Integration in Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/repos/github)
- [YAML Schema Reference](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema)
- [Build and Release Tasks](https://docs.microsoft.com/azure/devops/pipelines/tasks)