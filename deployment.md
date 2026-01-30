"# 🚀 Deployment Guide

## Quick Start for GitHub

### 1. Initialize Git Repository

```bash
cd /app
git init
git add .
git commit -m \"Initial commit: India Air Quality × Health Dashboard\"
```

### 2. Create GitHub Repository

1. Go to https://github.com/new
2. Create a new repository (e.g., `india-air-quality-dashboard`)
3. **Do not** initialize with README (we already have one)

### 3. Push to GitHub

```bash
git remote add origin https://github.com/YOUR_USERNAME/india-air-quality-dashboard.git
git branch -M main
git push -u origin main
```

## Project Structure for Git

```
india-air-quality-dashboard/
├── backend/
│   ├── server.py
│   ├── requirements.txt
│   ├── .env (NOT committed - in .gitignore)
│   └── .env.example (committed)
├── frontend/
│   ├── src/
│   │   ├── App.js
│   │   ├── App.css
│   │   ├── index.css
│   │   └── components/
│   ├── public/
│   ├── package.json
│   ├── .env (NOT committed - in .gitignore)
│   └── .env.example (committed)
├── design_guidelines.json
├── ARCHITECTURE.md
├── README.md
├── .gitignore
└── DEPLOYMENT.md (this file)
```

## Deployment Options

### Option 1: Vercel (Frontend) + Railway (Backend)

#### Frontend on Vercel

1. **Install Vercel CLI**
```bash
npm install -g vercel
```

2. **Deploy Frontend**
```bash
cd frontend
vercel
```

3. **Set Environment Variables in Vercel Dashboard**
```
REACT_APP_BACKEND_URL=https://your-backend.railway.app
```

4. **Deploy to Production**
```bash
vercel --prod
```

#### Backend on Railway

1. Go to https://railway.app
2. Click \"New Project\" → \"Deploy from GitHub repo\"
3. Select your repository
4. Configure:
   - **Root Directory**: `/backend`
   - **Build Command**: `pip install -r requirements.txt`
   - **Start Command**: `uvicorn server:app --host 0.0.0.0 --port $PORT`

5. **Add Environment Variables**:
```
MONGO_URL=your_mongodb_connection_string
DB_NAME=air_quality_db
CORS_ORIGINS=https://your-frontend.vercel.app
PORT=8001
```

### Option 2: Netlify (Frontend) + Render (Backend)

#### Frontend on Netlify

1. **Install Netlify CLI**
```bash
npm install -g netlify-cli
```

2. **Deploy**
```bash
cd frontend
netlify deploy --prod
```

3. **Set Environment Variables**:
```
REACT_APP_BACKEND_URL=https://your-backend.onrender.com
```

#### Backend on Render

1. Go to https://render.com
2. Click \"New\" → \"Web Service\"
3. Connect your GitHub repository
4. Configure:
   - **Root Directory**: `backend`
   - **Build Command**: `pip install -r requirements.txt`
   - **Start Command**: `uvicorn server:app --host 0.0.0.0 --port $PORT`

5. **Add Environment Variables**:
```
MONGO_URL=your_mongodb_connection_string
DB_NAME=air_quality_db
CORS_ORIGINS=https://your-frontend.netlify.app
```

### Option 3: Docker Deployment

**Coming Soon**: Docker Compose setup for containerized deployment

## MongoDB Setup

### Option 1: MongoDB Atlas (Free Tier)

1. Go to https://www.mongodb.com/cloud/atlas
2. Create a free M0 cluster
3. Create database user
4. Whitelist IP addresses (0.0.0.0/0 for development)
5. Get connection string:
```
mongodb+srv://username:password@cluster.mongodb.net/air_quality_db?retryWrites=true&w=majority
```

### Option 2: Local MongoDB

```bash
# Install MongoDB
# Ubuntu/Debian
sudo apt-get install mongodb

# macOS
brew install mongodb-community

# Start MongoDB
mongod --dbpath /path/to/data
```

## Environment Variables Setup

### Backend .env
```env
MONGO_URL=mongodb://localhost:27017
# OR for MongoDB Atlas:
# MONGO_URL=mongodb+srv://username:password@cluster.mongodb.net/?retryWrites=true&w=majority

DB_NAME=air_quality_db
CORS_ORIGINS=http://localhost:3000,https://yourdomain.com
```

