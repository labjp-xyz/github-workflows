# 🚀 GitHub Actions & Workflows Library

Biblioteca centralizada de **Actions** (componentes reutilizables) y **Workflows** (pipelines completos).

## 📁 Estructura

```
github-workflows/
├── actions/                      # 🔧 COMPONENTES REUTILIZABLES
│   ├── build/                   # Construcción de artefactos
│   │   └── container/           # Build de imágenes Docker/Podman
│   ├── deploy/                  # Despliegue
│   │   └── container/           # Deploy de contenedores
│   ├── security/                # Seguridad
│   │   └── scan-container/      # Escaneo de vulnerabilidades
│   ├── test/                    # Testing
│   │   ├── python/              # Tests Python con pytest
│   │   └── node/                # Tests Node.js
│   ├── notify/                  # Notificaciones
│   │   └── slack/               # Notificaciones Slack
│   └── apps/                    # Específicas por aplicación
│       └── n8n/
│           └── deploy-stack/    # Deploy stack completo n8n
│
└── .github/
    └── workflows/               # 📋 WORKFLOWS REUTILIZABLES Y CI/CD
        ├── deploy-api.yml       # Pipeline genérico para APIs (reutilizable)
        ├── deploy-n8n.yml       # Pipeline específico para n8n (reutilizable)
        └── test-workflows.yml   # Tests internos del repo
```

## 🎯 Diferencia entre Actions y Workflows

| Tipo | Qué es | Se usa para | Ejemplo |
|------|--------|-------------|---------|
| **Action** | Componente reutilizable | Una tarea específica | Escanear, deployar, notificar |
| **Workflow** | Pipeline completo | Orquestar múltiples actions | CI/CD completo |

## 📖 Cómo Usar desde tus Proyectos

### 1️⃣ Para APIs Simples (Python, Node, Go)

```yaml
# tu-proyecto/.github/workflows/deploy.yml
name: Deploy My API

on:
  push:
    branches: [main]

jobs:
  deploy:
    uses: jeanlopezxyz/github-workflows/.github/workflows/deploy-api.yml@main
    with:
      api-name: mi-api
      api-path: ./src
      api-type: python         # python, node, o go
      api-port: '8000'
      environment: production
      run-tests: true          # Opcional: ejecutar tests
      security-scan: true      # Opcional: escaneo de seguridad
    secrets: inherit
```

### 2️⃣ Para n8n (Stack Complejo)

```yaml
# n8n-project/.github/workflows/deploy.yml
name: Deploy n8n

on:
  push:
    branches: [main]

jobs:
  deploy:
    uses: jeanlopezxyz/github-workflows/.github/workflows/deploy-n8n.yml@main
    with:
      environment: production
      n8n-version: latest
    secrets:
      N8N_DB_PASSWORD: ${{ secrets.N8N_DB_PASSWORD }}
      N8N_USER: ${{ secrets.N8N_USER }}
      N8N_PASSWORD: ${{ secrets.N8N_PASSWORD }}
      N8N_HOST: n8n.midominio.com
```

## 💡 Ejemplos Reales

### HEIC Converter

```yaml
# heic-converter/.github/workflows/deploy.yml
name: Deploy HEIC Converter

on:
  push:
    branches: [main]
    paths:
      - 'api/**'
      - '.github/workflows/**'

jobs:
  deploy:
    uses: jeanlopez/github-workflows/workflows/deploy-api.yml@v1.0.0
    with:
      api-name: heic-converter
      api-path: ./api
      api-type: python
      api-port: '5000'
      environment: production
    secrets: inherit
```

### API Node.js

```yaml
# nodejs-api/.github/workflows/deploy.yml
name: Deploy Node API

on:
  push:
    branches: [main]

jobs:
  deploy:
    uses: jeanlopez/github-workflows/workflows/deploy-api.yml@v1.0.0
    with:
      api-name: backend-api
      api-path: ./backend
      api-type: node
      api-port: '3000'
      environment: production
    secrets: inherit
```

### API Go

```yaml
# go-api/.github/workflows/deploy.yml
name: Deploy Go API

on:
  push:
    branches: [main]

jobs:
  deploy:
    uses: jeanlopez/github-workflows/workflows/deploy-api.yml@v1.0.0
    with:
      api-name: go-service
      api-path: ./cmd/api
      api-type: go
      api-port: '8080'
      environment: production
      run-tests: true
    secrets: inherit
```

## 🔧 Workflows Disponibles

### `deploy-api.yml`
Pipeline CI/CD completo para APIs.

