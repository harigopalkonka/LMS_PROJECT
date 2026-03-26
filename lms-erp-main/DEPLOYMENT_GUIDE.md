# Arohak LMS Deployment Guide

## Table of Contents
1. [Free Hosting Options](#free-hosting-options)
2. [Vercel Deployment](#vercel-deployment)
3. [Netlify Deployment](#netlify-deployment)
4. [Railway Deployment](#railway-deployment)
5. [Render Deployment](#render-deployment)
6. [Heroku Deployment](#heroku-deployment)
7. [DigitalOcean App Platform](#digitalocean-app-platform)
8. [Firebase Hosting](#firebase-hosting)
9. [Pre-deployment Checklist](#pre-deployment-checklist)
10. [Environment Configuration](#environment-configuration)

---

## Free Hosting Options

### Recommended Platforms for Full-Stack Apps

| Platform | Frontend | Backend | Database | Free Tier | Best For |
|-----------|----------|---------|-----------|-----------|----------|
| **Vercel** | ✅ | ❌ | ❌ | Excellent | Frontend-only |
| **Netlify** | ✅ | ❌ | ❌ | Excellent | Frontend-only |
| **Railway** | ✅ | ✅ | ✅ | Good | Full-stack |
| **Render** | ✅ | ✅ | ✅ | Good | Full-stack |
| **Heroku** | ✅ | ✅ | ❌ | Limited | Full-stack |
| **DigitalOcean** | ✅ | ✅ | ✅ | Good | Full-stack |
| **Firebase** | ✅ | ✅ | ✅ | Excellent | Full-stack |

---

## Vercel Deployment (Frontend Only)

### Prerequisites
- GitHub repository
- Vercel account (free)

### Steps

#### 1. Prepare Frontend
```bash
# In frontend directory
cd lms-erp/frontend
npm install
npm run build
```

#### 2. Configure Environment Variables
Create `.env.production`:
```env
NEXT_PUBLIC_API_URL=https://your-backend-url.com/api
NEXT_PUBLIC_FRONTEND_URL=https://your-domain.vercel.app
```

#### 3. Deploy to Vercel
1. Go to [vercel.com](https://vercel.com)
2. Click "New Project"
3. Import your GitHub repository
4. Select `lms-erp/frontend` as root directory
5. Configure settings:
   ```
   Framework Preset: Next.js
   Build Command: npm run build
   Output Directory: .next
   Install Command: npm install
   ```
6. Add environment variables
7. Click "Deploy"

#### 4. Update API Calls
In your frontend code, update API base URL:
```typescript
// lib/api.ts
const API_BASE_URL = process.env.NEXT_PUBLIC_API_URL || 'http://localhost:4000/api';
```

---

## Netlify Deployment (Frontend Only)

### Prerequisites
- GitHub repository
- Netlify account (free)

### Steps

#### 1. Static Export Configuration
Update `next.config.js`:
```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  output: 'export',
  trailingSlash: true,
  images: {
    unoptimized: true
  }
}
module.exports = nextConfig
```

#### 2. Build Command
```bash
cd lms-erp/frontend
npm run build
```

#### 3. Deploy to Netlify
1. Go to [netlify.com](https://netlify.com)
2. Click "Add new site" → "Import an existing project"
3. Connect to GitHub
4. Select repository
5. Build settings:
   ```
   Build command: npm run build
   Publish directory: out
   ```
6. Add environment variables
7. Click "Deploy site"

---

## Railway Deployment (Full-Stack)

### Prerequisites
- GitHub repository
- Railway account (free tier: $5/month credit)

### Steps

#### 1. Project Structure
Ensure your project has this structure:
```
lms-erp/
├── backend/
│   ├── package.json
│   ├── src/
│   └── .env
├── frontend/
│   ├── package.json
│   └── src/
└── package.json (root)
```

#### 2. Root package.json
Create root `package.json`:
```json
{
  "name": "lms-erp",
  "version": "1.0.0",
  "scripts": {
    "install:frontend": "cd frontend && npm install",
    "install:backend": "cd backend && npm install",
    "build:frontend": "cd frontend && npm run build",
    "build:backend": "cd backend && npm run build",
    "start:frontend": "cd frontend && npm start",
    "start:backend": "cd backend && npm start"
  }
}
```

#### 3. Railway Configuration
Create `railway.json`:
```json
{
  "build": {
    "builder": "NIXPACKS"
  },
  "deploy": {
    "startCommand": "cd backend && npm start",
    "healthcheckPath": "/api/health"
  }
}
```

#### 4. Deploy to Railway
1. Go to [railway.app](https://railway.app)
2. Click "New Project" → "Deploy from GitHub repo"
3. Select your repository
4. Railway will detect your Node.js app
5. Configure environment variables
6. Click "Deploy"

#### 5. Environment Variables
Add these in Railway dashboard:
```env
NODE_ENV=production
PORT=4000
DB_HOST=your-railway-database-host
DB_PORT=5432
DB_USER=your-db-user
DB_PASSWORD=your-db-password
DB_NAME=your-db-name
JWT_SECRET=your-jwt-secret
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USER=your-email@gmail.com
MAIL_PASS=your-app-password
MAIL_FROM=Arohak LMS <your-email@gmail.com>
FRONTEND_URL=https://your-app.railway.app
```

---

## Render Deployment (Full-Stack)

### Prerequisites
- GitHub repository
- Render account (free tier)

### Steps

#### 1. Backend Deployment
1. Go to [render.com](https://render.com)
2. Click "New" → "Web Service"
3. Connect GitHub repository
4. Configure backend service:
   ```
   Name: lms-erp-backend
   Environment: Node
   Build Command: cd backend && npm install
   Start Command: cd backend && npm start
   Instance Type: Free
   ```
5. Add environment variables
6. Click "Create Web Service"

#### 2. Frontend Deployment
1. Click "New" → "Static Site"
2. Configure frontend:
   ```
   Name: lms-erp-frontend
   Build Command: cd frontend && npm run build
   Publish Directory: frontend/out
   ```
3. Add environment variables
4. Click "Create Static Site"

#### 3. Environment Variables for Backend
```env
NODE_ENV=production
PORT=4000
DATABASE_URL=your-render-database-url
JWT_SECRET=your-jwt-secret
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USER=your-email@gmail.com
MAIL_PASS=your-app-password
MAIL_FROM=Arohak LMS <your-email@gmail.com>
FRONTEND_URL=https://your-frontend.onrender.com
```

---

## Heroku Deployment (Full-Stack)

### Prerequisites
- GitHub repository
- Heroku account (free tier limited)
- External database (MySQL)

### Steps

#### 1. Backend Deployment
1. Install Heroku CLI
2. Login: `heroku login`
3. Create app: `heroku create your-app-name`
4. Add buildpack: `heroku buildpacks:set heroku/nodejs`
5. Configure environment variables:
```bash
heroku config:set NODE_ENV=production
heroku config:set JWT_SECRET=your-jwt-secret
heroku config:set DATABASE_URL=your-mysql-database-url
heroku config:set MAIL_HOST=smtp.gmail.com
heroku config:set MAIL_PORT=587
heroku config:set MAIL_USER=your-email@gmail.com
heroku config:set MAIL_PASS=your-app-password
```

#### 2. Deploy Backend
```bash
git subtree push --prefix backend heroku main
```

#### 3. Frontend Deployment
Deploy frontend to Vercel/Netlify with backend URL

---

## DigitalOcean App Platform

### Prerequisites
- GitHub repository
- DigitalOcean account ($200 free credit)

### Steps

#### 1. App Configuration
1. Go to [cloud.digitalocean.com](https://cloud.digitalocean.com)
2. Click "Create" → "Apps"
3. Connect GitHub repository
4. Configure build:
   ```
   Build Command: cd backend && npm install && cd ../frontend && npm install && npm run build
   Run Command: cd backend && npm start
   HTTP Port: 4000
   ```

#### 2. Environment Variables
```env
NODE_ENV=production
PORT=4000
DATABASE_URL=your-digitalocean-database-url
JWT_SECRET=your-jwt-secret
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USER=your-email@gmail.com
MAIL_PASS=your-app-password
MAIL_FROM=Arohak LMS <your-email@gmail.com>
FRONTEND_URL=https://your-app.ondigitalocean.app
```

---

## Firebase Hosting (Full-Stack)

### Prerequisites
- Firebase account (free tier)
- Google Cloud account

### Steps

#### 1. Firebase Setup
1. Install Firebase CLI: `npm install -g firebase-tools`
2. Login: `firebase login`
3. Initialize: `firebase init`

#### 2. Firebase Functions (Backend)
```javascript
// functions/index.js
const functions = require('firebase-functions');
const express = require('express');
const cors = require('cors');
const app = express();

app.use(cors({ origin: true }));

// Import your backend routes
// TODO: Move your backend routes here

exports.api = functions.https.onRequest(app);
```

#### 3. Firebase Hosting (Frontend)
```json
// firebase.json
{
  "hosting": {
    "public": "frontend/out",
    "ignore": ["firebase.json", "**/.*", "**/node_modules/**"],
    "rewrites": [
      {
        "source": "/api/**",
        "function": "api"
      },
      {
        "source": "**",
        "destination": "/index.html"
      }
    ]
  },
  "functions": {
    "source": "functions"
  }
}
```

#### 4. Deploy
```bash
firebase deploy --only functions,hosting
```

---

## Pre-deployment Checklist

### ✅ Backend Preparation
- [ ] Update database configuration for production
- [ ] Set secure JWT secrets
- [ ] Configure email settings for production
- [ ] Add CORS for frontend domain
- [ ] Set NODE_ENV=production
- [ ] Add proper error logging
- [ ] Test all API endpoints

### ✅ Frontend Preparation
- [ ] Update API base URL for production
- [ ] Optimize images and assets
- [ ] Test responsive design
- [ ] Add proper error handling
- [ ] Test all user flows
- [ ] Add analytics (optional)

### ✅ Security Checks
- [ ] Remove development dependencies
- [ ] Add rate limiting
- [ ] Implement proper authentication
- [ ] Secure file uploads
- [ ] Add input validation
- [ ] Set up HTTPS

### ✅ Performance Optimization
- [ ] Optimize database queries
- [ ] Add caching where appropriate
- [ ] Compress images and assets
- [ ] Minimize JavaScript/CSS
- [ ] Add lazy loading

---

## Environment Configuration

### Production Environment Variables

#### Backend (.env.production)
```env
# Server Configuration
NODE_ENV=production
PORT=4000

# Database
DB_HOST=your-production-db-host
DB_PORT=3306
DB_USER=your-db-user
DB_PASSWORD=your-db-password
DB_NAME=your-db-name

# JWT
JWT_SECRET=your-super-secure-jwt-secret-key

# Email Service
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USER=your-email@gmail.com
MAIL_PASS=your-gmail-app-password
MAIL_FROM=Arohak LMS <your-email@gmail.com>

# Frontend URL
FRONTEND_URL=https://your-frontend-domain.com

# File Upload
UPLOAD_DIR=/uploads
MAX_FILE_SIZE=10485760
```

#### Frontend (.env.production)
```env
NEXT_PUBLIC_API_URL=https://your-backend-domain.com/api
NEXT_PUBLIC_FRONTEND_URL=https://your-frontend-domain.com
```

### Database Setup

#### MySQL (Recommended for Production)
```sql
-- Create production database
CREATE DATABASE lms_erp_prod CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- Create user with limited privileges
CREATE USER 'lms_user'@'%' IDENTIFIED BY 'secure_password';
GRANT SELECT, INSERT, UPDATE, DELETE ON lms_erp_prod.* TO 'lms_user'@'%';
FLUSH PRIVILEGES;
```

#### PostgreSQL (Alternative)
```sql
-- Create production database
CREATE DATABASE lms_erp_prod;

-- Create user
CREATE USER lms_user WITH PASSWORD 'secure_password';
GRANT ALL PRIVILEGES ON DATABASE lms_erp_prod TO lms_user;
```

---

## Deployment Scripts

### Automated Deploy Script
```bash
#!/bin/bash
# deploy.sh

echo "🚀 Starting deployment..."

# Test environment
if [ "$NODE_ENV" != "production" ]; then
    echo "❌ NODE_ENV must be production"
    exit 1
fi

# Install dependencies
echo "📦 Installing dependencies..."
npm ci --production

# Build frontend
echo "🔨 Building frontend..."
cd frontend && npm run build && cd ..

# Run database migrations
echo "🗄️ Running database migrations..."
cd backend && npm run migrate && cd ..

# Start application
echo "🌟 Starting application..."
npm start
```

### Health Check Script
```bash
#!/bin/bash
# health-check.sh

API_URL="https://your-domain.com/api/health"

# Check API health
response=$(curl -s -o /dev/null -w "%{http_code}" $API_URL)

if [ $response -eq 200 ]; then
    echo "✅ API is healthy"
else
    echo "❌ API is unhealthy (HTTP $response)"
    exit 1
fi
```

---

## Monitoring and Maintenance

### Recommended Monitoring Tools
- **Uptime Robot** - Free uptime monitoring
- **Google Analytics** - User behavior tracking
- **Sentry** - Error tracking (free tier available)
- **LogRocket** - Session replay (free tier available)

### Backup Strategy
- **Database**: Daily automated backups
- **Files**: Weekly backup of uploaded content
- **Code**: Git repository serves as backup

### Security Maintenance
- **Monthly**: Update dependencies
- **Quarterly**: Security audit
- **Annually**: Review access controls

---

## Cost Comparison

| Platform | Free Tier Limits | Paid Plans | Recommended For |
|----------|------------------|------------|------------------|
| **Railway** | $5/month credit, 500hrs | $20/month | Small teams |
| **Render** | 750hrs/month, free database | $7/month | Startups |
| **Heroku** | 550hrs/month, no database | $7/month | Hobby projects |
| **DigitalOcean** | $200 free credit (3 months) | $5/month | Production |
| **Firebase** | 10GB storage, 125K invocations | Pay-as-you-go | Mobile apps |

---

## Troubleshooting

### Common Deployment Issues

#### 1. Database Connection Errors
```bash
# Check database connectivity
mysql -h your-db-host -u your-user -p your-db

# Test connection string
mysql://user:pass@host:port/database
```

#### 2. CORS Issues
```javascript
// backend/src/main.ts
app.enableCors({
  origin: ['https://your-frontend-domain.com'],
  credentials: true
});
```

#### 3. File Upload Issues
```bash
# Check upload directory permissions
ls -la uploads/
chmod 755 uploads/
```

#### 4. Memory Issues
```bash
# Increase Node.js memory limit
node --max-old-space-size=2048 server.js
```

### Performance Optimization

#### 1. Database Indexing
```sql
-- Add indexes for better performance
CREATE INDEX idx_courses_published ON courses(is_published);
CREATE INDEX idx_enrollments_user ON enrollments(user_id);
CREATE INDEX idx_lessons_section ON lessons(section_id);
```

#### 2. Caching Strategy
```javascript
// Add Redis caching (optional)
import Redis from 'ioredis';
const redis = new Redis(process.env.REDIS_URL);
```

---

## Domain and SSL

### Custom Domain Setup
1. Purchase domain from provider (Namecheap, GoDaddy, etc.)
2. Point DNS to hosting provider
3. Configure SSL certificate (usually automatic)
4. Update environment variables

### SSL Certificate
Most platforms provide free SSL certificates:
- **Let's Encrypt** (free, auto-renewing)
- **Platform SSL** (included with hosting)

---

## Recommended Deployment Choice

### For Production: **Railway** or **Render**
- Full-stack support
- Managed databases
- Good free tiers
- Easy scaling

### For Development/Testing: **Vercel** + **Railway**
- Vercel for frontend
- Railway for backend
- Fast deployments
- Great developer experience

### For Budget-conscious: **DigitalOcean App Platform**
- $200 free credit
- Full control
- Good performance
- Scalable

---

*Choose the platform that best fits your requirements and budget. All options listed have working free tiers suitable for the Arohak LMS project.*