### Frontend .env
```env
REACT_APP_BACKEND_URL=http://localhost:8001
# OR for production:
# REACT_APP_BACKEND_URL=https://api.yourdomain.com
```

## Testing Deployment

### Backend Health Check
```bash
curl https://your-backend-url.com/api/
# Expected: {\"message\": \"Air Quality Health API\"}
```

### Test API Endpoints
```bash
# Get all cities
curl https://your-backend-url.com/api/air-quality/cities

# Get specific city
curl https://your-backend-url.com/api/air-quality/city/delhi

# Get correlation data
curl https://your-backend-url.com/api/health/correlation
```

### Frontend Check
1. Open https://your-frontend-url.com
2. Verify map loads with 18 cities
3. Click different city markers
4. Test city search
5. Check all charts render

## CI/CD Setup (Optional)

### GitHub Actions for Frontend

Create `.github/workflows/deploy-frontend.yml`:

```yaml
name: Deploy Frontend

on:
  push:
    branches: [main]
    paths:
      - 'frontend/**'

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
        with:
          node-version: '18'
      - name: Install and Build
        run: |
          cd frontend
          yarn install
          yarn build
      - name: Deploy to Vercel
        run: vercel --prod --token=${{ secrets.VERCEL_TOKEN }}
```

### GitHub Actions for Backend

Create `.github/workflows/deploy-backend.yml`:

```yaml
name: Deploy Backend

on:
  push:
    branches: [main]
    paths:
      - 'backend/**'

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-python@v2
        with:
          python-version: '3.11'
      - name: Deploy to Railway
        run: |
          # Railway auto-deploys from GitHub
          echo \"Backend will auto-deploy via Railway\"
```

## Custom Domain Setup

### Frontend (Vercel)
1. Go to Vercel Dashboard → Your Project → Settings → Domains
2. Add your custom domain (e.g., `dashboard.yourdomain.com`)
3. Update DNS records as instructed

### Backend (Railway)
1. Go to Railway Dashboard → Your Project → Settings
2. Add custom domain (e.g., `api.yourdomain.com`)
3. Update DNS records with provided CNAME

### Update CORS
After setting custom domains, update backend `.env`:
```env
CORS_ORIGINS=https://dashboard.yourdomain.com
```

## Monitoring & Analytics

### Backend Monitoring
- Railway provides built-in logs and metrics
- Add Sentry for error tracking (optional)

### Frontend Analytics
- Add Google Analytics (optional)
- Vercel provides built-in Web Analytics

## Troubleshooting

### Frontend Not Loading Data
- Check browser console for CORS errors
- Verify `REACT_APP_BACKEND_URL` is correct
- Ensure backend is running and accessible

### Backend Errors
- Check logs: `railway logs` or Render logs
- Verify MongoDB connection string
- Ensure all environment variables are set

### Map Not Rendering
- Check browser console for errors
- Verify `react-simple-maps` is installed
- Clear browser cache and reload

### Charts Not Showing
- Ensure API endpoints return data
- Check browser console for React errors
- Verify `recharts` library is installed

## Performance Optimization

### Frontend
```bash
# Build optimized production bundle
cd frontend
yarn build

# Analyze bundle size
npx source-map-explorer build/static/js/*.js
```

### Backend
- Enable gzip compression
- Add response caching for API endpoints
- Use MongoDB indexes for faster queries

## Security Checklist

- [ ] Environment variables not committed to Git
- [ ] CORS properly configured (not `*` in production)
- [ ] MongoDB credentials secured
- [ ] HTTPS enabled for both frontend and backend
- [ ] API rate limiting configured (optional)

## Cost Estimates

### Free Tier Deployment
- **Frontend (Vercel)**: Free for personal projects
- **Backend (Railway/Render)**: Free tier available (500 hrs/month)
- **MongoDB Atlas**: Free M0 cluster (512MB storage)
- **Total**: $0/month for small-scale usage

### Production Scale
- **Vercel Pro**: $20/month
- **Railway**: ~$5-20/month (usage-based)
- **MongoDB Atlas M10**: ~$57/month
- **Total**: ~$82-97/month

## Support

For issues or questions:
1. Check ARCHITECTURE.md for technical details
2. Open an issue on GitHub
3. Check logs on your deployment platform

---

**Ready to Deploy?** Follow the steps above and your dashboard will be live in minutes! 🚀
"