# GitHub Workflows Repository

Centralized repository for reusable GitHub Actions workflows and composite actions.

## Structure

```
.github/
├── actions/           # Reusable composite actions
│   ├── docker-build/  # Build and push Docker images
│   ├── podman-deploy/ # Deploy containers with Podman
│   └── health-check/  # Health verification
└── workflows/         # Reusable workflows
    └── deploy-n8n.yml # Complete n8n deployment pipeline
```

## Available Workflows

### Core Workflows (Reusable Components)

#### ci-generic.yml
Generic CI workflow for any containerized application.
- Build Docker images
- Run tests
- Security scanning with Trivy
- Push to GitHub Container Registry
- Quality gates

#### cd-deploy.yml
Generic CD workflow for application deployment.
- Pre-deployment checks
- Database backups (production)
- Podman/Docker deployment
- Health checks
- Automatic rollback on failure
- Post-deployment notifications

### Application-Specific Workflows

#### deploy-n8n.yml
Complete CI/CD pipeline for n8n deployment with PostgreSQL.

#### deploy-api.yml
Generic API deployment pipeline using the core CI/CD workflows.

#### Features
- 🔨 **Build Stage**: Multi-platform Docker image builds
- 🔒 **Security Scan**: Vulnerability scanning with Trivy
- 🚀 **Deploy Stage**: Podman-based deployment
- ✅ **Health Checks**: Automated health verification
- 📢 **Notifications**: Status reporting

#### Usage

```yaml
name: Deploy n8n

on:
  push:
    branches: [main]

jobs:
  deploy:
    uses: labjp-xyz/github-workflows/.github/workflows/deploy-n8n.yml@main
    with:
      skip-build: false
      environment: production
      image-tag: latest
      dockerfile-path: ./docker/images/n8n/Dockerfile
      deployment-path: /opt/n8n
      runner-labels: '["self-hosted"]'
    secrets:
      github-token: ${{ secrets.GITHUB_TOKEN }}
```

#### Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `skip-build` | Skip build stage | No | `false` |
| `environment` | Deployment environment | No | `production` |
| `image-tag` | Image tag to deploy | No | `latest` |
| `dockerfile-path` | Path to Dockerfile | No | `./docker/images/n8n/Dockerfile` |
| `deployment-path` | Server deployment path | No | `/opt/n8n` |
| `runner-labels` | Runner labels (JSON array) | No | `["self-hosted"]` |

## Available Actions

### docker-build

Builds and pushes Docker images to a container registry.

```yaml
- uses: labjp-xyz/github-workflows/.github/actions/docker-build@main
  with:
    registry: ghcr.io
    image-name: ${{ github.repository_owner }}/my-app
    dockerfile: ./Dockerfile
    context: .
    platforms: linux/amd64,linux/arm64
    registry-username: ${{ github.actor }}
    registry-password: ${{ secrets.GITHUB_TOKEN }}
```

### podman-deploy

Deploys containers using Podman with optional PostgreSQL.

```yaml
- uses: labjp-xyz/github-workflows/.github/actions/podman-deploy@main
  with:
    deployment-path: /opt/myapp
    user: myapp
    postgres-enabled: true
    app-image: ghcr.io/owner/app:latest
    app-name: myapp
    app-port: 8080
```

### health-check

Performs health checks on deployed services.

```yaml
- uses: labjp-xyz/github-workflows/.github/actions/health-check@main
  with:
    service-url: http://localhost:8080
    wait-time: 30
    max-retries: 10
    postgres-check: true
```

## Requirements

### Self-Hosted Runner
- Container runtime: Podman or Docker
- User permissions: Ability to run containers
- Network access: GitHub, container registries

### Repository Secrets
- `GITHUB_TOKEN`: Automatically provided
- Additional secrets as needed by your application

## Examples

### Basic Deployment
```yaml
jobs:
  deploy:
    uses: labjp-xyz/github-workflows/.github/workflows/deploy-n8n.yml@main
    secrets:
      github-token: ${{ secrets.GITHUB_TOKEN }}
```

### Skip Build (Use Existing Image)
```yaml
jobs:
  deploy:
    uses: labjp-xyz/github-workflows/.github/workflows/deploy-n8n.yml@main
    with:
      skip-build: true
      image-tag: v1.2.3
    secrets:
      github-token: ${{ secrets.GITHUB_TOKEN }}
```

### Multi-Environment Setup
```yaml
jobs:
  deploy-staging:
    uses: labjp-xyz/github-workflows/.github/workflows/deploy-n8n.yml@main
    with:
      environment: staging
    secrets:
      github-token: ${{ secrets.GITHUB_TOKEN }}

  deploy-production:
    needs: deploy-staging
    uses: labjp-xyz/github-workflows/.github/workflows/deploy-n8n.yml@main
    with:
      environment: production
    secrets:
      github-token: ${{ secrets.GITHUB_TOKEN }}
```

## Contributing

1. Create feature branch
2. Add/modify workflows or actions
3. Test in your repository
4. Submit pull request

## License

MIT
