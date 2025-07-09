# Duro API Gateway

Nginx-based API gateway for the Duro Multi-Pharmacy Platform. Routes requests to various microservices deployed on Railway.

## Features

- **CORS Support** - Handles cross-origin requests for frontend applications
- **Service Routing** - Routes API requests to appropriate microservices
- **Health Checks** - Provides health endpoint for monitoring
- **Environment Variable Support** - Configurable service URLs

## Deployment to Railway

### Prerequisites
- Railway account
- Git repository (this one!)
- Existing Duro services deployed on Railway

### Steps

1. **Connect GitHub Repository to Railway**
   - Go to [Railway Dashboard](https://railway.app)
   - Click "New Project"
   - Select "Deploy from GitHub repo"
   - Choose this repository
   - Select "duro-backend/api-gateway" as the root directory

2. **Configure Environment Variables**
   
   Set these environment variables in Railway:
   ```bash
   USER_SERVICE_URL=https://your-user-service.railway.app
   PHARMACY_SERVICE_URL=https://your-pharmacy-service.railway.app
   DRUG_SERVICE_URL=https://your-drug-service.railway.app
   ```

3. **Deploy**
   - Railway will automatically build and deploy using the Dockerfile
   - The service will be available at a Railway-provided URL

## Environment Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `USER_SERVICE_URL` | URL of the user service | `https://duro-user-service.railway.app` |
| `PHARMACY_SERVICE_URL` | URL of the pharmacy service | `https://duro-pharmacy-service.railway.app` |
| `DRUG_SERVICE_URL` | URL of the drug service | `https://duro-drug-service.railway.app` |

## API Routes

The gateway routes the following endpoints:

### Authentication & Users
- `GET/POST /api/v1/auth/*` → User Service
- `GET/POST/PUT /api/v1/users/*` → User Service

### Pharmacies
- `GET/POST /api/v1/pharmacies/*` → Pharmacy Service
- `GET/POST/PUT /api/v1/admin/pharmacies/*` → Pharmacy Service

### Drugs
- `GET/POST /api/v1/drugs/*` → Drug Service
- `GET/POST/PUT /api/v1/admin/drugs/*` → Drug Service

### Health Check
- `GET /health` → Returns gateway status

## Local Development

```bash
# Build the Docker image
docker build -t duro-api-gateway .

# Run with environment variables
docker run -p 8080:80 \
  -e USER_SERVICE_URL=http://localhost:8091 \
  -e PHARMACY_SERVICE_URL=http://localhost:8082 \
  -e DRUG_SERVICE_URL=http://localhost:8083 \
  duro-api-gateway
```

## Configuration

The nginx configuration supports:
- Automatic CORS headers
- OPTIONS request handling
- Proxy headers for service communication
- Health check endpoint

## Architecture

```
Frontend Apps → API Gateway → Microservices
     ↓              ↓             ↓
duro-webapp → nginx proxy → user-service
duro-pharmacy → nginx proxy → pharmacy-service
duro-management → nginx proxy → drug-service
```

## Troubleshooting

### Common Issues

1. **502 Bad Gateway**
   - Check that target services are running
   - Verify environment variables are set correctly
   - Check service URLs are accessible

2. **CORS Errors**
   - Ensure the gateway is properly routing requests
   - Check that CORS headers are being added

3. **Health Check Fails**
   - Verify nginx is running: `curl https://your-gateway.railway.app/health`

### Logs

View logs in Railway dashboard or using Railway CLI:
```bash
railway logs
``` 