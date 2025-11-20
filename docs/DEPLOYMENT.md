# StoryWeaver AI - Deployment Guide

Complete guide for deploying StoryWeaver AI to production.

---

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Environment Setup](#environment-setup)
- [Database Setup](#database-setup)
- [Redis Setup](#redis-setup)
- [File Storage Setup](#file-storage-setup)
- [Deployment Options](#deployment-options)
  - [Vercel (Recommended)](#vercel-recommended)
  - [Railway](#railway)
  - [Render](#render)
  - [Docker](#docker)
- [Background Workers](#background-workers)
- [Domain & SSL](#domain--ssl)
- [Monitoring](#monitoring)
- [Backup Strategy](#backup-strategy)
- [CI/CD](#cicd)
- [Scaling](#scaling)
- [Troubleshooting](#troubleshooting)

---

## Overview

StoryWeaver AI deployment consists of several components:

1. **Frontend + API**: Next.js application (Vercel recommended)
2. **Database**: PostgreSQL (Supabase/Railway/Neon)
3. **Redis**: Cache and job queue (Upstash/Railway)
4. **Workers**: Background job processors (Railway/Render/Vercel Cron)
5. **Storage**: File storage (AWS S3/Cloudinary)

### Architecture

```
┌─────────────┐
│   Vercel    │ ← Next.js app (frontend + API)
└──────┬──────┘
       │
   ┌───┴───┐
   │       │
┌──▼──┐ ┌──▼────┐
│ PG  │ │ Redis │
└─────┘ └───┬───┘
            │
       ┌────▼────┐
       │ Workers │
       └─────────┘
```

---

## Prerequisites

### Required Accounts

- [x] GitHub account (for code repository)
- [x] Vercel account (for hosting)
- [x] Database hosting (Supabase/Railway/Neon)
- [x] Redis hosting (Upstash/Railway)
- [x] AWS account (for S3) OR Cloudinary account
- [x] Anthropic account (for Claude API)
- [x] Google Cloud Console (for OAuth)

### Required Tools

- Node.js 20+
- npm or yarn
- Git
- Prisma CLI (`npm install -g prisma`)

---

## Environment Setup

### 1. Environment Variables

Create a `.env.production` file:

```bash
# Database
DATABASE_URL="postgresql://user:password@host:5432/storyweaver?schema=public&pgbouncer=true&connection_limit=1"

# NextAuth.js
NEXTAUTH_URL="https://storyweaver.ai"
NEXTAUTH_SECRET="generate-with-openssl-rand-base64-32"

# Google OAuth
GOOGLE_CLIENT_ID="your-google-client-id"
GOOGLE_CLIENT_SECRET="your-google-client-secret"

# AI
ANTHROPIC_API_KEY="sk-ant-xxx"

# File Storage (AWS S3)
AWS_ACCESS_KEY_ID="your-aws-access-key"
AWS_SECRET_ACCESS_KEY="your-aws-secret-key"
AWS_REGION="us-east-1"
AWS_S3_BUCKET="storyweaver-production"

# Or Cloudinary
# CLOUDINARY_CLOUD_NAME="your-cloud-name"
# CLOUDINARY_API_KEY="your-api-key"
# CLOUDINARY_API_SECRET="your-api-secret"

# Redis
REDIS_URL="redis://:password@host:port"

# External Services
ASSEMBLY_AI_KEY="your-assemblyai-key"

# Monitoring
SENTRY_DSN="https://xxx@xxx.ingest.sentry.io/xxx"
NEXT_PUBLIC_POSTHOG_KEY="your-posthog-key"

# App
NEXT_PUBLIC_APP_URL="https://storyweaver.ai"
```

### 2. Google OAuth Setup

1. Go to [Google Cloud Console](https://console.cloud.google.com)
2. Create a new project
3. Enable Google+ API
4. Create OAuth 2.0 credentials
5. Add authorized redirect URIs:
   ```
   https://storyweaver.ai/api/auth/callback/google
   https://storyweaver.ai/api/auth/signin/google
   ```
6. Copy Client ID and Client Secret

### 3. Generate Secrets

```bash
# Generate NEXTAUTH_SECRET
openssl rand -base64 32

# Generate session encryption key
openssl rand -base64 32
```

---

## Database Setup

### Option 1: Supabase (Recommended)

**Pros**: Easy setup, includes backups, good free tier
**Cons**: Shared resources on free tier

1. **Create Project**
   ```
   1. Go to supabase.com
   2. Click "New Project"
   3. Choose region (closest to your users)
   4. Set strong database password
   5. Wait for project creation (~2 minutes)
   ```

2. **Get Connection String**
   ```
   Project Settings → Database → Connection string
   ```

   For Prisma, use the connection pooling URL:
   ```
   postgresql://postgres:[PASSWORD]@[HOST]:6543/postgres?pgbouncer=true&connection_limit=1
   ```

3. **Run Migrations**
   ```bash
   # Set DATABASE_URL
   export DATABASE_URL="your-supabase-url"

   # Run migrations
   npx prisma migrate deploy

   # Generate Prisma Client
   npx prisma generate
   ```

4. **Seed Database** (optional)
   ```bash
   npx prisma db seed
   ```

### Option 2: Railway

**Pros**: Simple, great free tier, automatic backups
**Cons**: Limited regions

1. **Create Database**
   ```
   1. Go to railway.app
   2. Click "New Project" → "Provision PostgreSQL"
   3. Copy connection string from Variables tab
   ```

2. **Run Migrations**
   ```bash
   export DATABASE_URL="your-railway-url"
   npx prisma migrate deploy
   ```

### Option 3: Neon

**Pros**: Serverless, autoscaling, generous free tier
**Cons**: Newer service

1. **Create Database**
   ```
   1. Go to neon.tech
   2. Create new project
   3. Copy connection string
   ```

2. **Configure Connection Pooling**
   ```
   Use the pooled connection string for Prisma
   ```

### Database Backups

#### Supabase
- Automatic daily backups (paid plans)
- Point-in-time recovery available

#### Railway
- Daily automatic backups
- Download backups from dashboard

#### Manual Backup
```bash
# Backup
pg_dump $DATABASE_URL > backup.sql

# Restore
psql $DATABASE_URL < backup.sql
```

---

## Redis Setup

### Option 1: Upstash (Recommended)

**Pros**: Serverless, pay-per-request, great free tier
**Cons**: Higher latency than self-hosted

1. **Create Database**
   ```
   1. Go to upstash.com
   2. Click "Create Database"
   3. Choose region (same as your app)
   4. Select "Redis"
   ```

2. **Get Connection URL**
   ```
   Copy the connection string from dashboard
   Format: redis://:password@host:port
   ```

### Option 2: Railway

1. **Create Redis**
   ```
   1. Go to railway.app
   2. Click "New" → "Database" → "Add Redis"
   3. Copy connection URL from Variables
   ```

### Option 3: Self-Hosted (Redis Cloud)

1. Go to [Redis Cloud](https://redis.com/cloud)
2. Create free database
3. Copy connection details

---

## File Storage Setup

### Option 1: AWS S3

1. **Create S3 Bucket**
   ```bash
   # Using AWS CLI
   aws s3 mb s3://storyweaver-production --region us-east-1

   # Or use AWS Console:
   1. Go to S3 service
   2. Create bucket
   3. Set region
   4. Enable versioning (recommended)
   5. Block public access (we'll use presigned URLs)
   ```

2. **Create IAM User**
   ```
   1. Go to IAM → Users → Create user
   2. Attach policy:
      - AmazonS3FullAccess (or create custom policy)
   3. Create access keys
   4. Save Access Key ID and Secret Access Key
   ```

3. **Configure CORS**
   ```json
   [
     {
       "AllowedHeaders": ["*"],
       "AllowedMethods": ["GET", "PUT", "POST", "DELETE"],
       "AllowedOrigins": ["https://storyweaver.ai"],
       "ExposeHeaders": ["ETag"]
     }
   ]
   ```

4. **Set Lifecycle Rules** (optional)
   ```
   - Delete incomplete multipart uploads after 7 days
   - Transition old versions to Glacier after 30 days
   ```

### Option 2: Cloudinary

1. **Create Account**
   ```
   1. Go to cloudinary.com
   2. Sign up for free account
   3. Note your cloud name, API key, and secret
   ```

2. **Configure Upload Presets**
   ```
   1. Go to Settings → Upload
   2. Create upload preset for each media type
   3. Set transformations as needed
   ```

---

## Deployment Options

## Vercel (Recommended)

**Pros**: Zero-config Next.js deployment, excellent DX, fast CDN
**Cons**: Function size limits, cold starts

### 1. Prepare Repository

```bash
# Ensure .gitignore includes
.env
.env.local
.env.production
.vercel
node_modules/
```

### 2. Deploy to Vercel

**Via CLI**:
```bash
# Install Vercel CLI
npm install -g vercel

# Login
vercel login

# Deploy
vercel

# Deploy to production
vercel --prod
```

**Via Dashboard**:
```
1. Go to vercel.com
2. Click "New Project"
3. Import from GitHub
4. Configure:
   - Framework: Next.js
   - Root Directory: ./
   - Build Command: npm run build
   - Output Directory: .next
5. Add environment variables
6. Deploy
```

### 3. Configure Environment Variables

In Vercel Dashboard:
```
Settings → Environment Variables → Add all variables from .env.production
```

### 4. Configure Domains

```
Settings → Domains → Add domain
Follow DNS instructions to point domain to Vercel
```

### 5. Prisma Setup for Vercel

Add to `package.json`:
```json
{
  "scripts": {
    "postinstall": "prisma generate",
    "build": "prisma generate && next build"
  }
}
```

Create `lib/prisma.ts`:
```typescript
import { PrismaClient } from '@prisma/client';

const globalForPrisma = global as unknown as { prisma: PrismaClient };

export const prisma =
  globalForPrisma.prisma ||
  new PrismaClient({
    log: process.env.NODE_ENV === 'development' ? ['query', 'error', 'warn'] : ['error'],
  });

if (process.env.NODE_ENV !== 'production') globalForPrisma.prisma = prisma;
```

### 6. Run Migrations

After first deployment:
```bash
# Connect to production database
export DATABASE_URL="your-production-url"

# Run migrations
npx prisma migrate deploy
```

---

## Railway

**Pros**: Supports both web and workers, simple setup
**Cons**: More expensive at scale

### 1. Deploy via Railway

```
1. Go to railway.app
2. Click "New Project" → "Deploy from GitHub"
3. Select repository
4. Add environment variables
5. Configure build:
   - Build Command: npm run build
   - Start Command: npm start
6. Deploy
```

### 2. Add Worker Service

```
1. In project, click "New Service"
2. Select same GitHub repo
3. Configure build:
   - Build Command: npm install
   - Start Command: npm run workers
4. Add same environment variables
5. Deploy
```

---

## Render

**Pros**: Simple, supports background workers
**Cons**: Slower cold starts

### 1. Create Web Service

```
1. Go to render.com
2. Click "New +" → "Web Service"
3. Connect GitHub repository
4. Configure:
   - Environment: Node
   - Build Command: npm install && npm run build && npx prisma migrate deploy
   - Start Command: npm start
5. Add environment variables
6. Create
```

### 2. Create Worker Service

```
1. Click "New +" → "Background Worker"
2. Connect same repository
3. Configure:
   - Build Command: npm install
   - Start Command: npm run workers
4. Add same environment variables
5. Create
```

---

## Docker

### Dockerfile

```dockerfile
FROM node:20-alpine AS base

# Install dependencies only when needed
FROM base AS deps
RUN apk add --no-cache libc6-compat
WORKDIR /app

COPY package*.json ./
COPY prisma ./prisma/
RUN npm ci
RUN npx prisma generate

# Rebuild source code only when needed
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .

RUN npm run build

# Production image
FROM base AS runner
WORKDIR /app

ENV NODE_ENV production

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

COPY --from=builder /app/public ./public
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static
COPY --from=builder /app/prisma ./prisma
COPY --from=builder /app/node_modules/.prisma ./node_modules/.prisma

USER nextjs

EXPOSE 3000

ENV PORT 3000

CMD ["node", "server.js"]
```

### docker-compose.yml

```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=${DATABASE_URL}
      - REDIS_URL=redis://redis:6379
      - NEXTAUTH_URL=${NEXTAUTH_URL}
      - NEXTAUTH_SECRET=${NEXTAUTH_SECRET}
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
    depends_on:
      - postgres
      - redis

  workers:
    build: .
    command: npm run workers
    environment:
      - DATABASE_URL=${DATABASE_URL}
      - REDIS_URL=redis://redis:6379
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
    depends_on:
      - postgres
      - redis

  postgres:
    image: postgres:15-alpine
    environment:
      - POSTGRES_USER=storyweaver
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=storyweaver
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:
```

### Deploy with Docker

```bash
# Build
docker-compose build

# Run migrations
docker-compose run app npx prisma migrate deploy

# Start
docker-compose up -d

# View logs
docker-compose logs -f
```

---

## Background Workers

### Option 1: Separate Service (Recommended)

Deploy workers as a separate service on Railway/Render:

**workers/index.ts**:
```typescript
import { anecdoteQueue, compilationQueue, exportQueue } from '@/lib/queue/jobs';
import { processAnecdote } from './process-anecdote';
import { refineAnecdote } from './refine-anecdote';
import { compileBook } from './compile-book';
import { exportDocument } from './export-document';

anecdoteQueue.process(async (job) => {
  return await processAnecdote(job.data);
});

compilationQueue.process(async (job) => {
  return await compileBook(job.data);
});

exportQueue.process(async (job) => {
  return await exportDocument(job.data);
});

console.log('Workers started');
```

**package.json**:
```json
{
  "scripts": {
    "workers": "tsx workers/index.ts"
  }
}
```

### Option 2: Vercel Cron Jobs

For simpler workloads, use Vercel Cron Jobs:

**vercel.json**:
```json
{
  "crons": [
    {
      "path": "/api/cron/process-queue",
      "schedule": "*/5 * * * *"
    }
  ]
}
```

**app/api/cron/process-queue/route.ts**:
```typescript
export async function GET(req: Request) {
  // Verify cron secret
  if (req.headers.get('authorization') !== `Bearer ${process.env.CRON_SECRET}`) {
    return new Response('Unauthorized', { status: 401 });
  }

  // Process waiting jobs
  const jobs = await getWaitingJobs();
  await Promise.all(jobs.map(processJob));

  return Response.json({ processed: jobs.length });
}
```

---

## Domain & SSL

### Configure Custom Domain

**Vercel**:
```
1. Go to Project Settings → Domains
2. Add your domain
3. Add DNS records as instructed
4. SSL certificate auto-provisioned
```

**Railway**:
```
1. Go to Settings → Networking
2. Click "Generate Domain" or add custom domain
3. Update DNS records
```

### DNS Configuration

**Example DNS Records**:
```
Type    Name    Value
A       @       76.76.21.21
CNAME   www     cname.vercel-dns.com
```

### Force HTTPS

Add to `next.config.js`:
```javascript
async headers() {
  return [
    {
      source: '/:path*',
      headers: [
        {
          key: 'Strict-Transport-Security',
          value: 'max-age=63072000; includeSubDomains; preload'
        }
      ]
    }
  ];
}
```

---

## Monitoring

### Error Tracking with Sentry

1. **Install Sentry**
   ```bash
   npm install @sentry/nextjs
   ```

2. **Initialize**
   ```bash
   npx @sentry/wizard@latest -i nextjs
   ```

3. **Configure** (`sentry.client.config.ts`):
   ```typescript
   import * as Sentry from '@sentry/nextjs';

   Sentry.init({
     dsn: process.env.SENTRY_DSN,
     tracesSampleRate: 0.1,
     environment: process.env.NODE_ENV,
   });
   ```

### Performance Monitoring

**Vercel Analytics**:
```bash
npm install @vercel/analytics
```

```typescript
// app/layout.tsx
import { Analytics } from '@vercel/analytics/react';

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Analytics />
      </body>
    </html>
  );
}
```

### Uptime Monitoring

Use services like:
- UptimeRobot (free)
- Pingdom
- StatusCake

Configure to ping:
```
https://storyweaver.ai/api/health
```

**Health Check Endpoint** (`app/api/health/route.ts`):
```typescript
export async function GET() {
  try {
    await prisma.$queryRaw`SELECT 1`;
    await redis.ping();

    return Response.json({
      status: 'healthy',
      timestamp: new Date().toISOString()
    });
  } catch (error) {
    return Response.json(
      { status: 'unhealthy', error: error.message },
      { status: 503 }
    );
  }
}
```

---

## Backup Strategy

### Database Backups

**Automated**:
```bash
# Daily backup script
#!/bin/bash
DATE=$(date +%Y-%m-%d)
pg_dump $DATABASE_URL | gzip > "backup-$DATE.sql.gz"

# Upload to S3
aws s3 cp "backup-$DATE.sql.gz" s3://storyweaver-backups/

# Keep last 30 days
find . -name "backup-*.sql.gz" -mtime +30 -delete
```

**Cron Job**:
```cron
0 2 * * * /path/to/backup.sh
```

### File Storage Backups

**S3 Versioning**:
- Enable versioning on S3 bucket
- Configure lifecycle rules to archive old versions

**Replication**:
- Set up S3 Cross-Region Replication
- Replicate to a bucket in different region

---

## CI/CD

### GitHub Actions

**.github/workflows/deploy.yml**:
```yaml
name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '20'

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test

      - name: Run linter
        run: npm run lint

      - name: Type check
        run: npm run type-check

      - name: Build
        run: npm run build

      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID}}
          vercel-args: '--prod'
```

---

## Scaling

### Database Scaling

**Vertical** (Supabase/Railway):
```
1. Go to project settings
2. Upgrade to larger instance
3. Apply changes (may have brief downtime)
```

**Horizontal** (Read Replicas):
```typescript
// lib/prisma.ts
const primaryDb = new PrismaClient({ datasources: { db: { url: PRIMARY_URL } } });
const replicaDb = new PrismaClient({ datasources: { db: { url: REPLICA_URL } } });

// Use replica for reads
const users = await replicaDb.user.findMany();

// Use primary for writes
await primaryDb.user.create({ data: { ... } });
```

### Redis Scaling

**Upstash**:
- Automatically scales
- Pay per request

**Self-hosted**:
- Upgrade to larger instance
- Or use Redis Cluster

### Worker Scaling

**Railway/Render**:
```
1. Go to service settings
2. Increase instance count
3. Deploy
```

**Docker**:
```bash
docker-compose up --scale workers=3
```

---

## Troubleshooting

### Common Issues

#### Database Connection Errors

**Issue**: `Can't reach database server`

**Solutions**:
- Check DATABASE_URL is correct
- Verify database is running
- Check firewall rules
- Ensure connection pooling configured

```bash
# Test connection
psql $DATABASE_URL -c "SELECT 1"
```

#### Prisma Migration Errors

**Issue**: `Migration failed to apply`

**Solutions**:
- Check for conflicting migrations
- Manually fix database
- Reset database (dev only)

```bash
# Check migration status
npx prisma migrate status

# Fix failed migration
npx prisma migrate resolve --applied <migration-name>

# Reset (dev only)
npx prisma migrate reset
```

#### Redis Connection Errors

**Issue**: `ECONNREFUSED`

**Solutions**:
- Check REDIS_URL
- Verify Redis is running
- Check Redis password

```bash
# Test connection
redis-cli -u $REDIS_URL ping
```

#### Out of Memory Errors

**Issue**: `JavaScript heap out of memory`

**Solutions**:
- Increase Node.js memory limit
- Optimize queries
- Add pagination

```json
{
  "scripts": {
    "start": "NODE_OPTIONS='--max-old-space-size=4096' next start"
  }
}
```

#### Build Failures

**Issue**: Build fails on Vercel

**Solutions**:
- Check build logs
- Ensure all dependencies in `package.json`
- Verify environment variables set

```bash
# Test build locally
npm run build
```

### Debug Mode

Enable debug logging:

```bash
# Next.js
DEBUG=* npm run dev

# Prisma
DEBUG="prisma:*" npm run dev

# Bull
DEBUG="bull:*" npm run workers
```

---

## Post-Deployment Checklist

- [ ] Database migrations applied
- [ ] Environment variables configured
- [ ] SSL certificate active
- [ ] Custom domain configured
- [ ] Background workers running
- [ ] Health check endpoint working
- [ ] Error tracking configured
- [ ] Monitoring set up
- [ ] Backups scheduled
- [ ] DNS records updated
- [ ] OAuth redirect URIs updated
- [ ] CORS configured
- [ ] Rate limiting enabled
- [ ] Security headers set
- [ ] Sitemap generated
- [ ] robots.txt configured

---

## Security Best Practices

### Environment Variables
- Never commit `.env` files
- Use secrets management (Vercel/Railway)
- Rotate secrets regularly

### Database
- Use strong passwords
- Enable SSL connections
- Restrict IP access
- Regular backups

### API
- Rate limiting on all endpoints
- Input validation
- CORS configuration
- CSRF protection

### Dependencies
- Regular updates
- Security audits

```bash
npm audit
npm audit fix
```

---

**Last Updated**: November 2025
**Version**: 2.0 (Node.js + Prisma + PostgreSQL)
