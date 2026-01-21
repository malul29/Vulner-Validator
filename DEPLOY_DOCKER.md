# Docker Deployment Guide

Panduan lengkap untuk deploy **Vulner-Validator** menggunakan Docker.

---

## 🎯 Prerequisites

- **Docker** - Install dari [docker.com](https://docs.docker.com/get-docker/)
- **Docker Compose** (optional) - Biasanya sudah include dengan Docker Desktop

**Verify installation:**
```bash
docker --version
docker compose version
```

---

## 🚀 Quick Start

### Option 1: Docker Compose (Recommended)

```bash
# Build dan run dalam satu command
docker compose up -d

# View logs
docker compose logs -f

# Stop
docker compose down
```

### Option 2: Docker Commands

```bash
# Build image
docker build -t vulner-validator .

# Run container
docker run -d \
  --name vulner-validator \
  -p 3000:3000 \
  vulner-validator

# View logs
docker logs -f vulner-validator

# Stop dan remove
docker stop vulner-validator
docker rm vulner-validator
```

**Access aplikasi:** `http://localhost:3000`

---

## 📦 Build Docker Image

### Basic Build
```bash
docker build -t vulner-validator:latest .
```

### Build dengan Tag Specific
```bash
docker build -t vulner-validator:1.1.0 .
docker build -t vulner-validator:production .
```

### Build dengan Custom Args (jika ada)
```bash
docker build \
  --build-arg NODE_ENV=production \
  -t vulner-validator:latest \
  .
```

**Check image:**
```bash
docker images | grep vulner-validator
```

---

## 🏃 Run Container

### Basic Run
```bash
docker run -d \
  --name vulner-validator \
  -p 3000:3000 \
  vulner-validator:latest
```

### Run dengan Environment Variables
```bash
docker run -d \
  --name vulner-validator \
  -p 3000:3000 \
  -e NODE_ENV=production \
  -e PORT=3000 \
  vulner-validator:latest
```

### Run dengan Restart Policy
```bash
docker run -d \
  --name vulner-validator \
  -p 3000:3000 \
  --restart unless-stopped \
  vulner-validator:latest
```

### Run dengan Resource Limits
```bash
docker run -d \
  --name vulner-validator \
  -p 3000:3000 \
  --memory="512m" \
  --cpus="0.5" \
  vulner-validator:latest
```

---

## 🔧 Docker Compose Usage

### Start Services
```bash
# Start in background
docker compose up -d

# Start with build
docker compose up -d --build

# Start with logs
docker compose up
```

### Manage Services
```bash
# Stop services
docker compose stop

# Start stopped services
docker compose start

# Restart services
docker compose restart

# Stop dan remove containers
docker compose down

# Stop, remove, dan delete volumes
docker compose down -v
```

### View Logs
```bash
# All services
docker compose logs -f

# Specific service
docker compose logs -f vulner-validator

# Last 100 lines
docker compose logs --tail=100
```

### Scale Services (jika diperlukan)
```bash
docker compose up -d --scale vulner-validator=3
```

---

## 📊 Container Management

### Check Status
```bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# Check container health
docker inspect vulner-validator | grep -A 10 Health
```

### View Logs
```bash
# Stream logs
docker logs -f vulner-validator

# Last 100 lines
docker logs --tail 100 vulner-validator

# Logs with timestamps
docker logs -t vulner-validator
```

### Execute Commands in Container
```bash
# Open shell
docker exec -it vulner-validator sh

# Run single command
docker exec vulner-validator node -v
docker exec vulner-validator npm list
```

### Stats & Resources
```bash
# Real-time stats
docker stats vulner-validator

# Container details
docker inspect vulner-validator
```

---

## 🔄 Update & Redeploy

```bash
# Stop and remove old container
docker stop vulner-validator
docker rm vulner-validator

# Rebuild image
docker build -t vulner-validator:latest .

# Run new container
docker run -d \
  --name vulner-validator \
  -p 3000:3000 \
  --restart unless-stopped \
  vulner-validator:latest
```

**With Docker Compose:**
```bash
docker compose down
docker compose up -d --build
```

---

## 🌐 Deploy to Cloud Platforms

### 1️⃣ Docker Hub

```bash
# Login
docker login

# Tag image
docker tag vulner-validator:latest yourusername/vulner-validator:latest

# Push to Docker Hub
docker push yourusername/vulner-validator:latest

# Pull dan run dari anywhere
docker run -d -p 3000:3000 yourusername/vulner-validator:latest
```

### 2️⃣ AWS ECS (Elastic Container Service)

```bash
# Install AWS CLI
# Configure: aws configure

# Create ECR repository
aws ecr create-repository --repository-name vulner-validator

# Get login command
aws ecr get-login-password --region us-east-1 | \
  docker login --username AWS --password-stdin <account-id>.dkr.ecr.us-east-1.amazonaws.com

# Tag and push
docker tag vulner-validator:latest <account-id>.dkr.ecr.us-east-1.amazonaws.com/vulner-validator:latest
docker push <account-id>.dkr.ecr.us-east-1.amazonaws.com/vulner-validator:latest

# Deploy via ECS console atau CLI
```

### 3️⃣ Google Cloud Run

```bash
# Install gcloud CLI
# Configure: gcloud init

# Build and submit
gcloud builds submit --tag gcr.io/PROJECT-ID/vulner-validator

# Deploy
gcloud run deploy vulner-validator \
  --image gcr.io/PROJECT-ID/vulner-validator \
  --platform managed \
  --region us-central1 \
  --allow-unauthenticated \
  --port 3000
```

### 4️⃣ Azure Container Instances

```bash
# Login
az login

# Create resource group
az group create --name vulner-validator-rg --location eastus

# Create container registry
az acr create --resource-group vulner-validator-rg \
  --name vulnervalidatoracr --sku Basic

# Login to ACR
az acr login --name vulnervalidatoracr

# Tag and push
docker tag vulner-validator:latest vulnervalidatoracr.azurecr.io/vulner-validator:latest
docker push vulnervalidatoracr.azurecr.io/vulner-validator:latest

# Deploy
az container create \
  --resource-group vulner-validator-rg \
  --name vulner-validator \
  --image vulnervalidatoracr.azurecr.io/vulner-validator:latest \
  --dns-name-label vulner-validator \
  --ports 3000
```

### 5️⃣ DigitalOcean App Platform

```bash
# Via Web Console:
# 1. Push image ke Docker Hub
# 2. Create new App di DigitalOcean
# 3. Select "Docker Hub" sebagai source
# 4. Configure ports dan environment
```

### 6️⃣ Railway

```bash
# Install Railway CLI
npm install -g @railway/cli

# Login
railway login

# Initialize project
railway init

# Deploy
railway up

# Set environment
railway run docker run -d -p 3000:3000 vulner-validator
```

---

## 🔐 Environment Variables

Create `.env` file untuk local development:

```bash
NODE_ENV=production
PORT=3000
```

**Dengan Docker run:**
```bash
docker run -d \
  --name vulner-validator \
  -p 3000:3000 \
  --env-file .env \
  vulner-validator:latest
```

**Dengan Docker Compose**, tambahkan ke `docker-compose.yml`:
```yaml
services:
  vulner-validator:
    env_file:
      - .env
```

---

## 🐛 Troubleshooting

### Container tidak start

```bash
# Check logs
docker logs vulner-validator

# Check container status
docker ps -a

# Inspect container
docker inspect vulner-validator
```

### Port sudah dipakai

```bash
# Windows: Check port usage
netstat -ano | findstr :3000

# Kill process
taskkill /PID <PID> /F

# Atau gunakan port lain
docker run -d -p 8080:3000 vulner-validator
```

### Image terlalu besar

```bash
# Check image size
docker images vulner-validator

# Remove unused images
docker image prune

# Multi-stage build optimization (sudah implemented)
```

### Health check failed

```bash
# Manual health check
curl http://localhost:3000/api/health

# Check logs
docker logs vulner-validator

# Restart container
docker restart vulner-validator
```

### Build gagal

```bash
# Clean build cache
docker builder prune

# Build tanpa cache
docker build --no-cache -t vulner-validator .

# Check Dockerfile syntax
docker build --check -t vulner-validator .
```

### Container crash / restart loop

```bash
# Check logs untuk error
docker logs --tail 100 vulner-validator

# Check health status
docker inspect vulner-validator | grep -A 5 Health

# Run interactively untuk debugging
docker run -it --rm vulner-validator sh
```

---

## 🧹 Cleanup Commands

```bash
# Stop all containers
docker stop $(docker ps -aq)

# Remove all stopped containers
docker container prune

# Remove unused images
docker image prune

# Remove all unused data (containers, images, networks, volumes)
docker system prune -a

# Remove specific image
docker rmi vulner-validator:latest

# Remove specific container
docker rm vulner-validator
```

**With Docker Compose:**
```bash
# Stop and remove
docker compose down

# Remove with volumes
docker compose down -v

# Remove with images
docker compose down --rmi all
```

---

## 📊 Monitoring

### Resource Usage
```bash
# Real-time stats
docker stats

# Specific container
docker stats vulner-validator

# Export stats
docker stats --no-stream --format "table {{.Container}}\t{{.CPUPerc}}\t{{.MemUsage}}"
```

### Logs Management
```bash
# Rotate logs (set max size)
docker run -d \
  --name vulner-validator \
  -p 3000:3000 \
  --log-driver json-file \
  --log-opt max-size=10m \
  --log-opt max-file=3 \
  vulner-validator:latest
```

---

## 🔒 Security Best Practices

✅ **Already Implemented:**
- Non-root user (`nodejs`)
- Multi-stage build (smaller attack surface)
- Alpine base image (minimal footprint)
- Health checks
- `.dockerignore` (exclude sensitive files)

**Additional recommendations:**
```bash
# Scan for vulnerabilities
docker scan vulner-validator:latest

# Use specific Node version (already using 18-alpine)
# Update dependencies regularly
npm audit
npm audit fix
```

---

## 📚 Additional Resources

- [Docker Documentation](https://docs.docker.com/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [Docker Best Practices](https://docs.docker.com/develop/dev-best-practices/)
- [Node.js Docker Best Practices](https://github.com/nodejs/docker-node/blob/main/docs/BestPractices.md)

---

## 🆚 Comparison: Docker vs Other Platforms

| Platform | Setup | Portability | Control | Cost |
|----------|-------|-------------|---------|------|
| **Docker** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | Free (local) |
| Heroku | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ | $0-$7/mo |
| VPS | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | $5-10/mo |
| Vercel | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐ | Free tier |

**Docker Advantages:**
- 🎯 Run anywhere (local, cloud, VPS)
- 🔒 Consistent environment
- 📦 Easy to scale
- 🚀 Fast deployment
- 💰 No vendor lock-in

---

## 🎯 Summary

**Quick Commands:**
```bash
# Development
docker compose up -d
docker compose logs -f

# Production
docker build -t vulner-validator:latest .
docker run -d -p 3000:3000 --restart unless-stopped vulner-validator:latest

# Update
docker compose down && docker compose up -d --build

# Cleanup
docker compose down
docker system prune
```

**Important Files:**
- [`Dockerfile`](file:///d:/Vulner-Validator/Dockerfile) - Image definition
- [`docker-compose.yml`](file:///d:/Vulner-Validator/docker-compose.yml) - Orchestration
- [`.dockerignore`](file:///d:/Vulner-Validator/.dockerignore) - Build exclusions

**Access:**
- Local: `http://localhost:3000`
- Health: `http://localhost:3000/api/health`

---

## ✨ Next Steps

1. ✅ Build image lokal: `docker build -t vulner-validator .`
2. ✅ Test lokal: `docker compose up -d`
3. 🧪 Test API endpoints
4. 📤 Push ke Docker Hub (optional)
5. 🌐 Deploy ke cloud platform (optional)

**Happy containerizing! 🐳**