**Inputs:**
- `api-name` (required): Nombre de la API
- `api-path` (required): Path al código fuente
- `api-type` (required): Tipo de API (python/node/go)
- `api-port`: Puerto de la API (default: 3000)
- `environment`: Ambiente (default: production)
- `run-tests`: Ejecutar tests (default: true)
- `security-scan`: Escaneo de seguridad (default: true)

**Stages:**
1. ✅ Testing (si `run-tests: true`)
2. 🔨 Build de imagen
3. 🔒 Security scan (si `security-scan: true`)
4. 🚀 Deploy
5. 🏥 Health check
6. 📢 Notificación

### `deploy-n8n.yml`
Pipeline específico para n8n con PostgreSQL y Redis.

**Inputs:**
- `environment`: Ambiente (default: production)
- `n8n-version`: Versión de n8n (default: latest)

**Secrets requeridos:**
- `N8N_DB_PASSWORD`: Password de PostgreSQL
- `N8N_USER`: Usuario admin de n8n
- `N8N_PASSWORD`: Password admin de n8n
- `N8N_HOST`: Dominio de n8n

**Despliega:**
1. PostgreSQL 15
2. Redis 7
3. n8n
4. APIs adicionales (HEIC converter, etc.)

## 📊 Actions Disponibles

### Build
- `actions/build/container`: Construye imágenes Docker/Podman

### Deploy
- `actions/deploy/container`: Despliega contenedores
- `actions/deploy/health-check`: Verifica salud del servicio

### Security
- `actions/security/scan-container`: Escanea vulnerabilidades con Trivy
- `actions/security/scan-dependencies`: Escanea dependencias
- `actions/security/scan-secrets`: Detecta secretos expuestos

### Test
- `actions/test/python`: Tests Python con pytest
- `actions/test/node`: Tests Node.js con npm/yarn
- `actions/test/smoke`: Tests de smoke post-deploy

### Notify
- `actions/notify/slack`: Notificaciones a Slack
- `actions/notify/email`: Notificaciones por email

### Apps Específicas
- `actions/apps/n8n/deploy-stack`: Deploy completo de n8n

## 🚀 Setup Inicial

### 1. Crear repositorio GitHub
```bash
gh repo create github-workflows --public
git clone https://github.com/jeanlopezxyz/github-workflows
```

### 2. Subir código
```bash
cd github-workflows
git add .
git commit -m "Initial release"
git push origin main
```

### 3. Crear tag de versión
```bash
git tag -a v1.0.0 -m "Release v1.0.0"
git push origin v1.0.0
```

### 4. En tus proyectos, referenciar
```yaml
uses: jeanlopezxyz/github-workflows/workflows/deploy-api.yml@main
```

## 🎯 Decisión: ¿Qué workflow usar?

| Si tu app... | Usa | Ejemplo |
|--------------|-----|---------|
| Es una API simple (REST, GraphQL) | `deploy-api.yml` | HEIC Converter, Backend API |
| Es una aplicación web estática | `deploy-frontend.yml` | React, Vue (próximamente) |
| Necesita múltiples servicios | `deploy-n8n.yml` o crear específico | n8n + PostgreSQL + Redis |
| Es un microservicio | `deploy-api.yml` | Cualquier microservicio |

## 🔐 Secrets Requeridos

En tu repositorio, configura estos secrets (Settings → Secrets → Actions):

### Para todas las APIs
- `SLACK_WEBHOOK`: URL del webhook de Slack (opcional)
- `DEPLOY_HOST`: Host del servidor de despliegue

### Para n8n específicamente
- `N8N_DB_PASSWORD`: Password de PostgreSQL
- `N8N_USER`: Usuario admin
- `N8N_PASSWORD`: Password admin
- `N8N_HOST`: Dominio (ej: n8n.tudominio.com)

## 🏷️ Versionado

- `@v1.0.0`: Versión específica (recomendado para producción)
- `@v1`: Última versión 1.x.x
- `@main`: Última versión de desarrollo (no usar en producción)

## 📈 Roadmap

- [ ] `deploy-frontend.yml` para apps React/Vue/Angular
- [ ] `deploy-mobile.yml` para apps móviles
- [ ] Actions para AWS/GCP/Azure
- [ ] Integración con Kubernetes
- [ ] Métricas y monitoring

## 🤝 Contribuir

1. Fork el repositorio
2. Crea una branch (`git checkout -b feature/nueva-action`)
3. Commit cambios (`git commit -am 'Add nueva action'`)
4. Push a la branch (`git push origin feature/nueva-action`)
5. Crea un Pull Request

## 📄 Licencia

MIT - Libre para uso comercial y personal.