# 🚀 Deployment Guide

## Cloud Deployment Architecture

Since this is a multi-component system, you'll need to deploy each part separately:

```
┌──────────────────┐         ┌──────────────────┐
│  Streamlit Cloud │ ──────> │ Backend Service  │
│    (Frontend)    │         │ (Render/Railway) │
└──────────────────┘         └──────────────────┘
                                      │
                                      ▼
                             ┌──────────────────┐
                             │   Qdrant Cloud   │
                             │  (Vector Store)  │
                             └──────────────────┘
```

## Step 1: Deploy Backend to Render

### 1.1 Create Render Account
Sign up at https://render.com

### 1.2 Prepare Backend for Render

Create `render.yaml` in the project root:
```yaml
services:
  - type: web
    name: doors-rag-backend
    env: docker
    dockerfilePath: ./Dockerfile
    envVars:
      - key: OPENAI_API_KEY
        sync: false
      - key: QDRANT_URL
        sync: false
      - key: QDRANT_API_KEY
        sync: false
```

### 1.3 Deploy to Render
1. Connect GitHub repository
2. Create new Web Service
3. Select Docker deployment
4. Add environment variables
5. Deploy

Your backend URL will be: `https://doors-rag-backend.onrender.com`

## Step 2: Set Up Qdrant Cloud

### 2.1 Create Qdrant Cloud Account
Sign up at https://cloud.qdrant.io

### 2.2 Create Cluster
1. Create new cluster
2. Choose region (select closest)
3. Get connection details:
   - URL: `https://xxx.qdrant.io`
   - API Key: `your-api-key`

### 2.3 Upload Vectors
Run the PDF processing script with cloud credentials:
```python
QDRANT_URL=https://xxx.qdrant.io
QDRANT_API_KEY=your-api-key
```

## Step 3: Deploy Frontend to Streamlit Cloud

### 3.1 Prepare Repository Structure
Your repository should have:
```
Doors-RAG/
├── frontend/
│   └── app.py
├── requirements.txt
├── .streamlit/
│   └── config.toml
└── README.md
```

### 3.2 Create Streamlit App

1. Go to https://streamlit.io/cloud
2. Sign in with GitHub
3. Click "New app"
4. Select repository: `DefoxxAnalytics/Doors-RAG`
5. Branch: `main`
6. Main file path: `frontend/app.py`
7. App URL: Choose custom URL (e.g., `doors-rag`)

### 3.3 Configure Secrets

In Streamlit Cloud settings, add secrets:
```toml
# .streamlit/secrets.toml
BACKEND_URL = "https://doors-rag-backend.onrender.com"
API_URL = "https://doors-rag-backend.onrender.com"

# Optional: If you want direct API access from frontend
OPENAI_API_KEY = "sk-proj-..."
```

### 3.4 Deploy
Click "Deploy" and wait for the app to build.

## Step 4: Update Frontend Configuration

Update `frontend/app.py` to use cloud backend:
```python
# Backend URL - use Streamlit secrets or environment variable
BACKEND_URL = st.secrets.get("BACKEND_URL", os.getenv("API_URL", "http://localhost:8000"))
```

## Alternative: All-in-One Deployment Options

### Option A: Railway (Easiest)
Railway can deploy all services together:

1. Sign up at https://railway.app
2. Import GitHub repository
3. Railway will auto-detect services
4. Add environment variables
5. Deploy all services

### Option B: Google Cloud Run
For production-grade deployment:

1. Build and push Docker images to GCR
2. Deploy backend to Cloud Run
3. Use Qdrant Cloud for vectors
4. Deploy frontend to Streamlit Cloud

### Option C: AWS ECS
Enterprise solution:

1. Push images to ECR
2. Create ECS task definitions
3. Deploy services to ECS
4. Use Application Load Balancer

## Environment Variables Summary

### Backend (Render/Railway)
```env
OPENAI_API_KEY=sk-proj-...
ANTHROPIC_API_KEY=sk-ant-...
QDRANT_URL=https://xxx.qdrant.io
QDRANT_API_KEY=your-qdrant-key
REDIS_URL=redis://...
```

### Frontend (Streamlit Cloud)
```env
BACKEND_URL=https://your-backend.onrender.com
API_URL=https://your-backend.onrender.com
```

## Cost Estimates (Monthly)

### Free Tier
- Streamlit Cloud: Free
- Render: Free (with spin-down)
- Qdrant Cloud: Free tier (1GB)
- Total: **$0/month**

### Production
- Streamlit Cloud: Free
- Render: $7/month (Starter)
- Qdrant Cloud: $29/month (4GB)
- Redis Cloud: $5/month
- Total: **~$41/month**

## Quick Deployment Script

```bash
#!/bin/bash
# deploy.sh

echo "Deploying Door RAG System..."

# Step 1: Push to GitHub
git add .
git commit -m "Update for cloud deployment"
git push origin main

# Step 2: Deploy backend to Render
echo "Deploy backend via Render dashboard"

# Step 3: Deploy to Streamlit
echo "Visit https://streamlit.io/cloud"
echo "Connect repository and deploy"

echo "Deployment initiated!"
```

## Monitoring

### Backend Health Check
```bash
curl https://your-backend.onrender.com/health
```

### Streamlit Status
Visit: https://share.streamlit.io/user/app/status

## Troubleshooting

### Frontend Can't Connect to Backend
- Check BACKEND_URL in Streamlit secrets
- Verify backend is running
- Check CORS settings

### Slow Response Times
- Render free tier spins down after 15 minutes
- Consider upgrading to paid tier
- Use caching strategically

### Vector Database Issues
- Verify Qdrant Cloud credentials
- Check collection exists
- Ensure vectors are uploaded

## Support

For deployment issues:
- Streamlit: https://discuss.streamlit.io
- Render: https://render.com/docs
- Qdrant: https://qdrant.tech/documentation

---

**Developed by MLawali@versatexmsp.com | © 2025**