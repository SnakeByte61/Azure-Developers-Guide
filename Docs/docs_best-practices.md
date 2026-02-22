# Best Practices for Azure Developers Using GitHub

This guide provides recommendations and best practices for Azure developers working with GitHub repositories and Azure services.

## Table of Contents

1. [Repository Management](#repository-management)
2. [Security Best Practices](#security-best-practices)
3. [Development Workflow](#development-workflow)
4. [Code Quality](#code-quality)
5. [Deployment Strategy](#deployment-strategy)
6. [Monitoring and Logging](#monitoring-and-logging)
7. [Cost Optimization](#cost-optimization)

## Repository Management

### Repository Structure

Organize your repository for clarity and maintainability:

```
project-root/
├── .github/
│   ├── workflows/          # GitHub Actions workflows
│   ├── ISSUE_TEMPLATE/     # Issue templates
│   └── PULL_REQUEST_TEMPLATE.md
├── .azure-pipelines/       # Azure Pipelines configuration
├── src/                    # Source code
│   ├── api/
│   ├── web/
│   └── services/
├── tests/                  # Test code
├── docs/                   # Documentation
├── infra/                  # Infrastructure as Code
│   ├── terraform/
│   ├── bicep/
│   └── scripts/
├── .gitignore
├── README.md
├── CONTRIBUTING.md
└── LICENSE
```

### Branch Strategy

Use a consistent branching strategy:

**Main Branches:**
- `main`: Production-ready code
- `develop`: Integration branch for features

**Temporary Branches:**
- `feature/feature-name`: New features
- `bugfix/bug-name`: Bug fixes
- `hotfix/issue-name`: Urgent production fixes

**Naming Convention:**
```
{type}/{issue-number}-{short-description}
Example: feature/123-add-user-authentication
```

### README Best Practices

Your README should include:

```markdown
# Project Name

Brief description (1-2 sentences)

## Features

- Feature 1
- Feature 2
- Feature 3

## Quick Start

### Prerequisites
- List requirements
- Version numbers

### Installation
Step-by-step setup instructions

### Configuration
- Azure service setup
- Environment variables
- Local development setup

## Usage
Examples and common use cases

## Architecture
High-level system design (can link to docs/)

## Contributing
Link to CONTRIBUTING.md

## License
License information
```

## Security Best Practices

### 1. Secrets Management

**❌ DON'T:**
```
api_key=sk_live_1234567890
connection_string=Server=myserver;Password=MyPassword123
```

**✅ DO:**
- Use Azure Key Vault for production secrets
- Use GitHub Secrets for CI/CD workflows
- Use `.env.local` for local development (in .gitignore)
- Rotate secrets regularly

### 2. GitHub Secrets Configuration

For Azure Pipelines:

```yaml
variables:
  - group: 'Production-Secrets'  # Link to Azure DevOps variable groups
```

For GitHub Actions:

```yaml
env:
  AZURE_SUBSCRIPTION_ID: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
```

**Setting Up Secrets:**
1. Go to **Settings** > **Secrets and variables** > **Actions**
2. Click **New repository secret**
3. Add secrets (e.g., `AZURE_SUBSCRIPTION_ID`, `AZURE_CLIENT_ID`)

### 3. Dependency Management

- Keep dependencies updated
- Use `dependabot` for automated updates:

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "nuget"
    directory: "/"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 5
```

### 4. Code Security Scanning

Enable in GitHub:
1. Go to **Settings** > **Security**
2. Enable **Dependabot alerts**
3. Enable **Dependabot security updates**
4. Enable **Secret scanning**
5. Enable **Code scanning** with CodeQL

### 5. Access Control

- Use branch protection rules:
  - Require pull request reviews
  - Dismiss stale PR approvals
  - Require status checks to pass
  - Include administrators in restrictions

### 6. Audit and Monitoring

- Enable **Audit log** monitoring
- Review collaborator access regularly
- Use organization-level security policies
- Enable 2FA for all team members

## Development Workflow

### Pull Request Process

1. **Create a feature branch** from `develop`
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feature/123-description
   ```

2. **Write code with tests**
   - Add unit tests for new features
   - Maintain >80% code coverage
   - Follow code style guidelines

3. **Commit with clear messages**
   ```
   feat: Add user authentication
   
   - Implement JWT token validation
   - Add login endpoint
   - Add tests for authentication
   
   Fixes #123
   ```

4. **Push and create PR**
   ```bash
   git push origin feature/123-description
   ```

5. **PR Template** (`.github/PULL_REQUEST_TEMPLATE.md`):

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change

## Related Issues
Fixes #(issue number)

## Testing
Describe testing performed

## Checklist
- [ ] Code follows style guidelines
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] No breaking changes
```

6. **Code Review**
   - Address all feedback
   - Request re-review after changes
   - Merge after approval and checks pass

### Commit Message Standards

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes
- `refactor`: Code refactoring
- `perf`: Performance improvements
- `test`: Test additions/modifications
- `ci`: CI/CD changes

**Example:**
```
feat(auth): implement JWT token refresh

- Add refresh token endpoint
- Implement token rotation strategy
- Add security headers

Fixes #456
```

## Code Quality

### Linting and Formatting

**.editorconfig:**
```ini
root = true

[*.cs]
indent_style = space
indent_size = 4
trim_trailing_whitespace = true
insert_final_newline = true
```

**Recommended Tools:**
- C#: StyleCop, Roslyn analyzers
- JavaScript: ESLint, Prettier
- Python: Black, Flake8
- YAML: yamllint

### Testing Strategy

1. **Unit Tests**: Test individual functions
2. **Integration Tests**: Test service interactions
3. **End-to-End Tests**: Test full user workflows

**Minimum Coverage by Environment:**
- `develop`: 70% coverage
- `main`: 85% coverage

Example test structure:
```csharp
[TestClass]
public class UserServiceTests
{
    [TestMethod]
    [ExpectedException(typeof(ValidationException))]
    public void CreateUser_WithInvalidEmail_ThrowsException()
    {
        // Arrange
        var userService = new UserService();
        
        // Act
        userService.CreateUser("invalid-email");
        
        // Assert is implicit (exception expected)
    }
}
```

### Documentation

- Keep docs in the repository
- Use Markdown format
- Include:
  - Architecture diagrams
  - API documentation
  - Deployment guides
  - Troubleshooting sections

## Deployment Strategy

### Environment Strategy

**Environments:**
1. **Local**: Developer machine
2. **Dev**: Automated deployments from develop branch
3. **Staging**: Pre-production environment
4. **Production**: Manual approval required

**Configuration per Environment:**

```
src/
├── appsettings.json           # Base configuration
├── appsettings.Development.json
├── appsettings.Staging.json
└── appsettings.Production.json
```

```csharp
var environment = Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT");
var configuration = new ConfigurationBuilder()
    .AddJsonFile("appsettings.json")
    .AddJsonFile($"appsettings.{environment}.json")
    .AddEnvironmentVariables()
    .Build();
```

### Deployment Checklist

- [ ] All tests passing
- [ ] Code reviewed and approved
- [ ] Security scan passed
- [ ] Database migrations tested
- [ ] Performance impact assessed
- [ ] Documentation updated
- [ ] Release notes prepared
- [ ] Rollback plan documented

### Blue-Green Deployment

For zero-downtime deployments:

```yaml
stages:
  - stage: DeployBlue
    displayName: Deploy to Blue Environment
    
  - stage: SwapSlots
    displayName: Swap to Production
    condition: succeeded()
    jobs:
      - deployment: SwapSlots
        environment: 'production'
        strategy:
          runOnce:
            deploy:
              steps:
                - task: AzureAppServiceManage@0
                  inputs:
                    action: 'Swap Slots'
                    resourceGroupName: 'rg-name'
                    appServiceName: 'app-name'
```

## Monitoring and Logging

### Azure Application Insights

Log all important events:

```csharp
public class UserController : ControllerBase
{
    private readonly ILogger<UserController> _logger;
    private readonly IApplicationInsights _telemetry;

    public UserController(ILogger<UserController> logger)
    {
        _logger = logger;
    }

    [HttpPost("users")]
    public async Task<IActionResult> CreateUser([FromBody] CreateUserRequest request)
    {
        try
        {
            _logger.LogInformation("Creating user: {Email}", request.Email);
            
            var user = await _userService.CreateAsync(request);
            
            _logger.LogInformation("User created successfully: {UserId}", user.Id);
            return Ok(user);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Failed to create user: {Email}", request.Email);
            throw;
        }
    }
}
```

### Structured Logging

```json
{
  "timestamp": "2024-02-22T10:30:00Z",
  "level": "ERROR",
  "message": "Failed to create user",
  "userId": "123",
  "email": "user@example.com",
  "errorCode": "VALIDATION_ERROR",
  "exception": "..."
}
```

### Health Checks

```csharp
services.AddHealthChecks()
    .AddAzureServiceBusHealthCheck(connectionString)
    .AddAzureTableStorageHealthCheck(storageConnection)
    .AddApplicationInsightsPublisher();
```

## Cost Optimization

### Recommendations

1. **Right-size resources**
   - Use Azure Advisor for sizing recommendations
   - Monitor actual vs. provisioned capacity
   - Use auto-scaling for variable workloads

2. **Use reserved instances**
   - Commit to 1-year or 3-year terms
   - Can save 25-40% on compute costs

3. **Optimize storage**
   - Archive old data to Blob Storage Archive tier
   - Use lifecycle policies for automatic tiering
   - Clean up unused resources regularly

4. **Monitor spending**
   - Set Azure budgets and alerts
   - Use Cost Management + Billing
   - Review spending weekly

5. **Leverage free tier**
   - Azure free account
   - GitHub free tier (2,000 free Actions minutes/month)
   - Always free services (Azure DevOps, App Service tier)

### Cost Monitoring in Pipeline

```yaml
- task: AzureCLI@2
  inputs:
    scriptType: 'bash'
    scriptLocation: 'inlineScript'
    inlineScript: |
      az costmanagement usage --start-date $(date -d '1 day ago' +%Y-%m-%d) --end-date $(date +%Y-%m-%d)
  displayName: 'Monitor Azure costs'
```

## Quick Reference

| Practice | Recommendation |
|----------|----------------|
| **Branch Protection** | Require reviews + passing checks |
| **Testing** | Min 80% coverage before merge |
| **Code Review** | At least 2 approvals for main |
| **Secrets** | Never commit, use Key Vault |
| **Logging** | Structured logs with context |
| **Monitoring** | Application Insights enabled |
| **Deployment** | Automated from main branch |
| **Documentation** | Keep in repo, always updated |

## Additional Resources

- [Azure SDK for .NET Best Practices](https://azure.microsoft.com/en-us/blog/inside-the-microsoft-azure-sdk-for-net/)
- [GitHub Security Best Practices](https://docs.github.com/en/code-security)
- [Microsoft Secure Development Lifecycle](https://www.microsoft.com/en-us/securityengineering/sdl/)
- [The Twelve-Factor App](https://12factor.net/)