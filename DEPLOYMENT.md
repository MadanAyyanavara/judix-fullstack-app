# Deployment Guide

## Overview

This guide covers deploying the Judix Full-Stack Application to various hosting platforms.

---

## Table of Contents

1. [Local Development](#local-development)
2. [Heroku Deployment](#heroku-deployment)
3. [AWS Deployment](#aws-deployment)
4. [DigitalOcean Deployment](#digitalocean-deployment)
5. [Vercel/Netlify Deployment](#vercel--netlify-deployment)
6. [Docker Deployment](#docker-deployment)

---

## Local Development

### Prerequisites

- Node.js v14+
- MongoDB (local or Atlas)
- npm or yarn
- Git

### Setup Instructions

```bash
# Clone repository
git clone https://github.com/MadanAyyanavara/judix-fullstack-app.git
cd judix-fullstack-app

# Backend setup
cd backend
npm install
cp .env.example .env
# Edit .env with your MongoDB URI
npm start
# Backend runs on http://localhost:5000

# Frontend setup (in new terminal)
cd frontend
npm install
npm run dev
# Frontend runs on http://localhost:5173
```

### Environment Variables (Backend)

```bash
NODE_ENV=development
PORT=5000
DATABASE_URL=mongodb://localhost:27017/judix
JWT_SECRET=your_jwt_secret_key_here
JWT_EXPIRE=7d
CORS_ORIGIN=http://localhost:5173
```

---

## Heroku Deployment

### Prerequisites

- Heroku CLI installed
- Heroku account
- MongoDB Atlas account (free tier available)

### Backend Deployment Steps

```bash
# Login to Heroku
heroku login

# Create Heroku app
cd backend
heroku create judix-backend-prod

# Set environment variables
heroku config:set NODE_ENV=production
heroku config:set DATABASE_URL=mongodb+srv://user:password@cluster.mongodb.net/judix
heroku config:set JWT_SECRET=your_production_secret_key
heroku config:set CORS_ORIGIN=https://judix-frontend.herokuapp.com

# Deploy to Heroku
git push heroku main

# View logs
heroku logs --tail
```

### Frontend Deployment Steps

For frontend, use Vercel or Netlify (better options for React)

---

## AWS Deployment

### Architecture

```
Route 53 (DNS)
    ↓
CloudFront (CDN)
    ↓
    ├─→ S3 (Frontend)
    └─→ ALB (Load Balancer)
         ↓
       EC2 Instances (Node.js)
         ↓
    RDS (MongoDB Atlas)
```

### Frontend Deployment (S3 + CloudFront)

```bash
# Build frontend
cd frontend
npm run build

# Create S3 bucket
aws s3 mb s3://judix-frontend-prod --region us-east-1

# Upload files
aws s3 sync dist/ s3://judix-frontend-prod --delete

# Create CloudFront distribution
# (Use AWS Console or AWS CLI)
```

### Backend Deployment (EC2)

```bash
# Connect to EC2 instance
ssh -i your-key.pem ec2-user@your-instance-ip

# Install dependencies
sudo yum update -y
sudo yum install nodejs npm git -y

# Clone and setup
git clone https://github.com/MadanAyyanavara/judix-fullstack-app.git
cd judix-fullstack-app/backend
npm install

# Create .env file
sudo nano .env
# Add your production environment variables

# Start with PM2
sudo npm install -g pm2
pm2 start src/server.js --name "judix-backend"
pm2 save

# Setup reverse proxy with NGINX
sudo yum install nginx -y
sudo systemctl start nginx
# Configure NGINX to proxy to localhost:5000
```

### NGINX Configuration

```nginx
server {
    listen 80;
    server_name api.yourdomain.com;

    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

---

## DigitalOcean Deployment

### App Platform (Easiest)

```bash
# Push to GitHub
git add .
git commit -m "Prepare for deployment"
git push origin main

# Create app.yaml
name: judix-app
services:
- name: backend
  github:
    repo: MadanAyyanavara/judix-fullstack-app
    branch: main
  build_command: npm install
  run_command: npm start
  envs:
  - key: DATABASE_URL
    value: mongodb+srv://user:pass@cluster.mongodb.net/judix
  - key: JWT_SECRET
    value: your_secret
  http_port: 5000

- name: frontend
  github:
    repo: MadanAyyanavara/judix-fullstack-app
    branch: main
  build_command: cd frontend && npm install && npm run build
  source_dir: frontend/dist
  http_port: 80
```

### Deploy Using CLI

```bash
doctl apps create --spec app.yaml
```

---

## Vercel / Netlify Deployment

### Vercel Frontend Deployment

```bash
cd frontend
npm install -g vercel
vercel

# Answer prompts and deploy
```

### Netlify Frontend Deployment

```bash
cd frontend
npm run build

# Deploy using CLI
npm install -g netlify-cli
netlify deploy --prod --dir=dist
```

### Configure API URL

```javascript
// frontend/src/services/api.js
const API_URL = process.env.REACT_APP_API_URL || 'https://your-backend-url.com/api'

export const api = axios.create({
  baseURL: API_URL
})
```

---

## Docker Deployment

### Build Images

```bash
# Backend
cd backend
docker build -t judix-backend:latest .

# Frontend
cd ../frontend
docker build -t judix-frontend:latest .
```

### Docker Compose

```yaml
version: '3.8'

services:
  backend:
    image: judix-backend:latest
    ports:
      - "5000:5000"
    environment:
      - DATABASE_URL=mongodb://mongo:27017/judix
      - JWT_SECRET=your_secret
    depends_on:
      - mongo

  frontend:
    image: judix-frontend:latest
    ports:
      - "80:80"
    environment:
      - REACT_APP_API_URL=http://backend:5000/api

  mongo:
    image: mongo:latest
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db

volumes:
  mongo_data:
```

### Run with Docker Compose

```bash
docker-compose up -d

# View logs
docker-compose logs -f

# Stop services
docker-compose down
```

---

## Kubernetes Deployment

### Create Deployment

```bash
# Create namespace
kubectl create namespace judix

# Create ConfigMap for environment variables
kubectl create configmap judix-config \
  --from-literal=DATABASE_URL=mongodb://mongo:27017/judix \
  --from-literal=JWT_SECRET=your_secret \
  -n judix

# Apply deployments
kubectl apply -f k8s/backend-deployment.yaml -n judix
kubectl apply -f k8s/frontend-deployment.yaml -n judix
kubectl apply -f k8s/services.yaml -n judix

# Check status
kubectl get pods -n judix
kubectl get svc -n judix
```

---

## CI/CD Pipeline

### GitHub Actions

Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2

      - name: Deploy to Heroku
        uses: akhileshns/heroku-deploy@v3.12.12
        with:
          heroku_api_key: ${{ secrets.HEROKU_API_KEY }}
          heroku_app_name: judix-backend-prod
          heroku_email: your-email@example.com
          appdir: backend

      - name: Deploy Frontend to Vercel
        run: |
          cd frontend
          npm ci
          npm run build
          npx vercel --prod --token=${{ secrets.VERCEL_TOKEN }}
```

---

## Health Checks

Add health check endpoints to backend:

```javascript
app.get('/health', (req, res) => {
  res.json({ status: 'UP', timestamp: new Date() })
})

app.get('/ready', async (req, res) => {
  try {
    await mongoose.connection.db.admin().ping()
    res.json({ status: 'READY' })
  } catch (error) {
    res.status(503).json({ status: 'NOT_READY' })
  }
})
```

---

## Monitoring

### Logging

- **Heroku**: View with `heroku logs --tail`
- **AWS**: Use CloudWatch Logs
- **DigitalOcean**: View in App Platform dashboard

### Performance Monitoring

- New Relic
- Datadog
- Sentry for error tracking

---

## Database Backups

### MongoDB Atlas

```bash
# Enable automated backups in MongoDB Atlas
# Dashboard → Backup → Automated Backups → Enable

# Manual backup
mongodump --uri="mongodb+srv://user:pass@cluster.mongodb.net/judix" --out=./backup

# Restore
mongorestore --uri="mongodb+srv://user:pass@cluster.mongodb.net/judix" ./backup
```

---

## Troubleshooting

### Common Issues

1. **CORS Errors**: Update `CORS_ORIGIN` in .env
2. **Database Connection**: Verify MongoDB URI and network access
3. **Environment Variables**: Ensure all required vars are set
4. **Port Conflicts**: Use different ports for multiple services
5. **Memory Issues**: Increase heap size with `NODE_OPTIONS=--max-old-space-size=4096`

---

## Production Checklist

- [ ] Environment variables configured
- [ ] Database backups enabled
- [ ] HTTPS/SSL configured
- [ ] Rate limiting enabled
- [ ] CORS properly configured
- [ ] Error monitoring setup (Sentry)
- [ ] Performance monitoring setup
- [ ] CDN configured for static assets
- [ ] Health checks implemented
- [ ] Automated deployments configured

---

For detailed information on specific platforms, refer to their official documentation.
